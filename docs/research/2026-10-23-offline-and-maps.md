# R3: Offline, Sync, Maps and Weather Tech Scan

_Researcher, 2026-10-23. Milestone 1, task R3. Feeds decisions D-5 (maps) and D-6 (weather). Sources were checked on this date._

**Legend:** `[unverified]` = no source confirmed it. `[vendor]` = the source is the company itself or a competitor. `[old]` = the source is more than two years old.

## Summary
- **Offline is doable on iPhone, but the app has to do its own syncing.** Safari has **no Background Sync API**. Writes must be queued in IndexedDB and replayed when the app is open and back online.
- **Install the PWA to the Home Screen.** Installed web apps get their own storage clock, can ask for persistent storage, and are the only way to get web push on iOS.
- **Maps:** The library choice (Leaflet vs. MapLibre) matters less than **which tile source allows offline saving**.
  - **USGS National Map** (topo and NAIP imagery) is public domain and has no published limit on saving tiles. It is the safest US satellite and topo source to cache.
  - **OpenFreeMap** explicitly allows small offline downloads.
  - **OSM's own tile server forbids it.**
  - **Mapbox** limits offline use to its mobile SDKs. Web offline is unclear, so treat it as not allowed until confirmed.
- **Weather:** Both APIs have wind and moon data.
  - Open-Meteo is free with no key, but the free tier is **non-commercial only**.
  - OpenWeather's free plan allows **commercial use**, but One Call (where the moon data lives) needs a key and charges per call after 1,000 calls/day.

---

