# Roadmap

_Maintained by the project-manager agent. Last updated 2026-10-23._

**End goal (from `docs/BRIEF.md`):** a private, offline-first yearbook app for one crew, built so other crews can join later.
**Non-negotiables:** camp data (especially pins) private to that camp; works on spotty signal (PWA); each camp isolated from day one.

---

## Milestone 1: Foundation

**Goal:** know what's out there, know how people get in, and have a repo plus a database that keeps each camp's data locked to that camp.

**Exit criteria (all must be true):**
- Research reports and feature matrix are in `docs/research/`, each ending in ranked Recommendations.
- `docs/design/system.md` and `docs/design/onboarding.md` exist and the PM has reviewed them against the brief.
- The Next.js PWA scaffold runs locally; lint, typecheck, and tests pass.
- SQL migrations for all M1 tables exist, every camp-data table has RLS, and automated tests prove one camp can't read or write another camp's data.
- Jason has answered decisions D-1 to D-4 below (D-5 to D-7 can wait).

**Workstreams run in parallel.** Researcher, designer, and builder do not block each other in M1. Where a task touches an open decision, it works from the stated default and flags anything that would change.

| ID | Task | Owner | Depends on | Status |
|---|---|---|---|---|
| R1 | Hunting app teardown | researcher | none | Not started |
| R2 | Group-trip and shared-memory app scan | researcher | none | Not started |
| R3 | Offline, sync, and maps tech scan | researcher | none | Not started |
| R4 | Feature matrix | researcher | R1, R2 | Not started |
| G1 | Starter design system | designer | none | Not started |
| G2 | Sign-in and join-a-camp flow spec | designer | G1 (can draft in parallel); D-2 for final copy | Not started |
| B1 | Scaffold Next.js PWA | builder | none | Not started |
| B2 | Database schema migrations with per-camp RLS | builder | B1 (repo layout) | Not started |
| B3 | Camp-isolation test suite | builder | B2 | Not started |

### R1: Hunting app teardown
- **Goal:** learn what onX Hunt, HuntStand, Spartan Forge, BaseMap, and HuntWise do well and badly, especially pin privacy, group or club sharing, harvest logs, weather, and offline.
- **Deliverable:** `docs/research/YYYY-MM-DD-hunting-apps.md` (date it was written).
- **Acceptance:** each app covers the researcher's standard fields (what, who, features, pricing, pin privacy, group use, offline, user praise and complaints); every claim has a source link or is marked unverified; ends with ranked Recommendations (value to crew vs. effort).
- **Owner:** researcher. **Depends on:** none.

### R2: Group-trip and shared-memory app scan
- **Goal:** find patterns worth copying for the yearbook side and the invite and join flow.
- **Deliverable:** `docs/research/YYYY-MM-DD-group-and-memory-apps.md`.
- **Acceptance:** covers 3–5 group-trip or club tools and 3–5 shared-album, journal, or yearbook apps; includes a short section on how they invite people in (links, codes, accounts); sourced; ranked Recommendations.
- **Owner:** researcher. **Depends on:** none.

### R3: Offline, sync, and maps tech scan
- **Goal:** give Jason facts for the map and offline decisions before M3.
- **Deliverable:** `docs/research/YYYY-MM-DD-offline-and-maps.md`.
- **Acceptance:** covers how outdoor apps handle no signal; PWA limits on iPhone (install, background sync, storage eviction, push); offline queue approaches for Next.js and Supabase; Mapbox vs. Leaflet (and MapLibre) on cost, free tier, satellite and topo tiles, and whether tile terms allow offline caching; Open-Meteo vs. OpenWeather on cost, terms, wind, and moon data; sourced; ranked Recommendations.
- **Owner:** researcher. **Depends on:** none.

### R4: Feature matrix
- **Deliverable:** `docs/research/feature-matrix.md` (products as columns, features as rows).
- **Acceptance:** includes every product from R1 and R2; rows cover at least the brief's features (trip page, roster, harvest board, stories, awards, private pins, harvest-to-pin link, stand assignments, group chat, weather with harvest, offline, multi-group).
- **Owner:** researcher. **Depends on:** R1, R2.

### G1: Starter design system
- **Goal:** one look the builder can apply from the first screen: rugged, warm, outdoor, but clean.
- **Deliverable:** `docs/design/system.md`.
- **Acceptance:** defines color tokens for a daylight high-contrast theme and a pre-dawn dark theme that won't blow out night vision; type scale; spacing; minimum 48px tap targets; core components (button, input, list row, card, sync-status badge, privacy badge, toast or banner); icon set choice; a standard way to show "saved on phone" vs. "sent to camp."
- **Owner:** designer. **Depends on:** none.

