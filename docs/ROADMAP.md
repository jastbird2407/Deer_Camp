# Roadmap

_Maintained by the project-manager agent. Last updated 2026-10-23 (M1 review)._

**End goal (from `docs/BRIEF.md`):** a private, offline-first yearbook app for one crew, built so other crews can join later.
**Non-negotiables:** camp data (especially pins) private to that camp; works on spotty signal (PWA); each camp isolated from day one.

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
| B4 | **Invite and profile schema follow-ups** (table above): add `preview_invite(token)` callable signed out, returning only camp name, inviter display name, role, expiry and status; `redeem_invite` returns `ok / already_member` and stable error codes; required `invitee_name` returned by preview; `used_at/used_by/revoked_by`; `camp_members.invited_by/invite_id`; cap `max_uses` at 1; column grants so no API role can read `token_hash`; align name lengths. Tests for each, including anon can call only `preview_invite` and gets nothing else. | builder | none | Not started |
| R5 | **Short verification scan:** (a) Supabase built-in email limits and free custom SMTP options (e.g. Resend) for 6-digit codes; (b) current iOS behavior: does a home-screen web app share Safari's sign-in and storage? (c) a test Open-Meteo call confirming moon fields; (d) fix the QR wording in the feature matrix. | researcher | none | Not started |

### Adopted defaults (PM, inside approved scope; no Jason decision needed unless he objects)
- Only the owner can invite admins; admins invite members only (fits D-3).
- Offline queue approach: hand-rolled IndexedDB outbox with client UUIDs and idempotent upserts (R3 rec 1). No extra sync service.
- Install-to-home-screen is part of onboarding (R3 rec 2, G2 flow 5).
- iPhone order stays: join in Safari, then install, then sign in once more inside the app. R3 confirms installed web apps keep their own storage; R5(b) confirms the sign-in split; a real-phone test (T1) checks the 2-minute goal.
- Never offer a public link for pins (R1 rec 1). Removing a member keeps their harvests and stories (R2 rec 10, already in B2).

---

## Milestone 2: Yearly Trip Page

**Goal:** the crew can sign in, Jason can create the camp and invite everyone, and the 2026 trip page shows the year, cover photo, location and roster, readable with no signal.

Split in two so the crew can get in before the season:
- **M2a: Get the crew in + trip page basics** (planned below).
- **M2b: Yearbook content:** harvest board with weather snapshot, stories wall, awards, background photo upload, offline outbox for posts. Planned after M2a review.

**M2a exit criteria:**
- Jason and at least one buddy (one iPhone, one Android) go from invite text to inside the camp in under 2 minutes, on real phones (T1).
- Cold start in airplane mode shows the camp home and roster from the phone (R1 rec 3).
- Isolation tests pass against the real Supabase setup, not only the local stub.
- Photo storage policies keep each camp's files private, with tests.

| ID | Task | Owner | Depends on | Status |
|---|---|---|---|---|
| J1 | Jason actions (below): repo private, Supabase project, Vercel, email sender | Jason | none | Not started |
| B5 | **Wire up Supabase and Vercel:** apply migrations; email OTP template with the 6-digit `{{ .Token }}`, code life ≤1 hour, 60s resend; session settings from G2 flow 6 (no time-box, no inactivity timeout); custom SMTP; run the DB tests against Supabase (local CLI or the project) and prove anon can only call `preview_invite`; preview deploy on Vercel. | builder | J1, B4 | Not started |
| G3 | **Trip page and camp home spec:** camp home, camp picker, trip-year page (year, cover, location, roster with guests), create/edit year, add guest, Crew screen; empty, loading, offline and error states. Uses G1. | designer | none | Not started |
| B6 | **Sign-in screens** (G2 flows 1 and 7) and **stay signed in offline** (flow 6 basics: never sign out on network error). | builder | B5 | Not started |
| B7 | **Create a camp, no-camp screen, camp picker** (flow 2). | builder | B6 | Not started |
| B8 | **Invites:** create, share sheet / text / copy, "Waiting to join" list, cancel; join preview signed out and signed in; all error screens (flows 3 and 4). | builder | B4, B6, B7 | Not started |
| B9 | **Install the app** card and iPhone/Android steps, `persist()` after install, standalone "sign in once more" screen (flow 5). | builder | B6 | Not started |
| B10 | **Photo storage:** private bucket, policies that mirror `camp/<camp_id>/` paths, tests that another camp and anon can't read or write. | builder | B5 | Not started |
| B11 | **Trip page basics:** create a year, cover photo (upload on signal), location name, roster with members and name-only guests (D-7). | builder | G3, B7, B10 | Not started |
| B12 | **Offline read cache:** cache camp home, trip page and roster after each online load; offline banner with "updated {time}"; airplane-mode cold-start test. | builder | B11 | Not started |
| T1 | **Real-phone onboarding test:** Jason + one buddy, iPhone and Android, timed from text to camp home; log problems as tasks. | Jason, PM | B8, B9 | Not started |

