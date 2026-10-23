# Camp Home, Trip Page and Crew

_Owner: designer. Task G3 (M2a). Last updated 2026-10-23. Uses tokens and components from `system.md`; sign-in, invites and install come from `onboarding.md` (G2)._

**Goal:** a buddy opens Deer Camp, sees his camp and this year's trip (cover, location, who's coming), and can trust it will be there at camp with no signal. Owner and admins can set up the year and manage the crew in a few taps.

**Decisions this spec follows:** D-7 (name-only guests on the roster) · D-11 (owner and admins add trip years) · D-12 ("Ready for camp" checklist; basic member management) · D-3 (one owner, backup admin).

**Schema checked:** `trip_years` (one per camp per year; `title`, `location_name`, `starts_on/ends_on`, `cover_photo_path`), `year_attendees` (member or `guest_name`), `set_member_role` (owner only), `camp_members` delete policy, roster kept as guest names on removal. All of these exist on the M1 branch.

Placeholders: `{Camp}`, `{Owner}`, `{year}`, `{name}`, `{time}` = "Fri 6:12 PM".

---

## 0. Shared rules
- **Frame:** header bar · banners · content · optional pinned primary (56px) · bottom tab bar.
- **Header bar (inside a camp, always):** camp name (`h2`), with a `chevron-down` only if the user is in more than one camp (tapping it opens the camp picker) · "[lock] Camp only" privacy badge · app-wide sync badge (only when something is waiting).
- **Bottom tab bar (M2a):** `tent` "Camp" · `users` "Crew" · `circle-user` "Me". Icon + label, each tab at least 64px wide and 56px tall. Map and Chat tabs join in M3, so the bar can hold 5 tabs.
- **Reading works offline. Editing in M2a needs signal.** Create or edit year, add guest, roster changes, role changes and removals are all online-only. Their buttons use the "Needs signal" state (system.md, Button). Typed input is kept. No outbox for these in M2a; posts and harvests get the outbox in M2b.
- **"Saved on phone" freshness line:** the camp home and trip page end with a caption: "[smartphone] Saved on this phone · updated {time}". This is the user's proof the page will be there at camp.
- **Loading:** skeletons (`surface-sunken` blocks) only after 400ms and only when nothing is cached. With a cache, show cached data at once and refresh quietly.
- **Offline banner** (warning tone, from G2 flow 6): "[cloud-off] No signal. Showing what's saved on this phone (updated {time})."
- **Load error with no cache:** full-screen state: `triangle-alert`, h1 "Couldn't load {Camp}", body "Check your signal and try again.", primary "Try again".
- **Role-gated controls are hidden, not disabled**, for members. Where a member might look for one, show a caption that says who can do it.
- **Success** is a toast ("2026 added."); a toast is never the only place a result shows (the new item appears on screen).

---

## 1. Camp home
**User goal:** see my camp and get to this year's trip in one tap.

**Opens when:** signed in and in exactly one camp, or the last-opened camp (remembered on the device) if in several.

**Layout (top to bottom):**
1. Header bar (section 0).
2. Banners: offline · welcome after joining (G2) · "Please sign in again" (G2).
3. **"Ready for camp" card** (section 6), until all items are done; then it shrinks to a 56px row: "[circle-check] Ready for camp" with a chevron.
4. **"Invite your crew" card** (G2 flow 2): owner and admins only, while the owner is the only member.
5. **This year's trip card** (the newest trip year): cover photo (16:9, 12px radius on top), year in `display`, title if set (`h2`), location line with `tent` icon, caption "{n} on the roster". The whole card opens the trip page.
6. **Past years:** `h2` "Past years". List rows: year (`h2` weight) · subtitle "{location} · {n} hunters" · chevron. Newest first.
7. Freshness line (section 0).

**Admin extras:** a secondary button "Add a year" under the past-years list. If the current calendar year has no page yet, and it's after Aug 1, show a card on top of the trip card: h2 "Start the {year} trip", body "Add this year's page so the crew has a place for the roster, photos and stories.", button "Add {year}".

**States:**
| State | Owner or admin | Member |
|---|---|---|
| Empty (no years) | Card: h2 "Start your first year", body "Add this year's trip so the crew has a page for the roster, photos and stories.", primary "Add {year}" | Card: h2 "No trip pages yet", body "{Owner} will add this year's trip. Check back soon." |
| Loading (no cache) | Skeleton: header, one tall card, three rows | Same |
| Offline, cached | Normal page + offline banner. "Add a year" shows "Needs signal" | Normal page + offline banner |
| Offline, nothing cached | G2 flow 6: h1 "Nothing saved on this phone yet", body "Open Deer Camp once with signal so it can download your camp." | Same |
| Error | Section 0 load error | Same |
| Cover photo missing or not cached | Card shows a `surface-sunken` block with a `tent` icon and the year. Never a broken image | Same |

