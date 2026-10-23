# Feature Matrix

_Researcher. Last updated 2026-10-23 (R4). This matrix is built from [R1](2026-10-23-hunting-apps.md) and [R2](2026-10-23-group-and-memory-apps.md). The sources for each cell are in those reports. Keep this file current as new products are studied._

**Legend**
- **Y** = yes, documented
- **P** = partial or limited (see note)
- **N** = clearly not part of the product
- **—** = not found in any source checked (may exist, so treat as unverified)
- **Deer Camp** = the target per `docs/BRIEF.md`, not built yet

The matrix is split into two tables so they fit on screen. The rows are the same in both.

## Table 1: Hunting apps (R1)

| Feature | Deer Camp | onX Hunt | HuntStand | Spartan Forge | BaseMap | HuntWise |
|---|---|---|---|---|---|---|
| Trip/year page | Y | — | — | — | — | — |
| Roster (who made the trip) | Y | — | P (Group Hunt Area members) | — | P (friends you follow) | P (invited buddies on a hunt area) |
| Harvest board | Y | P (waypoints as a "hunt log") | P ("track sightings and harvests") | P (journal) | P (journal logs harvests) | — (community Log Feed only) |
| Stories wall | Y | — | — | — | P (journal, public/friends/private) | P (public Log Feed) |
| Camp awards | Y | — | — | — | — | — |
| Private pins (shared within the group only) | Y (camp-only by RLS) | P (private until shared. Collaborative Folders) | P (Group Hunt Areas) | P (owner can restrict waypoints in Blue Force Tracker) | P (sent by text, needs signal) | P (shared hunt area by invite) |
| Harvest linked to pin | Y | P (a harvest can be a waypoint) | — | — | P (journal plus SmartMarker) | — |
| Stand assignments / claims | Y | — | P (one club tried it for sign-in, then went back to paper) | — | — | — |
| Group chat | Y (text-only, queued) | — | — | — | — | — |
| Weather saved with harvest | Y | — | — | P (journal logs conditions) | P (SmartMarkers save weather on a waypoint) | — |
| Wind / weather forecast | Y | Y | Y (HuntZone 72 h) | Y (hourly, 10-day) | P (per waypoint) | Y (WindCast, HuntCast) |
| Moon phase | Y | — | — | Y (used in model) | — | Y (in HuntCast) |
| Offline support | Y (offline-first queue) | Y (paid. Share Nearby, same platform only) | Y (Pro) | Y (maps and forecasts) | Y (maps. Sharing needs signal) | P (1 area free. Reviews report failures) |
| Multi-group support | Y (camp per group) | P (multiple folders) | P (multiple hunt areas) | — | — | P (multiple hunt areas) |
| Invite method | Per-person link + QR (D-4 default) | Link by text or email. Bluetooth nearby | Share hunt area | Text pin. Blue Force Tracker | Text or email. Follow friends | "Invite" on a hunt area |
| Guests without an account | Y (D-7 default) | — | — | — | — | — |
| Price (yearly) | Free for one crew | Free / $34.99–$99.99 | Free / about $30–$99.99 | Free / about $40–$80 (unverified) | Free / $39.99 (+$9.99) | Free / $59.99–$119.99 |

## Table 2: Group-trip, club and memory apps (R2)

| Feature | Deer Camp | CampKeeper | BlindBook | VSIB | GroupMe | Wanderlog | FamilyAlbum | Day One Shared | Google Photos shared | iCloud Shared Albums |
|---|---|---|---|---|---|---|---|---|---|---|
| Trip/year page | Y | P (season stats) | P (season management) | — | N | P (trip itinerary) | P (yearly recap video, paid) | — | P (an album per trip) | P (an album per trip) |
| Roster | Y | Y (club members, dues) | Y (crew or club members) | Y | Y | Y | Y | Y (max 30) | P | P (max 100) |
| Harvest board | Y | Y (species, points, weight, photos) | Y (harvest log) | P (sightings and stand use) | N | N | N | N | N | N |
| Stories wall | Y | P (club feed, likes, comments) | P (shared crew log) | — | P (chat) | P (shared notes) | Y (photos, comments) | Y (entries, comments) | P (photos) | P (photos, comments) |
| Camp awards | Y | — | P (trophy collection, personal) | — | N | N | N | N | N | N |
| Private pins (shared within the group only) | Y | P (club map. Privacy label "Data Not Collected") | P (pins for blinds and stands) | — | N | N | N | N | N | N |
| Harvest linked to pin | Y | Y (stand filled in from check-in) | — | — | N | N | N | N | N | N |
| Stand assignments / claims | Y | Y (weekly AM/PM grid, auto-close overnight) | Y (booking, double-book warning) | Y (sign-in/out, 9 PM alert) | N | N | N | N | N | N |
| Group chat | Y | P ("Messaging and Chat" listed. Feed) | Y (BlindBook Connect) | — | Y | — | P (comments) | P (comments) | P (comments) | P (comments) |
| Weather saved with harvest | Y | — | Y (hunt-day weather, wind relative to blind) | P (shows wind and temperature at sign-in) | N | N | N | N | N | N |
| Wind / weather forecast | Y | — | Y (Pro: radar, wind) | P (current wind and temperature) | N | — | N | N | N | N |
| Moon phase | Y | — | — | — | N | N | N | N | N | N |
| Offline support | Y | Y (check-ins and harvests queue, then sync) | P (offline maps) | — | — | P (Pro only) | — | — | — | — |
| Multi-group support | Y | — | P (crew, club and outfitter plans) | — | Y | Y (many trips) | — | Y (many journals) | Y | Y |
| Invite method | Per-person link + QR | QR for guests. Member flow not documented | Admin invite, renewable. Printable QR | Not documented | Open share link or QR | "Invite friends" | Admin picks role, then QR | Owner invites | Account or link | Apple ID |
| Guests without an account | Y | Y (gate QR, no app) | — | P (sign in on a buddy's phone) | — | — | — | N (free account required) | Y (link view) | — |
| Price (yearly) | Free for one crew | Free ≤3 members / $119.99 club | Free / $9.99–$149.99 | About $5/person (2018) | Free | Free / about $39.99 Pro | Free / $59–$109 | Free member. Premium to create (unverified) | Free (storage limits) | Free (iCloud storage) |

## Takeaways
- **No product covers the trip/year page plus awards.** That is Deer Camp's clearest gap.
- **CampKeeper** is the only product that links a harvest to a stand and queues offline. It is the one to watch.
- **Group chat tied to stands** does not exist anywhere. Crews use GroupMe or text, separate from any map.
- **Weather saved with a harvest** exists only in BlindBook (waterfowl-first) and partly in BaseMap and Spartan Forge.

## Recommendations
_Ranked by value to the crew relative to effort to build. These are proposals for the PM and Jason, not decisions._

1. **Gap, high value, medium effort:** Build the trip/year page, awards and stories first, as the M2 plan already does. Nobody else offers them.
2. **Copy, high value, medium effort:** The CampKeeper pattern of a stand claim that pre-fills the harvest pin, plus a BlindBook-style weather snapshot on each harvest.
3. **Do better, high value, low-medium effort:** Put stand claims and chat on one live board that works offline. Today's tools split them across separate apps.
4. **Copy, high value, low effort:** Name-only guests and in-person QR invites.
5. **Avoid:** Open invite links (GroupMe) and paywalled offline maps. Offline is core for Deer Camp, not an upsell.