### Out of scope for M2a
- Harvest board, stories, awards, chat, map, weather (M2b and M3).
- Member management screens beyond what B8 needs (pending D-12).
- Anything in the proposals list that Jason hasn't approved.

### Notes for later milestones (from M1 review)
- **M3 stand board sync:** `pin_assignments` uses hard deletes. Before building live stand claims, decide how other phones learn a claim was dropped (tombstones or full refresh).
- **Rate limiting** for the signed-out `preview_invite` call should sit at the Supabase/Vercel edge; tokens are 244-bit, so guessing is not practical.

---

## Milestone 3: Map, Stand Assignments, Live Hunt, Weather
_To be planned after M2._ Needs D-5 and D-6. Chat is "sends when the app is open and has signal" on iPhone (R3 rec 7).

---

## Potential implementation ideas
_Not scheduled. Jason decides if and when._
- **Resend email sender** with a domain Jason owns, for sign-in code emails to the full crew. Until then, Supabase's built-in email is for testing only.
- **Trail-cam import:** pull trail camera photos into the camp, tied to a stand pin.

## Decisions Jason needs to make

**D-1 to D-12 are all decided (see `DECISIONS.md`). The table below is kept for reference.**

| # | Decision | Needed by | PM recommendation |
|---|---|---|---|
| D-5 | **Maps:** library and tile source | Start of M3 | **MapLibre GL JS with USGS satellite and topo tiles**, saved offline for the camp's area only. Free, public domain, allowed to cache. Avoid Mapbox for offline (terms unclear for web). |
| D-6 | **Weather API** | Start of M3 | **Open-Meteo:** free, no key, wind, gusts, moon. Free tier is non-commercial; switch to OpenWeather or paid Open-Meteo if Deer Camp is ever sold. |
| D-7 | **Roster guests** without an app account | M2a (B11) | **Yes.** Already built into the schema; keeps the yearbook complete. |
| D-8 | **Who can create a camp** | M2a (B7) | **Any signed-in user** (already built). Supports other crews later; revisit if misused. |
| D-9 | **Show camp name and inviter on the signed-out invite screen** | M2a (B4) | **Yes.** Only someone holding the link sees it, and nothing else (no pins, members, IDs). Helps a buddy trust the link. |
| D-10 | **Email sender for sign-in codes** | Before M2a B5 | **Resend free tier** with a domain Jason owns (about $10–15/yr). Supabase's built-in email is reportedly limited and not meant for real users (R5 confirms). |
| D-11 | **Who adds pins and trip years** | M2a (B11) | **Owner and admins only** (as built). Members claim stands, log harvests and post. Keeps the private map tidy. |
| D-12 | **Research and design proposals** | Before M2a planning closes | **Yes now (small, serve the brief):** QR code of the same per-person invite (M2a, B8); "Ready for camp" checklist (M2a); basic member management: remove member, make/unmake admin (M2a, schema exists); auto-release stand claims at dark (M3); per-stand "good winds" tag (M3). **Later:** printable end-of-season yearbook (after M3); auto dark theme at sunset; custom hunting icons; GPX/coordinate import. **No:** trail-cam import, scouting AI, parcel lookup, cost splitting/dues, email-bound invites, satellite messaging (FAQ note only). |

## Jason actions (not decisions)
1. **Switch the GitHub repo `jastbird2407/deer_camp` to private** (Settings → General → Danger Zone → Change visibility). Do this before the builder pushes the M1 branch.
2. **Create a Supabase project** (free tier). Share keys only through environment variables, never in chat or the repo.
3. **Create a Vercel account** (Hobby) and connect it to the private repo.
4. **Set up the email sender** per D-10: buy or pick a domain, create a Resend account, verify the domain. The builder connects it to Supabase in B5.
5. **Pick the backup admin** (D-3) so the builder can invite them once invites work.