---

## 2. Camp picker
**User goal:** switch between camps (rare; mostly for people in two crews).

**Entry:** tap the camp name in the header (only when in 2+ camps) · Me → "Switch camp" · after sign-in when in 2+ camps and no camp was opened before on this phone.

**Layout:** full-screen sheet. h1 "Your camps". List rows: `tent` icon · camp name · subtitle "{Role} · {n} in the crew" · trailing `circle-check` on the current camp. Below: secondary "Start a camp" (D-8, G2 flow 2).
- Tapping a row opens that camp's home and remembers it as last-opened.
- Privacy line once, under the h1: "[lock] Each camp is private. Nothing is shared between them."

**States:**
- **Offline:** camps saved on the phone open normally. Camps never opened on this phone show subtitle "Not saved on this phone" and the row is disabled with "Needs signal".
- **Loading:** list from the phone at once; refresh quietly.
- **Error:** keep the cached list; toast "Couldn't refresh your camps."
- **Empty:** not possible here (0 camps goes to G2's "You're not in a camp yet").
- **Removed from a camp:** that row disappears after the next refresh; if it was open, G2 flow 6 "You're no longer in {Camp}" screen.

---

## 3. Trip-year page
**User goal:** see the year at a glance: where, when, who came.

**Header:** back to camp home · title "{year}" · "[lock] Camp only" · owner/admin only: `pencil` icon button (aria-label "Edit {year}").

**Layout (top to bottom):**
1. **Cover photo:** full width, 4:3, no side padding. Dark theme: dimmed to 70%; tap opens full brightness (system.md 2b). No cover: `surface-sunken` block with the year in `display`; admins also see a ghost "Add cover photo" on it.
2. **Title block:** year in `display` · title (`h1`) if set · `tent` + location name (`body-lg`) · dates caption "Nov 14–22" if set.
3. **Roster:** h2 "Who made the trip" + count. Rows (56px): initials avatar (40px) · name · subtitle "Owner" / "Admin" / "Member" / "Guest". Guests also get a `user` outline avatar (not initials in a filled circle) so they read differently without color. Sorted: members A–Z, then guests A–Z.
   - Owner/admin: secondary button "Add to roster" under the list (section 5). Each row has a trailing 48px `ellipsis` menu: "Remove from {year}" (members and guests) and "Edit name" (guests only).
   - Members: no controls. Caption under the list: "Missing someone? Ask {Owner} or an admin."
4. **M2b slots (reserved, not specified here).** Fixed order so M2b drops in without a re-layout:
   - `harvest-board` slot: "Harvest board"
   - `stories` slot: "Stories"
   - `awards` slot: "Camp awards"
   - **In M2a**, render one muted card in the slot area: h2 "More coming", body "Harvests, stories and camp awards are coming before the season." No buttons. Remove it when M2b ships.
5. Freshness line.

**Pinned primary:** none in M2a (M2b will put "Post" or "Log harvest" here; keep the bottom free).

**States:**
- **Loading:** skeleton cover block, two text lines, four rows.
- **Offline, cached:** normal page + offline banner; edit icon and roster controls show "Needs signal" when tapped (sheet with the reason, not a dead tap).
- **Offline, not cached** (a year never opened on this phone): body "This year isn't saved on this phone yet. Open it once with signal." Past years are cached only after being opened; the current year is always cached (B12).
- **Empty roster:** admin: "No one on the {year} roster yet." + "Add to roster" (primary-sized). Member: "No one on the roster yet."
- **Error:** section 0 load error, title "Couldn't load {year}".
- **Year deleted by an admin while open:** toast "That year was removed." and return to camp home.

---

## 4. Create or edit a year (owner and admins, D-11)
**User goal:** set up this year's page in under a minute.

**Entry:** camp home "Add {year}" / "Add a year" · trip page `pencil`.

**Screen: "Add a year" / "Edit {year}"** (one scrolling form; inputs per system.md):
- **Year** (required, numeric keyboard, default = current year). Help: "One page per year."
- **Location** (optional, up to 120 characters). Help: "Like *Uncle Ray's place, Ottawa County*. Only {Camp} sees this."
- **Trip name** (optional, up to 60 characters). Help: "A nickname for the year, like *The Big Snow*."
- **Dates** (optional): two date fields "First day" / "Last day".
- **Cover photo** (optional): a 16:9 well with `image-plus` + "Add cover photo". After picking: preview + ghost "Change" and ghost "Remove".
- Privacy card at the bottom: "[lock] Only **{Camp}** can see this page and its photos."
- **Pinned primary:** "Create {year}" / "Save changes".

**After create:** go to the **"Who made the trip?"** step (the "Crew" segment of section 5, full screen) with every current member pre-checked, since most of the crew comes every year. Primary "Add {n} to roster", ghost "Skip for now". Then the trip page opens. Toast: "{year} added."

**Builder notes:**
- Resize the cover on the phone before upload (about 2000px long edge, JPEG ~80%) and **strip EXIF, especially GPS**, so a photo can't leak the camp's location.
- In M2a the cover uploads with the save and needs signal. If the photo upload fails but the page saved, keep the page, show the cover well in `danger`: "[triangle-alert] Photo didn't upload. Tap to retry." (M2b moves this to "Photo waits for better signal".)
- Year changes on edit are allowed (to fix a typo) but must stay unique.
- No delete-a-year in M2a (see open questions).

**States and copy:**
- **Loading:** primary spins; with a photo, the caption under the button reads "Uploading photo…".
- **Offline:** primary shows "Needs signal". Help line: "Adding a year needs signal so the whole crew gets it."
- **Year already exists:** on the Year field: "{Camp} already has a {year} page." + ghost link "Open {year}".
- **Year out of range:** "Enter a year like 2026."
- **Last day before first day:** "The last day is before the first day."
- **Photo too big or not an image:** "That file isn't a photo we can use. Try a different one."
- **Save error:** banner (danger): "Couldn't save {year}. Check your signal and try again." All fields kept.
- **Member reaches this screen (old link, role changed):** "Only {Owner} and admins can add or edit years." + "Back".

---

## 5. Add to roster and add a guest (owner and admins)
**User goal:** get everyone who came, including guests without the app, on the year's roster.

**"Add to roster" sheet**, two segments:
- **Crew** (default): checklist of camp members not yet on this year's roster (56px rows, checkbox on the left, whole row tappable). Primary "Add {n}". Empty: "Everyone in the crew is already on the {year} roster."
- **Guest:**
  - Field "Guest's name" (required, 1–40 characters).
  - Help: "For someone who came but isn't on the app, like a son, a neighbor or a buddy's dad."
  - **Past guests** as tappable chips (48px tall) under the field: names used as guests in earlier years of this camp, so "Uncle Ray" is one tap.
  - Caption: "[lock] Only {Camp} sees this name."
  - Primary "Add guest". Toast: "{name} added to {year}."

**Edit guest name:** same field in a sheet, primary "Save".

**Remove from {year}** (member or guest): confirm sheet h2 "Take {name} off the {year} roster?", body "They stay in the crew. This only changes who's listed for {year}." Buttons "Remove" (danger) / "Keep". (In M2b, add: "{n} harvests are tied to {name} for {year}." if any.)

**States:**
- **Duplicate:** "{name} is already on the {year} roster."
- **Empty name:** "Type a name."
- **Offline:** primaries show "Needs signal".
- **Error:** "Couldn't update the roster. Try again." Input kept.

---

## 6. "Ready for camp" checklist (D-12)
**User goal:** know before leaving town that the app will work at camp.

**Where:** card on camp home (section 1) and full screen at Me → "Ready for camp".

**Layout:** h1 "Ready for camp" · body "Do these before you leave town." · progress caption "{done} of {total} done" · checklist rows (72px, two lines). Each row: status icon + word (never color alone) · title · subtitle · trailing action button (48px) when not done.

| Row | Done when (checked on this phone, works offline) | Done text | To-do text + action |
|---|---|---|---|
| Installed | Running from the home screen (`display-mode: standalone` / `navigator.standalone`) | "On your home screen" | "Put Deer Camp on your home screen" · "Show me how" (G2 flow 5). In a browser on iPhone add: "Already did it? Open Deer Camp from your home screen." |
| Signed in | Has a session **inside the installed app** | "Signed in on this phone" | In the browser: "Open the home-screen app to check" (no action). In the app with no session: "Sign in" |
| Camp saved | Camp home, current year, roster and cover are cached, and storage is kept (`persist()` granted, or installed on iPhone) | "{Camp} saved · updated {time}" | "Not saved yet" or "Last saved {time}" if older than 3 days · "Save now" (Needs signal when offline) |
| Nothing waiting | 0 items waiting to send | "Nothing waiting to send" | "{n} things waiting. Get signal before you go." · "Try sending" |
| Alerts (M3) | **Hidden in M2a.** Added with M3 push alerts, only where the phone supports them (iPhone: installed app, iOS 16.4+) | "Alerts on" | "Turn on alerts" |

- Row icons: done `circle-check` + "Done" (`success`); to do `circle` + "To do" (`warning`); can't check `circle-help` + "Check in app" (`text-muted`).
- All done: the card shrinks to the one-line row; the full screen shows "[circle-check] You're ready for camp."
- **States:** never loading (all checks are local and instant), works fully offline, no errors; "Save now" failure: "Couldn't save. Check your signal and try again."

---

## 7. Crew screen and member management (D-12)
**User goal:** see who's in the camp; the owner and admins keep it tidy.

**Layout (Crew tab):**
- Header bar + h1 "Crew" + caption "{n} people · [lock] Only {Camp}".
- Owner/admin: primary pinned "Invite someone" (G2 flow 3).
- **Members list:** owner first, then admins, then members, A–Z. Row: initials avatar · name (+ " (you)") · subtitle role, with an icon so role isn't color-only: `crown` Owner, `shield` Admin, none for Member. Tap opens the member sheet.
- **Waiting to join:** owner/admin only, exactly as G2 flow 3.
- Members see the caption "Ask {Owner} to invite new people." (G2).

**Member sheet:** name (`h2`), role, caption "Joined {date}". Actions as 48px full-width buttons, only the ones the viewer may use:

| Viewer → person | Actions |
|---|---|
| Owner → admin | "Make member" (secondary) · "Remove from camp" (danger) |
| Owner → member | "Make admin" (secondary) · "Remove from camp" (danger) |
| Admin → member | "Remove from camp" (danger) |
| Admin → owner or admin; member → anyone | None. Caption: "Only {Owner} can change roles." |
| Admin or member → self | "Leave {Camp}" (danger, ghost) |
| Owner → self | Caption: "You're the owner. The owner can't leave the camp." |

**Confirm sheets (copy):**
- **Make admin:** h2 "Make {name} an admin?" body "Admins can invite members, add years and guests, add pins, hide posts and remove members. You can undo this." Buttons "Make admin" (primary) / "Cancel". Toast "{name} is now an admin."
- **Make member:** h2 "Make {name} a member?" body "{name} won't be able to invite people, add years or remove anyone." Buttons "Make member" / "Cancel". Toast "{name} is now a member."
- **Remove:** h2 "Remove {name} from {Camp}?" body "{name} loses access to the pins, photos, posts and messages right away. Their name stays on past rosters, harvests and stories." Buttons "Remove" (danger) / "Keep {name}". Toast "{name} was removed."
- **Leave:** h2 "Leave {Camp}?" body "You'll lose access to its pins, photos, posts and messages, and they'll be cleared from this phone. You'll need a new invite to come back." Buttons "Leave camp" (danger) / "Stay". Then camp picker or G2 "You're not in a camp yet".

**States:**
- **Loading:** cached list at once; skeleton of five rows only with no cache.
- **Offline:** list from the phone + offline banner; all sheet actions show "Needs signal". Invite button per G2.
- **Empty:** owner alone: G2 "Invite your crew" card in place of the list below the owner's row.
- **Error (action):** "Couldn't update {name}. Try again." Sheet stays open.
- **Changed by someone else** (role already changed or person already removed): refresh the list, toast "The crew list changed. Here's the latest."
- **My own role changed while I'm on the app:** controls appear or disappear after the next refresh, with an info banner "Your role in {Camp} is now {role}." (one time).

---

## 8. Me tab (M2a contents)
- Name and email (read-only in M2a) · "Ready for camp" (section 6) · "Switch camp" (2+ camps) · "Install the app" (G2 flow 5, hidden once installed) · Theme: Sun/Moon/Auto (system.md 2) · "Sign out" (G2 flow 7).

---

## Open questions (for the PM)
1. **Members adding themselves to a year's roster:** the schema allows it. Default in this spec: owner and admins manage the roster only. A member "I made this trip" button would be cheap; approve or not?
2. **Leave camp** for non-owners is included (schema allows it and there's otherwise no way out). Confirm.
3. **Transfer ownership** exists in the schema but isn't in D-12. Left out of M2a. Is that OK, given D-3's backup admin can't take over if Jason is unavailable?
4. **Delete a year** is left out of M2a (it would cascade the roster and, later, harvests). Mistakes are fixed by editing. Confirm.
5. **Trip name and dates** are optional fields already in the schema. Kept, as they help the yearbook; cut if the PM wants M2a leaner. Schema allows a 120-character title; the spec caps it at 60 for layout.
6. **Removed members show as "Guest"** on past rosters, because the schema turns them into plain guest names. A "Former member" label would need a flag column. Low priority.
7. **Guest name length:** schema allows 80; spec caps at 40 for layout. Builder to align in the UI only.
8. **M2b placeholder:** spec shows one muted "More coming" card. Alternative: show nothing until M2b. PM's call.
