# Roadmap

_Maintained by the project-manager agent. Last updated 2026-10-23 (model change D-13 to D-17, R6 folded in)._

**End goal (from `docs/BRIEF.md`):** a private, offline-first yearbook app for one crew, built so other crews can join later.
**Non-negotiables:** group data (especially pins) private to that group; works on spotty signal (PWA); each group isolated from day one; unshared hunt details private to the party.

---

## Model change (D-13 to D-16): group → hunts → party

**In plain words**
- **Group** = the whole crew (today's "camp"). Members, invites, roles and pins live here. Unchanged.
- **Hunt** = one trip. Many per group, several per year allowed. Replaces "one trip year per camp per year."
- **Party** = who went on that hunt: group members plus name-only guests. Replaces the year roster.
- **Visibility:** party members see and add everything on their hunt. Other group members see only the **summary** until a party member shares the hunt. Shared hunts are fully readable by the whole group.
- **Pins** stay at group level; any hunt's harvests and stand assignments can point at them.

**Proposed summary (Q1):** hunt name, start/end dates, area name, party names (members and guests), harvest count, shared yes/no. **Not** in the summary: cover photo, coordinates, photos, stories, harvest details, awards, pins used, stand assignments, chat.

**What it means for the schema on `feature/m1-foundation`** (B4 done there; no real data exists yet, so this is a rename-and-reshape, not a data migration)
| Today (branch) | Becomes |
|---|---|
| `trip_years` (unique `year` per camp) | `hunts`: no per-year uniqueness; `name` required; `starts_on` required, `ends_on` optional; `location_name` (area); `location_lat/lng` (party-only); `cover_photo_path`; `shared_at`, `shared_by`; `created_by` |
| `year_attendees` (roster) | `hunt_party`: member *or* guest name per hunt, same rules as today |
| `harvests`, `posts`, `awards` → `trip_year_id`; `hunter/recipient_attendee_id` | → `hunt_id`; → party row of the same hunt (composite FKs as today) |
| `messages` (camp-wide chat) | add required `hunt_id`: chat belongs to a hunt (D-14) |
| `pin_assignments` (camp-wide) | add required `hunt_id`; keep the group-wide no-double-booking indexes so two parties can't hold the same stand at once |
| `pins` | unchanged (group-level, D-16) |
| RLS "any member sees all camp data" | group-level tables as today; hunt content readable only by the party, or by the whole group once shared; summary for everyone via one read-only function |
| Storage path `camp/<camp_id>/...` | hunt files under `camp/<camp_id>/hunts/<hunt_id>/...`; storage policies must apply the same party-or-shared rule (B10) |

**Naming:** the user-facing names for the two levels ("group" vs. "camp", "hunt" vs. "trip") are **open; Jason is choosing**. This doc uses group/hunt as working names. Whatever he picks, the database keeps `camps` / `camp_id` (`group` is an SQL keyword and a rename adds risk); the table for trips is named after his choice in B4b.

**Design first (Jason's direction, 2026-10-23):** builder tasks from B4b on are **on hold until Jason approves the G4 designs** (updated specs and Claude Design screens). Research and design continue.

**Adopted defaults (PM, follow from D-13 to D-16; Jason can object)**
- New hunts start **unshared** (party only). Sharing is a deliberate act.
- Only party members add people to a party. A non-party member can't add himself (that would bypass D-14).
- Any party member can edit the hunt's details and manage its party. A party keeps at least one member account.
- On a shared hunt, non-party members can read everything but don't post (they weren't there). Party members post.
- Sharing reveals photos, stories, harvests, awards, stand use and chat. The share confirm says so. Unsharing hides it from others' phones at their next sync (cached copies are purged then); the confirm says that too.
- No hunt delete in M2a (G3 Q4 default). The import review step lets the user discard suggested hunts before they're created.

---

## Milestone 1: Foundation — Done, with follow-ups

**Exit criteria check:**
- [x] Research reports and feature matrix in `docs/research/`, each with ranked Recommendations.
- [x] `docs/design/system.md` and `docs/design/onboarding.md` exist; PM reviewed them against the brief (this update).
- [x] Next.js PWA scaffold; builder reports lint, typecheck, build pass. PM re-ran `npm test`: 379/379 pass.
- [x] Migrations for all M1 tables; RLS on every public table; isolation tests cover every camp-data table, plus a catalog test that fails if RLS is off.
- [x] D-1 to D-4 answered (D-1 still needs Jason to switch the repo to private).

**Not yet merged.** The build lives on branch `feature/m1-foundation` (worktree, 4 commits, not pushed). Merge and push only after the repo is private (D-1). On merge, keep `main`'s `docs/ROADMAP.md` and `docs/DECISIONS.md`; the branch has older copies of both.

| ID | Task | Owner | Status | PM review |
|---|---|---|---|---|
| R1 | Hunting app teardown | researcher | Done | All 5 apps, standard fields, sourced, ranked recs. |
| R2 | Group-trip and shared-memory app scan | researcher | Done | 5 club/trip tools, 4 memory apps, invite section, ranked recs. |
| R3 | Offline, sync, and maps tech scan | researcher | Done, with follow-ups | Strong on PWA limits, offline queue options, tile terms, weather. Did not cover Supabase auth-email limits or confirm the iPhone Safari vs. home-screen sign-in split (see R5). |
| R4 | Feature matrix | researcher | Done, with follow-up | Complete. Deer Camp column lists "link + QR" invites; QR is a proposal, not decided (fix wording in R5). |
| G1 | Starter design system | designer | Done | Meets every acceptance item: both themes with contrast, type, spacing, 48px targets, all core components, Lucide, "saved on phone / sent to camp" pattern. |
| G2 | Sign-in and join-a-camp flow spec | designer | Done | All 7 flows with states and copy; PWA install for iPhone and Android; invite rules; D-2 alternatives; schema needs. Open questions routed below. |
| B1 | Scaffold Next.js PWA | builder | Done | Manifest, hand-written service worker, offline page, `.env.example` placeholders, README dev section. |
| B2 | Schema migrations with per-camp RLS | builder | Done, with follow-ups | Solid: composite FKs block cross-camp links, immutable `camp_id`, hashed tokens, owner-only admin invites, guests on roster, weather snapshot check, soft deletes, fake seed coordinates. Gaps vs. G2 listed in B4. |
| B3 | Camp-isolation test suite | builder | Done | 379 tests on a throwaway Postgres 16 with a test-only auth stub. Not yet run against real Supabase (B5). |

### Design vs. schema reconciliation (G2 "Schema needs" vs. B2)
| G2 need | B2 today | Result |
|---|---|---|
| Invite preview while signed out (`preview_invite`) | Missing. `anon` has no access at all | **Gap.** Blocks the main join path (flow 4a). B4. |
| Owner-only admin invites | Enforced in `create_invite` and tested | Match. Adopted as the default (fits D-3). |
| Already-member tap doesn't use up the invite | Returns the camp id, invite not used | Match on behavior, but the app can't tell "joined" from "already in" to show the right screen. B4. |
| Invitee name stored, prefills display name | Optional `label`, not returned to the invitee | **Gap.** B4. |
| Hash-only token, nobody reads the hash | SHA-256 only; but admins can `select` the `token_hash` column | Low risk (hash can't be reversed). Tighten anyway. B4. |
| Specific redeem results | Exceptions `invite_invalid/revoked/expired/used` | Workable; return a status instead so `already_member` is distinct. B4. |
| Single use (D-4) | `max_uses` 1–100 allowed, default 1 | Schema allows shared links, which D-4 rules out. Cap at 1. B4. |
| `used_at/used_by`, `revoked_by`, `camp_members.invited_by` | Only `use_count` | Add, so the owner can see who joined on which link. B4. |
| Name lengths: display 1–30, camp 2–40 | display 1–60, camp 1–80 | Align to the design. B4. |

### M1 follow-up tasks
| ID | Task | Owner | Depends on | Status |
|---|---|---|---|---|
| B4 | **Invite and profile schema follow-ups** (table above): add `preview_invite(token)` callable signed out, returning only camp name, inviter display name, role, expiry and status; `redeem_invite` returns `ok / already_member` and stable error codes; required `invitee_name` returned by preview; `used_at/used_by/revoked_by`; `camp_members.invited_by/invite_id`; cap `max_uses` at 1; column grants so no API role can read `token_hash`; align name lengths. Tests for each, including anon can call only `preview_invite` and gets nothing else. | builder | none | **Done on branch.** Branch `docs/schema.md` matches every item. PM test re-run pending. |
| R5 | **Short verification scan:** (a) Supabase built-in email limits and free custom SMTP options (e.g. Resend) for 6-digit codes; (b) current iOS behavior: does a home-screen web app share Safari's sign-in and storage? (c) a test Open-Meteo call confirming moon fields; (d) fix the QR wording in the feature matrix. | researcher | none | **Done** (`docs/research/2026-10-23-verification.md`). Built-in email only reaches Supabase team members, about 2/hour: custom SMTP is needed before buddies test (D-10 revisit). Open-Meteo moon parameter names still need one live call (builder, B5). |
| B4b | **Hunts and parties schema (D-13 to D-16).** On `feature/m1-foundation`, before B5 applies anything to Supabase. Reshape per the "Model change" table: `trip_years` → `hunts`, `year_attendees` → `hunt_party`, `hunt_id` on harvests, posts, awards, messages and pin assignments; `shared_at/shared_by`; helpers `is_hunt_party(hunt_id)` and `can_read_hunt(hunt_id)` (party, or shared); `share_hunt(hunt_id)` / `unshare_hunt(hunt_id)` RPCs, idempotent and offline-retry safe; `hunt_summaries(camp_id)` read-only function returning only the summary fields (Q1); update `docs/schema.md` and the "Who can do what" table. Create-hunt rights per Q2, unshare per Q3, owner access per Q4; build to the PM recommendations and keep each a one-line policy change. **Acceptance (tests):** (1) a non-party group member gets **no rows** from `hunts`, `hunt_party`, harvests, harvest photos, posts, post photos, awards, messages and pin assignments of an unshared hunt, and gets exactly the summary fields from `hunt_summaries` (no coordinates, cover, or ids beyond the hunt id); (2) party members read and write everything on their hunt; (3) any party member can share and unshare, a non-party member can't, and after unshare non-party reads return nothing again; (4) shared hunts are fully readable but not writable by non-party members; (5) a non-party member can't add himself to a party; (6) the last member account can't be removed from a party; (7) pins stay readable by every group member and any hunt's harvest or assignment can reference them; a pin from another group is refused; (8) two hunts can't hold the same pin in overlapping sessions; (9) every existing cross-group isolation test still passes, rewritten for hunts; (10) removing a member from the group turns their party rows into guest names (as today); (11) if that leaves a hunt with no member account, the group owner is added to its party (Q4) so the hunt is never locked away. | builder | Jason approves G4 designs; Q1-Q4; naming choice | **On hold** (design first) |

### Adopted defaults (PM, inside approved scope; no Jason decision needed unless he objects)
- Only the owner can invite admins; admins invite members only (fits D-3).
- Offline queue approach: hand-rolled IndexedDB outbox with client UUIDs and idempotent upserts (R3 rec 1). No extra sync service.
- Install-to-home-screen is part of onboarding (R3 rec 2, G2 flow 5).
- iPhone order stays: join in Safari, then install, then sign in once more inside the app. R3 confirms installed web apps keep their own storage; R5(b) confirms the sign-in split; a real-phone test (T1) checks the 2-minute goal.
- Never offer a public link for pins (R1 rec 1). Removing a member keeps their harvests and stories (R2 rec 10, already in B2).

---

## Milestone 2: Group Home and Hunt Page

**Goal:** the crew can sign in, Jason can create the group and invite everyone, and the group home lists hunts; each hunt page shows name, dates, cover, area and party, readable with no signal.

Split so the crew can get in before the season:
- **M2a: Get the crew in + group home and hunt page basics** (planned below). Re-sequenced for the model change: B4b goes **before** B5, so the schema applied to Supabase is already hunts-based and nothing needs migrating later. G4 replaces G3's year-based screens. Sign-in, invites and install (B5 to B9) barely change, but per Jason's design-first direction **all builder tasks wait for his approval of the G4 designs**. Order once approved: B4b → B5 → B6 to B10 → B11 → B12 → T1.
- **M2b: Yearbook content:** harvest board with weather snapshot, stories wall, awards, background photo upload, offline outbox for posts, all per hunt. Planned after M2a review.
- Past hunts can be added **by hand** in M2a (a hunt with past dates). Photo import comes later (M4).

**M2a exit criteria:**
- Jason and at least one buddy (one iPhone, one Android) go from invite text to inside the group in under 2 minutes, on real phones (T1).
- Cold start in airplane mode shows the group home, and the party of every hunt the user can read, from the phone (R1 rec 3).
- A non-party member sees only the summary of an unshared hunt, in the app and in the API; after a party member shares it, he sees the full hunt; after unshare, it's gone from his phone at the next sync.
- Isolation and party-visibility tests pass against the real Supabase setup, not only the local stub.
- Photo storage policies keep each group's files private, and unshared hunts' files private to the party, with tests.

| ID | Task | Owner | Depends on | Status |
|---|---|---|---|---|
| J1 | Jason actions (below): repo private, Supabase project, Vercel, email sender | Jason | none | Not started |
| B5 | **Wire up Supabase and Vercel:** apply migrations; email OTP template with the 6-digit `{{ .Token }}`, code life ≤1 hour, 60s resend; session settings from G2 flow 6 (no time-box, no inactivity timeout); custom SMTP once D-10 is revisited; run the DB tests against Supabase (local CLI or the project) and prove anon can only call `preview_invite`; one live Open-Meteo call to confirm moon parameter names (R5 c); preview deploy on Vercel. | builder | J1, B4, **B4b** | On hold (design first) |
| G3 | **Trip page and camp home spec** | designer | none | **Done** (`docs/design/trip-page.md`), now superseded in part by the model change. Camp picker, "Ready for camp", Crew screen and Me tab stand; year screens are reworked in G4. |
| G4 | **Hunts update to the specs and Claude Design screens.** Update `docs/design/trip-page.md` (rename or split as needed) and the wording in `docs/design/onboarding.md` to Jason's chosen names for the two levels. Screens: (a) **group home**: all hunts, newest first, grouped by season; full cards for hunts I was on or that are shared, summary cards for the rest, each clearly marked; "Add a hunt"; (b) **hunt page** with party (members and guests), cover, area, dates, M2b slots; (c) **create/edit hunt**, including past dates, and a "Who went?" party picker (default: only the creator checked, not the whole group); (d) **share-to-group control** and **unshare**, with confirm copy that lists what sharing reveals (photos, stories, harvests, awards, stand use, chat) and that unsharing removes it from others' phones at their next sync; (e) the **summary view** a non-party member sees when opening an unshared hunt, with the party named and no dead ends; (f) "Ready for camp" "saved" row updated for hunts. Empty, loading, offline and error states for each. Revisit G3 open questions 1, 4 and 6 in light of parties. Acceptance: every screen above in the spec and in Claude Design; summary card shows exactly the Q1 fields; privacy badge says who can see each hunt ("Party only" / "Whole group"); role-gated and party-gated controls hidden, not disabled. M2b/M3 sections on the hunt page (live hunt entry, stands today, harvest board with weather, stories, awards) are fine to show as placeholders or future states, labeled as later milestones. | designer | Q1-Q4 and the naming choice (can start on recommendations and working names) | **In progress.** A "Hunt page" screen (party, share control, live hunt entry, stands today, harvest board with weather, stories, awards) is on the Claude Design canvas; spec updates and the other screens pending. |
| B6 | **Sign-in screens** (G2 flows 1 and 7) and **stay signed in offline** (flow 6 basics: never sign out on network error). | builder | B5 | On hold (design first) |
| B7 | **Create a group, no-group screen, group picker** (flow 2). | builder | B6 | On hold (design first) |
| B8 | **Invites:** create, share sheet / text / copy, "Waiting to join" list, cancel; join preview signed out and signed in; all error screens (flows 3 and 4). | builder | B4, B6, B7 | On hold (design first) |
| B9 | **Install the app** card and iPhone/Android steps, `persist()` after install, standalone "sign in once more" screen (flow 5). | builder | B6 | On hold (design first) |
| B10 | **Photo storage:** private bucket, policies that mirror `camp/<camp_id>/` paths, and for `camp/<camp_id>/hunts/<hunt_id>/` the party-or-shared rule from B4b. Tests: another group and anon can't read or write; a non-party member can't read an unshared hunt's files by path, and can after it's shared. On-device resize (about 1600px long edge) and EXIF strip for every upload, verified by reading the stored file's metadata in a test. | builder | B5, B4b | On hold (design first) |
| B11 | **Group home and hunt page basics** (G4): group home with full and summary cards, create/edit hunt (past dates allowed), cover photo (upload on signal), area name, party with members and name-only guests (D-7), share/unshare control. | builder | G4 approved, B7, B10 | On hold (design first) |
| B12 | **Offline read cache:** cache the group home (summaries included) and every hunt the user can read after each online load; purge cached details of hunts the user can no longer read (unshared, or removed from the party); offline banner with "updated {time}"; airplane-mode cold-start test. | builder | B11 | On hold (design first) |
| T1 | **Real-phone onboarding test:** Jason + one buddy, iPhone and Android, timed from text to camp home; log problems as tasks. | Jason, PM | B8, B9 | Not started |

### Out of scope for M2a
- Harvest board, stories, awards, chat, map, weather (M2b and M3).
- Photo import (M4).
- Hunt delete (adopted default above).
- Anything in the proposals list that Jason hasn't approved.

### Notes for later milestones
- **M3 stand board sync:** `pin_assignments` uses hard deletes. Before building live stand claims, decide how other phones learn a claim was dropped (tombstones or full refresh).
- **M3 stand board across parties:** if two hunts overlap, a pin held by another party shows as "Taken" with no names or hunt details to non-party members.
- **M3 pin history:** "which spots produce over the years" counts only harvests the viewer can read; unshared hunts add nothing to a non-party member's pin history. Note it in the M3 spec.
- **M3 chat:** one chat per hunt (B4b), party only unless shared.
- **Rate limiting** for the signed-out `preview_invite` call should sit at the Supabase/Vercel edge; tokens are 244-bit, so guessing is not practical.

---

## Milestone 3: Map, Stand Assignments, Live Hunt, Weather
_To be planned after M2._ Needs D-5 and D-6. Chat is "sends when the app is open and has signal" on iPhone (R3 rec 7). Chat and stand assignments are per hunt (B4b).

---

## Milestone 4: Photo Import for Past Hunts (D-17) — PARKED (D-19)

**Jason moved photo import to the potential ideas list on 2026-10-23.** Nothing below is scheduled; T2, G5 and B30–B33 are parked. The plan is kept here so it's ready if Jason picks it back up.

**Goal:** Jason (or any party member) turns a folder of old hunting photos into past hunts in one sitting: suggested trips with dates and area, confirmed on one review screen, photos stored small and location-free.

**Timing (Q5):** recommended as its own milestone **after M2b** (imported photos land on the stories wall, which M2b builds) and as an off-season task, so it never delays getting the crew in or the in-season features. Manual "add a past hunt" in M2a covers the gap. Design (G5) and the real-device metadata test (T2) don't need the builder and can run during the design-first phase.

**Resolving "strip GPS from uploaded photos" vs. "read GPS to place the hunt"** (PM, inside the privacy non-negotiable)
- GPS and date are read **on the device, in the browser** during import (exifr, with ExifReader fallback for iPhone 17 HEIC). Nothing with GPS is ever uploaded.
- Each photo is resized on the device (about 1600px long edge plus a ~400px thumbnail) and re-encoded without EXIF before upload. Originals are never stored. A test reads the stored file to prove no GPS remains.
- **Stored:** the stripped photo, its capture date, and **one hunt-level location** (median of the cluster's GPS points, ignoring 0,0), saved as the hunt's party-only coordinates. The user can snap it to an existing group pin. Creating a new pin from it follows D-11 (owner/admin); for others it stays a hunt location.
- **Never stored:** per-photo coordinates.
- Copy at import: "Location is read on this device and removed from the saved photos."

**What R6 found** (`docs/research/2026-10-23-photo-import.md`)
- iPhone Safari (and likely the home-screen app) **strips GPS** from picked photos since iOS 16.4; the date is kept. iOS 17+ may let the user turn location on under "Options" in the picker (wording and default unverified).
- Android Chrome likely strips GPS too (single secondary source, unverified).
- Desktop browsers pass the original file with EXIF intact. **So import is desktop-first**, with phone import as a secondary path that works on dates alone.
- Time-gap clustering (new trip after a ~36-48 hour gap) works without GPS; GPS adds an optional >50 km split and the suggested location.
- Storage: Supabase free tier is 1 GB. About 500 resized photos ≈ 125-200 MB; 500 originals would be 1-2 GB. Hence on-device resize, a per-import cap (about 300 photos), and an owner storage meter.
- Google Takeout JSON sidecars: deferred (v2, only if Jason uses Google Photos).

| ID | Task | Owner | Depends on | Status |
|---|---|---|---|---|
| R6 | Photo import research: metadata survival per platform, EXIF libraries, clustering, privacy, storage cost | researcher | none | **Done** (`docs/research/2026-10-23-photo-import.md`). PM review: covers every question, sources marked, unverified items flagged; open device tests moved to T2. |
| T2 | **Real-device metadata test** with a small set of Jason's old hunting photos: iPhone Safari and home-screen app (Options → Location on/off, HEIC → JPEG, date kept?), Android Chrome, desktop Chrome/Safari; does canvas re-encode drop EXIF in Safari and Chrome; iPhone 17 HEIC with current exifr. A throwaway test page from the builder, or the researcher with a public EXIF viewer. Result: a table in the R6 file. | Jason, researcher | none (builder test page waits for design approval) | Not started |
| G5 | **Import flow spec and Claude Design screens:** desktop-first entry ("Build past hunts from photos"), phone path with the iPhone "Options → Location" tip; picking and reading progress; **review screen** with suggested hunts (date range, area, photo count, thumbnails) to rename, merge, split, move photos, discard; **"Unsorted" tray** for undated photos with "set date for selection"; match to an existing hunt the user can read ("Add these to {hunt}?"); overlap with a hunt the user can't read shows only the summary and "Ask {party} to add you"; party picker per hunt; location suggestion (snap to pin / keep as area / skip); upload queue and progress, resumable; storage meter for the owner. Acceptance: every state (empty, loading, offline, error, partial upload) and the privacy copy above. | designer | G4, T2 | Not started |
| B30 | **Read and cluster (pure code, no UI):** exifr `lite` + ExifReader fallback; time-gap clustering with a tunable gap, optional GPS split, median location ignoring 0,0; undated → Unsorted. Unit tests with fixture metadata, including no-GPS and mixed sets. | builder | G5 approved | On hold (design first) |
| B31 | **Resize and strip:** on-device 1600px JPEG + 400px thumbnail, EXIF removed; HEIC handling on desktop; test that output carries no GPS. Reuses B10's upload path. | builder | B10, B30 | On hold (design first) |
| B32 | **Review screen and create hunts:** G5 review flow; creates hunts (unshared, importer in the party), party, hunt location, and one photo-only story post per day on the hunt's stories wall (no new photo table); per-import cap of about 300; idempotent so a retried import never duplicates hunts or photos. | builder | B31, M2b stories | On hold (design first) |
| B33 | **Upload queue and storage meter:** IndexedDB queue (same outbox as M2b), resumes after closing the tab; owner sees group storage used vs. 1 GB. | builder | B32 | On hold (design first) |
| T3 | **Real import test:** Jason imports 2-3 real past trips from a computer and one from his phone; check clusters, dates, locations, privacy of stored files. | Jason, PM | B33 | Not started |

**Out of scope for M4:** Google Takeout sidecars, trail-cam import (ideas list), face or object recognition, auto-publishing without review, storing per-photo GPS.

---

## Potential implementation ideas
_Not scheduled. Jason decides if and when._
- **Resend email sender** with a domain Jason owns, for sign-in code emails to the full crew. Until then, Supabase's built-in email is for testing only.
- **Trail-cam import:** pull trail camera photos into the camp, tied to a stand pin.
- **Photo import for past hunts** (D-17, parked by D-19): upload old photos, read dates and location from metadata, build past hunts. Full plan in Milestone 4 above; research in `docs/research/2026-10-23-photo-import.md`. Past hunts can still be added by hand.

## Decisions Jason needs to make

**Naming decided (D-18: Camp and Hunt). Q5 answered (D-19: photo import parked). Q1-Q4 still open; designer works from the PM defaults until Jason answers.**

| # | Question | Needed by | PM recommendation |
|---|---|---|---|
| Q1 | **What's in a hunt summary** that non-party members see (D-14) | G4, B4b | **Name, dates, area name, party names (members and guests), harvest count, shared yes/no.** Not the cover photo, coordinates, photos, stories, harvest details, awards, stands used or chat. Harvest count gives bragging rights without the details; drop it if the party should keep even that quiet. |
| Q2 | **Who can create a hunt** (D-11 said owner and admins add trip years) | G4, B4b | **Any group member**, who becomes the first party member. Small parties go on their own trips, and import needs it. Pins stay owner/admin only (D-11 unchanged for pins). |
| Q3 | **Unsharing** (D-15 covers sharing) | G4, B4b | **Any party member can unshare**, same as share; the app records who and when, and the confirm says others' phones lose it at their next sync. |
| Q4 | **Do the owner and admins see unshared hunts they weren't on?** | B4b | **No.** Summary only, like everyone else; the privacy promise stays simple. One exception: if the last member account in a party leaves the group, the group owner is added to that party so the hunt isn't locked forever. |
| Q5 | **When to build photo import** | After M2a review | **Own milestone (M4) after M2b, as an off-season task.** Design (G5) and the device test (T2) can run now. Manual "add a past hunt" is in M2a. |

**D-1 to D-17 are decided (see `DECISIONS.md`). The table below is kept for reference.**

| # | Decision | Needed by | PM recommendation |
|---|---|---|---|
| D-5 | **Maps:** library and tile source | Start of M3 | **MapLibre GL JS with USGS satellite and topo tiles**, saved offline for the camp's area only. Free, public domain, allowed to cache. Avoid Mapbox for offline (terms unclear for web). |
| D-6 | **Weather API** | Start of M3 | **Open-Meteo:** free, no key, wind, gusts, moon. Free tier is non-commercial; switch to OpenWeather or paid Open-Meteo if Deer Camp is ever sold. |
| D-7 | **Roster guests** without an app account | M2a (B11) | **Yes.** Already built into the schema; keeps the yearbook complete. |
| D-8 | **Who can create a camp** | M2a (B7) | **Any signed-in user** (already built). Supports other crews later; revisit if misused. |
| D-9 | **Show camp name and inviter on the signed-out invite screen** | M2a (B4) | **Yes.** Only someone holding the link sees it, and nothing else (no pins, members, IDs). Helps a buddy trust the link. |
| D-10 | **Email sender for sign-in codes** | Before M2a B5 | Decided: Resend on hold; built-in email for testing only. R5: built-in email reaches only Supabase team members, about 2/hour, so revisit before any buddy tests sign-in. |
| D-11 | **Who adds pins and trip years** | M2a (B11) | Decided: owner and admins. The "trip years" half is reopened by the model change (Q2); pins unchanged. |
| D-12 | **Research and design proposals** | Before M2a planning closes | **Yes now (small, serve the brief):** QR code of the same per-person invite (M2a, B8); "Ready for camp" checklist (M2a); basic member management: remove member, make/unmake admin (M2a, schema exists); auto-release stand claims at dark (M3); per-stand "good winds" tag (M3). **Later:** printable end-of-season yearbook (after M3); auto dark theme at sunset; custom hunting icons; GPX/coordinate import. **No:** trail-cam import, scouting AI, parcel lookup, cost splitting/dues, email-bound invites, satellite messaging (FAQ note only). |

## Jason actions (not decisions)
1. **Switch the GitHub repo `jastbird2407/deer_camp` to private** (Settings → General → Danger Zone → Change visibility). Do this before the builder pushes the M1 branch.
2. **Create a Supabase project** (free tier). Share keys only through environment variables, never in chat or the repo.
3. **Create a Vercel account** (Hobby) and connect it to the private repo.
4. **Email sender:** on hold per D-10. Needed before any buddy tests sign-in (R5).
5. **Pick the backup admin** (D-3) so the builder can invite them once invites work.
