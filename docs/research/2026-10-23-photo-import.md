# R6: Photo import for past hunts (D-17)

Date: 2026-10-23. Scope: Next.js PWA + Supabase. Goal: upload many old photos, read date/GPS, group into past trips. Brief says photos upload later on better signal and the spot map is private (docs/BRIEF.md).

## 1. Does metadata survive the file picker?

### iPhone Safari / home-screen PWA
- Since iOS 16.4, Safari file uploads keep timestamps, camera settings and filenames, but GPS was stripped (both HEIC and "Most Compatible" captures). Source: [WebKit bug 207088](https://bugs.webkit.org/show_bug.cgi?id=207088) (user test reports, not Apple docs).
- GPS stripping since 16.4 confirmed in [WebKit bug 257534](https://bugs.webkit.org/show_bug.cgi?id=257534). WebKit's stated reason: a site's location permission does not mean the user agrees to share location inside photos.
- iOS 17+: the photo picker has an "Options" menu where the user chooses whether to share location. Per a WebKit engineer comment in [bug 257534](https://bugs.webkit.org/show_bug.cgi?id=257534). Bug closed "CONFIGURATION CHANGED". Exact UI wording and default (on/off) NOT verified; the user must toggle it each time, as far as reported. Test on Jason's phone.
- Picking from the library keeps metadata; taking a new photo via the camera option strips it again (iOS 17 test, [bug 207088](https://bugs.webkit.org/show_bug.cgi?id=207088)). Irrelevant for old photos.
- HEIC: Safari converts HEIC to JPEG on upload (reported in [bug 207088](https://bugs.webkit.org/show_bug.cgi?id=207088)). A 2022 report says that with `accept="image/jpeg"`, conversion dropped EXIF on iOS. Unverified for current iOS; use `accept="image/*"` and test. Apple developer thread on conversion: [forums](https://developer.apple.com/forums/thread/727526) (not read in detail).
- Home-screen (standalone) PWA behaves the same as Safari for the picker: UNVERIFIED, test.

### Android Chrome
- One source says Android strips GPS from photos given to browsers via a standard file input (sanitized copy), while Make/Model/DateTimeOriginal usually remain. Native apps need ACCESS_MEDIA_LOCATION; web pages cannot get it. Source: [technetexperts](https://www.technetexperts.com/fix-android-exif-data-upload/amp/) (secondary blog; treat as likely but verify on a real device).
- Same source suggests importing via cloud storage (Drive, Dropbox) keeps EXIF intact.

### Desktop browser
- Desktop file input hands over the raw file, so EXIF/GPS is intact. Evidence: the WebKit bugs describe stripping only on iOS; macOS kept EXIF in a 2022 report ([bug 207088](https://bugs.webkit.org/show_bug.cgi?id=207088)). Not independently verified for Windows/Chrome, but standard behavior (INFERENCE).
- Best path for bulk: Jason exports from Google Photos/iCloud/phone to a computer folder, then multi-selects on desktop.

## 2. Client-side EXIF reading
- exifr: runs in browser and Node, supports JPEG and HEIC (EXIF/TIFF/GPS segments; not XMP/IPTC), `exifr.gps(file)` returns latitude/longitude, has `lite` (12 KB gz) and `mini` (8 KB gz) bundles. Source: [exifr README](https://github.com/MikeKovarik/exifr).
- Performance: README claims ~3 ms per file in browser from ArrayBuffer (~7 ms Blob), phones ~4x slower (4-30 ms). 2,036 photos averaged 2.5 ms in its benchmark. Hundreds of photos is trivial. (Vendor benchmark.) Same README.
- Known gotcha: iPhone 17 HEIC files reportedly fail exifr's ftyp check, returning null GPS/date (indistinguishable from "no GPS"); fix is falling back to ExifReader. Source: [lilting.ch write-up](https://lilting.ch/articles/iphone-17-heic-exif-gps-browser-fallback) (single blog; unverified upstream status). Same article advises ignoring exact (0,0) coordinates.
- Practical: read only date (DateTimeOriginal) and GPS; read the first chunk of the file rather than the whole thing (exifr does chunked reads by default per README).
- Browsers other than Safari cannot render HEIC in `<img>`; desktop uploads of HEIC need conversion (e.g., heic2any/libheif WASM) for resizing. Unverified here; test. Prefer: read EXIF from the original, then resize.

## 3. Google Photos / iCloud export
- Google Takeout gives each photo a JSON sidecar with `photoTakenTime`, `creationTime`, `geoData`, `geoDataExif` (lat/lon/alt). Source: [PhotoStructure forum example](https://forum.photostructure.com/t/does-photostructure-ingest-google-photos-takeout-geodata/740). Takeout does not write this back into the image, so the sidecar is the only place some data lives (same thread).
- Sidecar naming is inconsistent (misnamed files, newer `supplemental-metadata` suffix): see [PhotoStructure on sidecar naming](https://forum.photostructure.com/t/creation-time-formatted-time-total-confusion-or-google-takeout-sidecar-files-are-misnamed/574) and [ExifTool forum](https://exiftool.org/forum/index.php?msg=90811). Matching photo to JSON is the hard part.
- Note: `photoTakenTime` is the real capture time; `creationTime` is upload time (per the naming-confusion thread above).
- iCloud export: Photos app "Export Unmodified Original" keeps EXIF (general Apple behavior; not sourced here, UNVERIFIED). Simplest instruction for Jason: export originals from Photos on Mac, or drag from Photos to a folder.
- Recommendation: v1 does not parse Takeout JSON. Add as optional v2 (accept .json files in the same multi-select and match by filename).

## 4. Clustering photos into trips
- Apple Photos has Trips/Moments and Google Photos has memories/trip albums; their algorithms are proprietary. Not found in public docs (UNVERIFIED). Apple's Photos help page: [support.apple.com](https://support.apple.com/guide/photos/phtacde28864/mac) (only describes user features).
- Generic spatiotemporal clustering is well understood (see [Alibaba IMM docs](https://help.aliyun.com/en/imm/user-guide/spatiotemporal-clustering-1) as one example; not read in depth).
- Hunting apps (onX, HuntStand, etc.) are not known to do photo-to-trip import (not checked in this report; see earlier competitor reports if any).
- Proposed simple heuristic (my design, untested):
  1. Sort by capture time.
  2. Start a new trip when gap to the previous photo is greater than ~36-48 hours (hunting trips run several days, with nights between).
  3. Optionally split/flag when GPS jumps more than ~50 km from the cluster centroid within the gap.
  4. Photos with no date: put in an "Unsorted" tray.
  5. Photos with date but no GPS: still join the time cluster.
  6. Trip location = median of GPS points (ignore outliers, ignore 0,0).
  7. Suggest year from the median date; Jason confirms or edits name, dates, and merges/splits clusters on one review screen.
- Because iOS strips GPS, time-only clustering must work; GPS is a bonus. Hunts are also seasonal (fall), so tune the gap by testing on Jason's real photos.

## 5. Privacy
- Read EXIF in the browser; GPS never needs to leave the device for reading ([exifr README](https://github.com/MikeKovarik/exifr) browser support; lilting article stresses in-browser parsing).
- Resizing through canvas re-encodes the image and drops EXIF by default (standard browser behavior; UNVERIFIED in a cited source, confirm in test). That gives GPS-free stored copies for free. Verify by running exifr on the output.
- Store: photo (stripped), capture date, and a trip-level pin chosen from the median GPS, which Jason can nudge or snap to an existing stand pin. Do not store per-photo coordinates unless Jason opts in (they reveal exact stands; BRIEF says the map is group-private). Storage buckets must be private with RLS per camp (BRIEF).
- Tell the user plainly: "Location is read on your phone/computer and removed from the saved photo."

## 6. Storage and upload costs (Supabase free)
- Free plan includes 1 GB storage; overage $0.0213/GB/month on paid plans. Source: [Supabase storage pricing](https://supabase.com/docs/guides/storage/pricing).
- Free plan max file size 50 MB per file. Source: [Supabase file limits](https://supabase.com/docs/guides/storage/uploads/file-limits).
- Image transformations (server resize): availability on Free NOT confirmed; do not depend on it. Resize on device instead.
- Arithmetic (estimates, not sourced): original iPhone photos about 2-4 MB, so 500 originals is 1-2 GB and would blow the free tier. Resized to 1600 px long edge at JPEG quality ~0.8 is roughly 250-400 KB, so 500 photos is about 125-200 MB. Add a ~400 px thumbnail (~30 KB).
- Include a storage meter for the camp (owner view) and a per-import cap (e.g., 300 photos per batch).
- Offline: queue uploads in IndexedDB and send on signal, matching the brief's "photos upload later" rule. Past-hunt import is a at-home task so offline is lower priority; keep the same queue to avoid a second code path.

## Recommendations (ranked: value to crew vs build effort)

1. Desktop-first bulk import, mobile as secondary (value high, effort low). Multi-file `<input type=file multiple accept="image/*">` plus drag-drop. Tell iPhone users to tap Options and turn on Location, but never rely on it.
2. Client-side EXIF with exifr (`lite`), fallback to ExifReader when result is null on HEIC (value high, effort low).
3. Resize + strip on device, upload only the stripped 1600 px JPEG and thumbnail (value high, effort medium). Keeps Supabase free tier viable and protects spots.
4. Time-gap clustering (36-48 h) with a review screen to rename, merge, split, and move photos (value high, effort medium). Build time-only first; add GPS jump splitting later.
5. Trip pin from median GPS, shown on the map as a suggested pin Jason confirms; if no GPS, ask him to drop a pin or pick an existing stand (value medium, effort low).
6. Manual fallback for undated or location-less photos: an "Unsorted" tray and "set date for selection" (value medium, effort low).
7. Takeout JSON sidecar support (value medium, effort medium-high because of filename matching). Defer to v2 and only if Jason uses Google Photos.
8. Android/Chrome GPS: do not promise; test on Jason's phones (effort low, important for expectations).

Copy: Apple/Google style "suggested trips" that the user confirms, not auto-published.
Do better: privacy-first (strip GPS, trip-level pin only), and link imported trips to the yearbook year pages and harvest board.
Gaps nobody fills (UNVERIFIED, based on no hunting app found offering it): turning an old camera roll into a hunting-crew yearbook.
Avoid: uploading originals; depending on Supabase image transforms; server-side EXIF reading (would send GPS to server); storing per-photo GPS by default.

## Open items to test before building
- iPhone (Jason's iOS version): Safari vs home-screen PWA, Options > Location on/off, HEIC to JPEG, is date kept.
- Android Chrome: does GPS survive.
- Does canvas resize drop EXIF in Safari and Chrome.
- iPhone 17 HEIC with current exifr.