### G2: Sign-in and join-a-camp flow spec
- **Goal:** a buddy with low tech comfort gets from an invite to inside the camp in under two minutes, and stays signed in at camp with no signal.
- **Deliverable:** `docs/design/onboarding.md`.
- **Acceptance:** covers these flows: first sign-in; create a camp (becomes owner); invite someone (owner or admin); accept an invite while signed out and while signed in; open the app at camp with no signal; sign out. For each: user goal, steps, screen layout, empty, loading, offline, and error states (expired, used, or revoked invite; wrong code), and copy. Also covers installing the PWA on iPhone and Android, and states the invite rules the schema must support (single-use or reusable, expiry, which role it grants, revoke). Written for the D-2 default (email code); notes what changes for the other sign-in options.
- **Owner:** designer. **Depends on:** G1 (can draft in parallel); D-2 for final copy.

### B1: Scaffold Next.js PWA
- **Goal:** a clean, installable, testable app shell to build on.
- **Deliverables:** Next.js (TypeScript, App Router) project at the repo root; web app manifest and service worker; offline fallback page; `.env.example`; README dev section.
- **Acceptance:** `npm run dev`, `npm run build`, `npm run lint`, `npm run typecheck`, and `npm test` all work; the built app passes a basic installability check (manifest and service worker registered); with the network off, it shows the offline page instead of a browser error; no secrets committed; placeholder Supabase env vars only.
- **Owner:** builder. **Depends on:** none.

### B2: Database schema migrations with per-camp RLS
- **Goal:** the full M1 data model, private per camp, authored as migrations that will run unchanged on Supabase later.
- **Deliverables:** `supabase/migrations/*.sql`; `docs/schema.md` (tables, key columns, who can do what).
- **Acceptance:** see the builder task text below for the table list and rules. In short: every camp-data table has `camp_id` and RLS on; access by role (owner, admin, member); camp creation and invite redemption run through safe database functions; offline-friendly keys; weather snapshot stored on harvests; seed data uses fake locations only.
- **Owner:** builder. **Depends on:** B1 (repo layout).

### B3: Camp-isolation test suite
- **Goal:** proof, not promises, that one camp can't see or touch another camp's data.
- **Deliverables:** tests under `supabase/tests/` (or `tests/db/`), run by `npm test` or `npm run test:db`.
- **Acceptance:** runs against a local Postgres (installed v16) or PGlite with a test-only stub of Supabase's `auth` schema; covers every camp-data table; includes a check that fails if any public table has RLS off; all tests pass.
- **Owner:** builder. **Depends on:** B2.

### Out of scope for M1
- No feature UI beyond the app shell and offline page. Sign-in screens are built in M2, from the G2 spec.
- No Supabase project, Vercel deploy, or real credentials. Jason needs to create these before M2 (see Jason actions).
- No storage bucket policies for photos (M2), no map, weather, or chat code (M3).
- Anything the research surfaces beyond the brief (trail cams, scouting AI, tags and licenses, cost splitting) goes to Jason as a proposal and doesn't go into the build.

---

## Decisions Jason needs to make

| # | Decision | Needed by | PM recommendation |
|---|---|---|---|
| D-1 | **Repo visibility:** private or public GitHub repo (`jastbird2407/deer_camp`) | Before the builder pushes B1 | **Private.** Free on GitHub, works with Vercel Hobby, and keeps the camp's plans out of view. No real pin coordinates go in the repo either way. |
| D-2 | **Sign-in method:** email magic link, email 6-digit code, phone/SMS, or Google | G2 final copy; M2 build | **Email with a 6-digit code** (Supabase supports it free). Magic links often open in the wrong browser instead of the installed iPhone app, which breaks sign-in. SMS costs per text and needs Twilio. Google leaves out anyone without a Google account. Keep sessions long so nobody has to sign in again at camp. |
| D-3 | **Camp owner and admins** | Before M2 seed or real data | **Jason as owner**, plus one trusted admin as backup so the camp isn't locked if Jason loses access. |
| D-4 | **Invite style:** one reusable camp link, or one invite per person | G2 | **Per-person invite links that expire in 7 days**, which the owner can revoke. They're easy to text to the crew, and a forwarded link can't let strangers in. |
| D-5 | **Maps:** Mapbox or Leaflet (or MapLibre) | Start of M3 | **Lean Leaflet or MapLibre** to stay free with no usage meter. Decide after R3 confirms which satellite or topo tiles allow offline caching, because offline maps matter more than polish. |
| D-6 | **Weather API:** Open-Meteo or OpenWeather | Start of M3 | **Open-Meteo:** free, no key, has wind data. Its free tier is for non-commercial use, so revisit if Deer Camp is ever offered to other crews for money. Moon phase can be calculated in the app. |
| D-7 | **Roster guests:** can the roster, harvests, and awards include people without an app account (for example, an older hunter who won't install it)? | M2 | **Yes.** The schema supports a name-only guest so the yearbook stays complete. |

## Jason actions (not decisions)
- Before M2: create a Supabase project (free tier) and a Vercel account, then share the keys through environment variables, never in chat or the repo.

---

## Milestone 2: Yearly Trip Page
_To be planned after M1 review._ Expected work: sign-in and join flow (from G2), camp home, trip-year page (cover, location, roster), harvest board, stories wall with photos, awards, photo storage with per-camp bucket policies, background photo upload, local offline queue for posts.

## Milestone 3: Map, Stand Assignments, Live Hunt, Weather
_To be planned._ Needs D-5 and D-6 first.