## 1. How outdoor apps handle no signal
- **Download maps ahead.** onX, HuntStand, HuntWise, BaseMap and Spartan Forge all charge for offline maps (see R1). BaseMap advises downloading on Wi-Fi, and larger areas get less detail ([Hunting Life](https://huntinglife.com/basemap-review) `[vendor]`).
- **Save locally, sync later.** CampKeeper says check-ins and harvests "save on the phone and sync once signal returns" ([App Store](https://apps.apple.com/app/id6792546790)).
- **Phone-to-phone sharing with no signal.**
  - onX Share Nearby works only iPhone to iPhone or Android to Android.
  - **Edits made after the share do not sync** ([onX offline sharing](https://www.onxmaps.com/hunt/app/features/offline-sharing)).
- **Forecasts cached for offline use.** Spartan Forge says its forecasts and predictions work without service ([Outdoor Life, 2021](https://www.outdoorlife.com/hunting/spartan-forge-hunting-app/) `[old]`).
- **Satellite messaging.**
  - **Garmin Messenger** sends over cell or Wi-Fi, or over satellite through a paired inReach device with a paid plan. Group threads can include SMS numbers ([Garmin manual](https://www8.garmin.com/manuals/webhelp/GUID-A494ACED-FFD6-481B-9DAF-D521E717FE5F/EN-US/GUID-BC0B9CB6-ED4B-4F23-93E8-4EAF4724F5E9.html)).
  - **iPhone Messages via satellite** works on iPhone 14 and later with iOS 18+. It is free for a period after activation. **Group chats are not supported** ([TidBITS, Oct 2024](https://tidbits.com/2024/10/10/ios-18s-messages-via-satellite-feature-is-magic/)).
  - Whether a web app can use satellite messaging is not documented. Assume it can't `[unverified]`.
- **Failure modes users hate:**
  - The app won't launch without internet, offline downloads stall, and tracks are erased when signal drops ([HuntWise reviews](https://apps.apple.com/app/id645518545)).
  - Offline maps disappear after an update ([HuntStand reviews, unitQ](https://unitq.com/unitq-scorecards/huntstand)).

## 2. What PWAs can't do on iPhone
| Topic | Status on iOS/Safari | Source |
|---|---|---|
| **Install** | iOS 26: "Add to Home Screen" makes a web app by default. A manifest is no longer required, but manifest icons are used if present. | [heise](https://heise.de/-10749652) (article date not shown) |
| **Background Sync API** | **Not supported** in Safari on iOS or macOS in any version. Not supported in Firefox either. | [caniuse](https://caniuse.com/background-sync) |
| **Background replay workaround** | Workbox's queue replays **when the service worker starts**, which means only while the app is open. It stores requests in IndexedDB. | [Workbox docs](https://developer.chrome.com/docs/workbox/modules/workbox-background-sync) |
| **Storage cleared (Safari tab)** | Intelligent Tracking Prevention deletes script-writable storage (IndexedDB, Cache, service worker) after **7 days of Safari use without visiting the site**. | [WebKit, 2020](https://webkit.org/blog/10218/full-third-party-cookie-blocking-and-more/) `[old, still referenced]` |
| **Storage cleared (installed app)** | Home Screen apps keep their own day count, which resets when the app is used. WebKit treats deletion of their first-party data as a bug. | [WebKit, 2020](https://webkit.org/blog/10218/full-third-party-cookie-blocking-and-more/) |
| **Quota and eviction (iOS 17+)** | Up to 60% of disk per origin in browser apps. Storage is "best-effort" by default, and under disk pressure the least-recently-used origin is evicted first. `navigator.storage.persist()` is supported, and installed Home Screen apps are more likely to be granted it. | [WebKit, 2023](https://webkit.org/blog/14403/updates-to-storage-policy/) |
| **Push notifications** | iOS 16.4+, **only for Home Screen web apps**. Permission must come from a user tap. The badge API is supported. | [WebKit, 2023](https://webkit.org/blog/13878/web-push-for-web-apps-on-ios-and-ipados/) |

**What this means for the build**
- Prompt the crew to **install to the Home Screen** during onboarding (G2 already covers this). Call `persist()` after install.
- Sync **on app open, on `online` events, and on a timer while open.** Never rely on sync running after the app is closed.
- Show a clear "saved on phone" vs. "sent to camp" status (G1 already plans this).

## 3. Queuing writes offline with Next.js and Supabase
| Approach | How it works | Pros | Cons |
|---|---|---|---|
| **A. Hand-rolled outbox in IndexedDB** (for example with Dexie) `[library choice unverified]` | Each write goes to a local `outbox` table with a client-generated UUID. A sync loop pushes to Supabase when online, using upserts so a retried write isn't saved twice. | Small, no extra service, fits Supabase RLS directly | We write retries and conflict handling ourselves |
| **B. Workbox background-sync** | The service worker catches failed `fetch` POSTs and replays them from IndexedDB ([Workbox](https://developer.chrome.com/docs/workbox/modules/workbox-background-sync)) | Little code | Replays raw HTTP requests. On iOS it replays only when the service worker starts. Auth tokens can expire while queued `[unverified risk]` |
| **C. TanStack Query persisted mutations** | Paused mutations are saved and later resumed with `resumePausedMutations()`. Needs `setMutationDefaults` so they work after a reload ([TanStack docs](https://tanstack.com/query/latest/docs/react/guides/mutations)) | Fits React data fetching | The "No mutationFn found" pitfall after reload. Persisted mutations are not a full local database |
| **D. PowerSync + Supabase** | Local SQLite on the device. Writes go to an upload queue, and `uploadData()` sends them to Supabase. Sync Streams mirror RLS ([PowerSync docs](https://docs.powersync.com/integration-guides/supabase-+-powersync)) | Full offline database, real-time sync, web SDK | Another service. The free cloud tier allows 2 GB/mo and 50 connections, and **deactivates after 1–2 weeks of inactivity** (a problem between seasons). Pro is from $49/mo. A free self-hosted edition exists ([PowerSync pricing](https://powersync.com/pricing.md)) |
| **E. RxDB Supabase replication** | Local database in IndexedDB. Pulls changes using `_modified` timestamps, uses `_deleted` soft deletes, and listens to Supabase Realtime ([RxDB](https://rxdb.info/replication-supabase.html)) | No extra server | Needs text primary keys and soft deletes. Licensing for premium storage not checked `[unverified]` |

**Schema implications for B2, whichever approach is chosen:** client-generated UUID keys, `updated_at`, soft-delete `deleted_at`, and idempotent upserts. The roadmap already asks for "offline-friendly keys."

## 4. Maps: Mapbox vs. Leaflet vs. MapLibre
### Libraries
| | Mapbox GL JS | Leaflet | MapLibre GL JS |
|---|---|---|---|
| License and cost | Proprietary. Free up to **50,000 map loads/mo**, then $5 per 1,000 ([Mapbox pricing](https://www.mapbox.com/pricing)) | Open source, about 42 KB. 2.0 is in alpha ([leafletjs.com](https://leafletjs.com/)) | BSD-3. A fork of Mapbox GL JS from before Mapbox's license change in Dec 2020 ([GitHub](https://github.com/maplibre/maplibre-gl-js)) |
| Tiles | Mapbox-hosted vector and raster | Raster tile layers and WMS | Vector, **raster XYZ**, raster-dem and GeoJSON ([style spec](https://maplibre.org/maplibre-style-spec/sources/)) |
| Key needed | Yes | No (depends on the tile source) | No (depends on the tile source) |
| Notes | A map load counts once per page init, and sessions last up to 12 h ([Mapbox docs](https://docs.mapbox.com/mapbox-gl-js/guides/pricing.md)) | Simple, mature | Smooth vector maps. Can read PMTiles files |

### Tile sources: free tier and offline terms
| Source | Type | Free tier | Offline / saving tiles allowed? |
|---|---|---|---|
| **USGS National Map** (USGSTopo, USGSImageryOnly, USGSImageryTopo) | Topo and NAIP satellite | Free ([USGS basemap list](https://www.usgs.gov/index.php/faqs/what-are-base-map-services-or-urls-used-national-map)) | **Likely yes.** USGS-produced data is US public domain, with credit requested ([USGS copyright](https://www.usgs.gov/information-policies-and-instructions/copyrights-and-credits)). The service page lists no use constraints. Rate limits are not published `[unverified]`. Imagery is mostly USDA NAIP ([item info](https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryTopo/MapServer/info/iteminfo)) |
| **OpenFreeMap** | OSM vector streets | Free, no key, no view limits, commercial use OK | **Yes, with limits:** up to 3,000 tiles or 50 MB per download and 4 parallel requests ([openfreemap.org](https://openfreemap.org/)). No satellite |
| **Protomaps / PMTiles** (self-hosted) | OSM vector, single file | Self-host on static storage. The hosted API is free for non-commercial use ([protomaps.com](https://protomaps.com/)) | Self-hosted files are ours to cache. OSM data license terms apply `[ODbL attribution unverified here]` |
| **OSM tile.openstreetmap.org** | OSM raster | Free, best-effort | **No.** Offline use and prefetching are banned ([OSM tile policy](https://operations.osmfoundation.org/policies/tiles/)) |
| **Mapbox** (satellite, outdoors) | Vector and raster | 50k web loads. Static Tiles 200k/mo. Raster Tiles 750k/mo ([pricing](https://www.mapbox.com/pricing)) | Offline is documented **only for the mobile SDKs** (750 tile-pack cap, and offline maps can't be shared or redistributed) ([Mapbox offline](https://docs.mapbox.com/android/maps/guides/offline/concepts.md)). **Web or service-worker offline caching is not confirmed.** The Product Terms PDF was not readable `[unverified, ask Mapbox]` |
| **MapTiler Cloud** | Vector, satellite, outdoor | Free: 5k sessions and 100k requests per month. **Non-commercial only.** Flex plan is $30/mo ([plans](https://www.maptiler.com/cloud/plans); [terms 1.2](https://www.maptiler.com/terms/cloud/)) | **Mostly no.** Only a temporary per-user cache is allowed. Bulk tile downloads are banned without written agreement ([terms 5.7, 6.3](https://www.maptiler.com/terms/cloud/)) |
| **Esri World Imagery** | Satellite | Needs an ArcGIS account and API key ([Esri Leaflet FAQ](https://developers.arcgis.com/esri-leaflet/faq)) | Only through the "World Imagery (for Export)" service: **organizational account**, 100,000-tile cap ([Esri support](https://support.esri.com/en/technical-article/000014855)) |

## 5. Weather: Open-Meteo vs. OpenWeather
| | Open-Meteo | OpenWeather |
|---|---|---|
| Cost | Free tier: <10,000 calls/day, 5,000/hr, 600/min, 300k/mo ([terms](https://open-meteo.com/en/terms)). Paid: Standard $29/mo, Professional $99/mo ([Open-Meteo, 2023](https://openmeteo.substack.com/p/api-subscriptions-for-commercial) `[price may be outdated]`) | Free plan: 60/min, 1M/mo for current conditions and a 5-day/3-hour forecast. **One Call:** 1,000 calls/day free, then $0.0015 per call ([pricing](https://openweathermap.org/price); [full price](https://m.openweathermap.org/full-price)) |
| Key | **No key** for free use ([docs](https://open-meteo.com/en/docs)) | Key required. Whether a card is needed for One Call is `[unverified]` |
| Commercial vs. non-commercial | **Free tier is non-commercial only.** Apps with subscriptions or ads count as commercial ([terms](https://open-meteo.com/en/terms)) | Self-service plans allow **commercial and non-commercial** use under ODbL, with **visible "Weather data © OpenWeather" attribution** ([full price](https://m.openweathermap.org/full-price)) |
| License | CC BY 4.0, attribution required ([terms](https://open-meteo.com/en/terms)) | ODbL ([full price](https://m.openweathermap.org/full-price)) |
| Wind | Hourly `wind_speed_10m`, `wind_direction_10m`, `wind_gusts_10m` ([docs](https://open-meteo.com/en/docs)) | `wind_speed`, `wind_deg`, `wind_gust` (current, hourly, daily) ([One Call](https://openweathermap.org/api/one-call-3)) |
| Moon | Daily moonrise, moonset and moon phase listed in the docs ([docs](https://open-meteo.com/en/docs)). Confirm with a test call `[verify]` | Daily `moon_phase`, `moonrise`, `moonset` (One Call only) ([One Call](https://openweathermap.org/api/one-call-3)) |
| Forecast length | Up to 16 days. Default is 7 ([docs](https://open-meteo.com/en/docs)) | 48 h hourly, 8 days daily ([One Call](https://openweathermap.org/api/one-call-3)) |

- **Fit for one crew:** One camp with about 10 stands, refreshed hourly, is about 240 calls/day. That is far under both free tiers.
- **Moon phase fallback:** If an API drops moon data, it can be calculated in the app with no network (the PM's D-6 note). We can't recommend a specific library yet `[unverified]`.

---

## Recommendations
_Ranked by value to the crew relative to effort to build. These are proposals for the PM and Jason, not decisions._

| # | Type | Recommendation | Value | Effort |
|---|---|---|---|---|
| 1 | Do better | **Use a hand-rolled IndexedDB outbox (approach A) for M2 and M3:** client UUIDs, idempotent upserts, and sync on open, on `online` events and on a timer. It's simple, free, and works with Safari's limits. Revisit PowerSync only if two-way conflicts become painful. Its free tier going inactive between seasons is a poor fit. | High | Medium |
| 2 | Copy | **Make "Install to Home Screen" part of onboarding.** It protects stored data from the 7-day rule, makes `persist()` more likely to be granted, and is the only path to push. | High | Low |
| 3 | For D-5 | **MapLibre GL JS with USGS Imagery and Topo raster tiles,** saved offline for the camp's area only (a small bounding box at zooms 12–17), with USGS credit shown. Add OpenFreeMap vector tiles for roads and labels if needed. **Avoid Mapbox for offline** until Mapbox confirms in writing that web caching is allowed. Leaflet also works with the same USGS tiles if the builder prefers it. | High | Medium |
| 4 | For D-6 | **Open-Meteo for now** (no key, wind, gusts, 16 days, moon listed). Calculate moon phase locally as a fallback. **If Deer Camp is ever sold or carries ads, switch to OpenWeather One Call** (commercial use allowed on the free 1,000 calls/day) or pay for Open-Meteo Standard. | High | Low |
| 5 | Do better | **Cache the forecast on the phone** and stamp each harvest from the cached forecast when offline, marked "forecast, not observed." Refresh after sync. | Medium-High | Low |
| 6 | Avoid | **OSM's standard tiles, MapTiler free, and Mapbox web tiles for offline caching.** Their terms ban it or don't clearly allow it. | High (compliance) | — |
| 7 | Avoid | **Relying on Background Sync or push to deliver chat while the app is closed** on iPhone. Design live chat as "sends when your app is open and has signal." | High (expectations) | — |
| 8 | Proposal for Jason | **Satellite messaging** (inReach, iPhone satellite) can't be reached from a web app `[unverified]`. Note it in the crew FAQ as a separate safety tool. Don't build for it. | Low | — |
