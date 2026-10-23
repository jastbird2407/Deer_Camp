# Modules (hunt and camp)

_Owner: designer. Task for D-20. Last updated 2026-10-23. Builds on `hunts.md` (section 3, hunt page) and `system.md` (tokens, components). Status: **draft for Jason's approval** (design first)._

**Decisions followed:** D-20 (modules) · D-13/D-18 (camp → hunts → party) · D-14/D-15 (summary until shared; any party member shares) · D-16 (pins belong to the camp) · D-11 (owner and admins add pins) · D-7 (name-only guests).

## Sample data
- Camp **Bird Creek Camp**. Hunt **Rifle Opener 2026**, Nov 14–22, 2026, North pasture lease, **not shared**. Party: Jason B. (you), Travis K., Cody R., Uncle Ray (guest). Today: Fri Oct 23, 2026.
- Camp pins: Creek Bottom, Ridge Ladder, Hedge Row Blind, South Food Plot.

---

## 0. Words and icons

- In the UI a module is called a **section**. People already know "add a section"; "module" is a builder word. Button: "Add a section". (Builder and docs can keep saying "module".)
- Proposed icons (Lucide). Add them to `system.md` section 5 once approved:

| Module | Icon | Default title |
|---|---|---|
| Checklist | `list-checks` | "Checklist" |
| Photos | `images` | "Photos" |
| Pins | `map-pin` | "Stands" |
| Regulations | `scroll-text` | "Rules" |
| Notes | `notebook-pen` | "Notes" |
| Harvest board (M2b) | `target` | "Harvest board" |
| Stories (M2b) | `book-open` | "Stories" |
| Camp awards (M2b) | `trophy` | "Camp awards" |

- "Pins" shows as **"Stands"** by default because that is what the crew calls them. "Regulations" shows as **"Rules"** because it is shorter and covers lease rules too. Both can be renamed.
- In Rules, "allowed" uses `check` + "OK" and "not allowed" uses `ban` + "Not allowed". Don't use `circle-check` here, because it already means "Sent to camp".

---

## 1. The module model

### 1a. Hunt page layout (replaces `hunts.md` section 3, items 7 to 10)
**Fixed header.** These parts are always present and can't be moved or removed:
1. Header bar (back, hunt badge, sync badge, `pencil`)
2. Live hunt bar (M3, only while the hunt is on)
3. Cover
4. Title block (name, dates, area, "Starts in 22 days")
5. Share card or shared row
6. Party

**The section stack.** Below the header comes an ordered list of section cards. After the stack:
- Secondary button `plus` "Add a section" (full width, 48px, party only)
- Freshness line
- Pinned actions (Post / Log harvest, M2b), unchanged

### 1b. What becomes a module

| Today (`hunts.md`) | Becomes | Why |
|---|---|---|
| Harvest board | Module (one per hunt) | Yearbook content, and some hunts don't need it (a scouting trip, a fishing day) |
| Stories | Module (one per hunt) | Same |
| Camp awards | Module (one per hunt) | Same. Some crews don't do awards |
| Stands today | **Merged into the Pins module ("Stands")** | One place for stands. Before the hunt it shows the plan (who's assigned). During the hunt (M3) the same rows show today's claims and the weather line |
| Live hunt | **Stays fixed** (the bar under the header) | It's time-bound and has to be glanceable in one second. It should never be buried or removed by accident |
| Weather | **Not a module.** It stays as the caption line in Stands (M3) and on each harvest | A stand-alone weather card adds a network dependency and little yearbook value. If Jason wants a forecast card, that's a scope proposal (see Proposals) |

### 1c. Default sections for a new hunt
New hunts start with this order, which matches the hunt's arc: plan, then hunt, then remember.
1. **Checklist** (starts from the camp template if one exists, section 4)
2. **Rules**, only if the camp has camp Rules. It comes pre-linked to them.
3. **Stands**
4. **Harvest board** (M2b)
5. **Stories** (M2b)
6. **Photos**
7. **Camp awards** (M2b)

Notes and any extra Checklist or Rules sections are added on demand. Before M2b, the M2b sections aren't added. When M2b ships, existing hunts get them added once, at the end of the stack.

**Past hunts** (last day before today when the hunt is created) start with: Harvest board, Stories, Photos, Camp awards. Nobody needs a packing list for 2019.

### 1d. Who can do what

| Action | Party member | Guest | Camp member not in party (shared hunt) | Summary viewer |
|---|---|---|---|---|
| See sections | Yes | No account (n/a) | Read only | **No sections** (summary stays as `hunts.md` A1) |
| Add, remove, reorder, rename | Yes | n/a | No (menus hidden) | n/a |
| Edit content (claim, check, write) | Yes | n/a | No | n/a |

- This matches D-15: any party member already manages the party and sharing, so they manage sections too.
- **Layout changes** (add, remove, reorder, rename) **need signal**, the same as the other hunt edits in `hunts.md` A6. This keeps everyone's stack in the same order. **Content edits work offline** and save on the phone.

### 1e. Limits
- **Up to 12 sections per hunt.** At 12, "Add a section" is disabled with the reason "12 sections is the max. Remove one to add another."
- **Only one each of** Stands, Photos, Harvest board, Stories and Camp awards. These collect the hunt's data, so two copies would split it.
- **Up to 3 each of** Checklist, Rules and Notes. Example: "Grocery run" and "Gear" as two checklists.

### 1f. Section card
A section is a `system.md` Card with a header row and a body.

**Header row** (56px, the whole row toggles collapse):
- Icon (24px) · title (`h2`) · count (`caption`, muted) · trailing `chevron-down`/`chevron-up` · trailing 48px `ellipsis` menu (party only)
- Counts by type:
  - Checklist: "9 of 14 packed · 3 need someone"
  - Photos: "23 photos"
  - Stands: "4 stands"
  - Rules: "Checked Oct 20"
  - Notes: "Edited Thu"
  - Harvest: "2"
  - Stories: "5"
  - Awards: "3"
- A small `tent` badge "From camp" appears next to the count when the section holds camp content (section 4).

**`ellipsis` menu** (bottom sheet, 56px rows):
- `pencil` "Rename"
- `arrow-up` "Move up" (hidden on the first section)
- `arrow-down` "Move down" (hidden on the last section)
- `trash-2` "Remove section" (danger text)

Notes on the menu:
- **No drag to reorder.** Drag is hard with gloves and has no clear accessible version. Move up and Move down are enough for a stack of 12 or fewer.
- **Rename:** a sheet with field "Section name" (1–30 characters), primary "Save", and the help text "Only the name changes. Everything in it stays." The type icon stays, so a renamed "Grocery run" still shows `list-checks`.
- **Remove:** a confirm sheet:
  - `h2` "Remove Checklist?"
  - body "Its 14 items go with it. You can bring it back for 30 days from **Add a section**."
  - Buttons: danger "Remove" · ghost "Keep it"
  - After: toast "Checklist removed." with the action "Undo" (8 seconds)
  - If the section is empty, skip the confirm. Just remove it and show the Undo toast.

**Collapse:**
- Collapsed is remembered per phone, per hunt.
- Default is expanded, except that **Checklist auto-collapses once the hunt's last day has passed**, so the yearbook leads.
- A collapsed card is the 56px header row only.

---

## 2. "Add a section" sheet

**Entry:** the "Add a section" button at the end of the stack. Opens a full-height sheet.

**Layout:**
1. `h2` "Add to Rifle Opener 2026" · close `x`.
2. Caption (hunt badge, long form): "[lock] Only the party sees what you add here." When the hunt is shared: "[tent] Everyone in Bird Creek Camp can see this hunt."
3. **List, not grid.** Rows are 72px with a 40px icon tile, name (`body-lg`, 600) and a one-line description (`caption`). A list reads better at large text sizes and gives bigger tap targets than a 2-column grid. Rows by type:
   - `list-checks` **Checklist**: "Who's bringing what. Works without signal."
   - `scroll-text` **Rules**: "What's OK and what's not on this hunt."
   - `notebook-pen` **Notes**: "A notepad for anything: plans, meals, directions."
   - `map-pin` **Stands**: "Camp pins you're hunting and who's sitting where."
   - `images` **Photos**: "Everyone's photos from the hunt, in one place."
   - `target` **Harvest board**: "Every deer, who got it, where, and the weather."
   - `book-open` **Stories**: "Posts and photos from camp."
   - `trophy` **Camp awards**: "Biggest buck, worst miss, camp cook."
4. **Rows that can't be added** stay visible but disabled, with the reason as a trailing caption:
   - One-per-hunt sections already in the stack: "[check] Added"
   - At 3 of a type: "3 is the max"
   - Not built yet (M2b): "Coming before the season"
5. **Recently removed** (`h2`-weight, only when there is something to restore): rows like "Notes · removed by Cody R. · Oct 21" with a trailing secondary "Bring back".
6. Footer caption: "12 sections max · 8 now".

**Tap a row:**
- Most types: the section is added at the **end** of the stack, the sheet closes, the page scrolls to the new section (expanded), and a toast confirms: "Notes added."
- **Checklist when the camp has a template:** a step sheet opens.
  - `h2` "Start your checklist"
  - Radio rows (56px):
    - "Start from **Opener checklist**" with caption "18 items from Bird Creek Camp. Nobody's claimed yet." This one is the default.
    - "Start empty"
  - Primary "Add checklist"
- **Rules when the camp has camp Rules:** a step sheet opens.
  - `h2` "Add rules"
  - Checkbox row (default on): "Include **Kansas firearm deer season** from Bird Creek Camp", with caption "Stays up to date when the camp's copy changes."
  - Primary "Add rules"

**States:**
- **Offline:** rows are disabled. Top banner: "[cloud-off] Adding a section needs signal so the whole party gets it."
- **Error:** "Couldn't add the section. Try again." The sheet stays open.
- **Someone else changed the stack meanwhile:** add anyway at the end. Toast: "Added. Travis K. also changed the sections; here's the latest."

---

## 3. The five named modules

### 3a. Checklist
**Goal:** know who's bringing what and see what's still unclaimed, in camp, without signal.

**Body:**
- **Filter chips** (48px, optional): "All 14" · "Need someone 3" · "Mine 4". These only show when the list has 8 or more items.
- **Categories:** optional. When an item has a category, items group under `label`-style subheads, in this order: "Food", "Gear", "Camp", then any custom ones, then "Other". When no item has a category, it's a flat list with no subheads.

**Item row** (min 64px):
- Leading 48px **checkbox**. Checked means "packed / at camp". Any party member can check or uncheck.
- Text block:
  - Name (`body-lg`). When checked, add a strikethrough and switch to `text-muted`.
  - Subtitle (`caption`), one of:
    - "Travis K. is bringing it"
    - "You're bringing it"
    - "Uncle Ray is bringing it (guest)"
    - "Nobody yet" (shown with the `user-plus` icon, muted, so it never relies on color)
- Trailing slot:
  - Unclaimed: secondary button "I'll bring it" (48px).
  - Claimed: 48px `ellipsis` menu with "Someone else will bring it" (opens a picker of the party, guests included), "Nobody yet", "Edit item" and "Delete item".
  - Your own unsent changes show the per-item sync badge icon (`smartphone`).

**Add item:** an inline row at the bottom of the section.
- Field "Add an item" (`body-lg`), with help text "Like *2 bags of ice*".
- Category chips under the field when it has focus: Food · Gear · Camp · None.
- Secondary "Add". The Enter key also adds, and focus stays in the field so you can add the next one.
- New items start as "Nobody yet". There's an optional "I'll bring it" toggle row under the field.

**Sample (header: "Checklist · 9 of 14 packed · 3 need someone"):**
- FOOD
  - [x] Breakfast burritos (Sat–Sun) · You're bringing it
  - [ ] Chili fixings · Uncle Ray is bringing it (guest)
  - [ ] 2 bags of ice · Nobody yet · **I'll bring it**
  - [x] Coffee + filters · Travis K. is bringing it
- GEAR
  - [x] Rangefinder · Cody R. is bringing it
  - [ ] Drag rope + gambrel · Nobody yet · **I'll bring it**
  - [x] Extra blaze orange hats (2) · Travis K. is bringing it
- CAMP
  - [ ] Generator + gas can · Travis K. is bringing it
  - [ ] Propane (2 tanks) · Nobody yet · **I'll bring it**
  - [x] Cots · You're bringing it
- Show 4 more (collapsed after 10 items; the button is a 48px ghost)
- Add an item

**Offline:**
- Claim, check, add, edit and delete all work offline and save on the phone (`system.md` section 7).
- The section header shows "[smartphone] 2 saved on phone" until they're sent.

**Empty:**
- body "Nothing on the list yet. Add the first thing, like *ice* or *propane*."
- The add row has focus.

### 3b. Photos
**Goal:** one place to see every photo from the hunt and add your own.

**What's in it:** photos added here **plus** photos from Stories and the Harvest board (M2b). Those are shown, not copied. Each photo has one home, so deleting the post also removes its photo from here.

**Body:**
- A 3-column square grid with 4px gaps. Thumbs are at least 104px, and each one is a tap target. Dark theme: dimmed to 70%.
- Shows the first 9. Then a ghost button "See all 23 photos" opens the full-screen grid.
- Secondary `image-plus` "Add photos" (multi-select from the phone).
- Your photos that are still waiting show a small corner `smartphone` badge (`system.md` section 7 photo sub-state).

**Viewer:**
- Full screen and full brightness, with swipe left and right.
- Bottom caption: "Cody R. · Sun Nov 15, 6:48 AM". If the photo came from a post, add "· from a story" (tap to open it).
- `ellipsis` menu:
  - "Save to phone"
  - "Delete photo" (own photos only; photos from posts are deleted from the post)
- Close `x` (48px, top left).

**Builder notes:**
- Resize on the phone and strip EXIF, including location, the same as covers (`hunts.md` section 2).
- Show "Photo waits for better signal" for photos still uploading.

**States:**
- **Empty:** `images` tile · body "No photos yet. Add some from your phone." · "Add photos"
- **Offline:** "Add photos" still works and saves on the phone. Header: "23 photos · 3 waiting".
- **Cached:** thumbs only. Full-size photos that aren't saved show a `cloud-off` placeholder that says "Opens with signal".

### 3c. Pins ("Stands")
**Goal:** see which camp stands this hunt uses and who's sitting where.

**Body:**
1. **Map thumbnail**, 16:9, 12px radius:
   - Until M3 (the map): a `surface-sunken` placeholder with `map` icon and the text "Map comes before the season".
   - From M3: a static snapshot with the hunt's pins, plus a ghost button "Open map".
2. **Rows** (56px): `map-pin` · pin name · subtitle · trailing slot:
   - "Creek Bottom" · "Cody R." · `ellipsis`
   - "Ridge Ladder" · "You" · `ellipsis`
   - "Hedge Row Blind" · "Nobody yet" · secondary "I'll sit here"
   - "South Food Plot" · "Travis K." · `ellipsis`
   - Row menu: "Assign someone" (party picker, guests included), "Clear", "Take off this hunt".
3. Ghost `plus` "Add a camp stand". This opens a checklist of camp pins not yet on this hunt, with the caption "New stands are added to the camp map by Jason B. or Travis K." (D-11, D-16).

**Privacy caption** at the bottom: "[lock] Stand locations belong to Bird Creek Camp. Who's sitting where stays with the party."

**Assigned vs claimed (flag for PM):**
- "Assigned" is the **plan** for the hunt.
- During the hunt (M3), the same rows switch to **today's claims**. The header caption becomes the weather line ("NW 8 mph · 31°F · Waning crescent") and the trailing actions become Claim / Release, as in `hunts.md` section 3, item 7.
- The assignee gets the first Claim each morning (it's pre-filled), but anyone can claim an open stand.

**States:**
- **Empty:** body "No stands on this hunt yet." · "Add a camp stand"
- **Camp has no pins:** "Bird Creek Camp has no stands on the map yet. Ask Jason B. or Travis K. to add them."
- **Offline:** assign and clear save on the phone. Adding a stand needs signal.

### 3d. Regulations ("Rules")
**Goal:** know what's OK and what's not on this hunt, from state rules down to "bucks only on Ridge Ladder", before the first person climbs a stand.

**Structure:**
- Rules are organized into **topics**. Each topic has a heading and up to 3 groups:
  - **OK** (`check`)
  - **Not allowed** (`ban`)
  - **Good to know** (`info`)
- Each group is a list of short lines. The groups use subhead words **and** icons, never color alone. The "Not allowed" subhead uses `danger` text plus the `ban` icon.
- Topic footer:
  - **Source**: an optional link field. Shown as "Source: ksoutdoors.com ›", which opens in the browser.
  - **Last checked**: "Checked by Travis K. · Oct 20, 2026", with a ghost button "I checked it today" (it updates the date and the name).
- **Stale warning:** if the last check is before this season started (Aug 1), show the caption "[triangle-alert] Last checked last season. Check the current rules."

**Disclaimer** (always shown, `caption`, at the top of the body): "These are the party's notes, not legal advice. Check the official rules before you hunt."

**Sample (header: "Rules · Checked Oct 20"):**

*Topic 1: "Kansas firearm deer season"* — linked from the camp. It carries the badge "[tent] From Bird Creek Camp" and a ghost "Edit in camp".
- OK
  - "Rifle, shotgun slug, muzzleloader"
  - "Hunting from a half hour before sunrise to a half hour after sunset"
- Not allowed
  - "Hunting without blaze orange: hat + 100 sq in front and back"
  - "Shooting from a vehicle or across a road"
- Good to know
  - "Tag your deer before you move it"
- Source: ksoutdoors.com · Checked by Travis K. · Oct 20, 2026

*Topic 2: "North pasture lease"* — belongs to this hunt (party only).
- OK
  - "Does OK from every stand"
  - "ATV on the section-line road only"
- Not allowed
  - "No baiting on the lease"
  - "No bucks except from Ridge Ladder"
  - "No shooting toward the house (east)"
- Good to know
  - "Call Dale before you drive in: he's feeding cattle mornings"
- Checked by Jason B. · Oct 22, 2026

**Sample copy note:** The legal lines above are **sample copy, not verified**. The blaze orange wording should be checked by the researcher before it appears in any mock or seed data. The real content is whatever the party types.

**Editing:**
- Use the `ellipsis` on the topic to get "Edit topic", "Move up/down" and "Delete topic".
- Edit opens a full-screen form:
  - Topic name
  - 3 list editors (OK / Not allowed / Good to know), with one line per row, an "Add a line" row, and an `x` (48px) to remove a line
  - Source link
  - Primary "Save"
- Ghost `plus` "Add a topic" sits at the end of the section.
- Linked camp topics are read-only on the hunt. Their only menu items are "Edit in camp" and "Take off this hunt".

**Offline:**
- Edits save on the phone.
- Linked camp topics show the camp's copy that is cached on the phone.

**Empty:**
- body "Add what's OK and what's not, like *blaze orange* or *no baiting*."
- Ghost "Add a topic"

### 3e. Notes
**Goal:** a place for anything that doesn't fit elsewhere: meal plan, directions, who's driving.

**View mode** (default, so a gloved thumb can't edit by accident):
- Rendered text: headings (`h2`-weight 18px), bullets, bold. Links are auto-detected, and phone numbers become tappable (`tel:`).
- Footer `caption`: "Edited by Cody R. · Thu 8:14 PM", plus the sync badge if it's your unsent edit.
- Long notes are collapsed to about 12 lines, with a ghost "Show all".
- Secondary `pencil` "Edit".

**Edit mode** (full screen):
- Toolbar above the keyboard with 48px buttons: "Heading", "Bullets", "Bold", "Done".
- No other formatting. Up to 10,000 characters.
- Done saves on the phone, then sends.

**Sample** (title "Notes", header "Edited Thu"):
> **Getting there**
> - Meet at Jason's 4:30 AM Fri Nov 13, one truck + trailer
> - Gas in Salina, not after
>
> **Meals**
> - Fri: chili (Uncle Ray)
> - Sat–Sun: burritos, sandwiches at the truck
>
> **Morning plan**
> - Everybody on stand by 6:15
> - Text "on stand" in the live chat
>
> Edited by Cody R. · Thu 8:14 PM

**Empty:**
- body "A notepad for anything: plans, meals, directions."
- Secondary "Start writing"

---

## 4. Camp-level modules

**Proposal:** yes, the camp home can hold a few sections. It's where the stuff that never changes lives (gate code, landowner's number, state rules), so nobody retypes it each hunt.

**Where:** a **"Camp info"** group on the camp home.
- It sits after "Ready for camp" / "Happening now" and before "Upcoming".
- Each camp section shows as a **56px row** (icon · title · count · chevron) that opens its own page. Rows keep the home short.
- Example rows:
  - "[notebook-pen] Gate + landowner · Edited Oct 2"
  - "[scroll-text] Kansas firearm deer season · Checked Oct 20"
  - "[list-checks] Opener checklist · 18 items"

**Allowed camp types:**
- **Notes**: gate codes, landowner contact, directions.
- **Rules**: state and lease rules that apply every year.
- **Checklist template**: a starter list. Items only; there are no claims or checks at camp level.
- Not allowed at camp level: Photos, Harvest, Stories, Awards. Those belong to hunts. Stands are already the camp map (D-16).
- Max 6 camp sections.

**Who:**
- **Owner and admins** add, remove, rename and reorder camp sections. This matches D-11 for pins and keeps the home tidy.
- **Any member** edits their content. The last-edited name is always shown.
- Flag for PM: confirm with Jason.

**How hunts reuse camp sections:**

| Camp type | Reuse | Why |
|---|---|---|
| Rules | **Linked**: the hunt shows the camp topic read-only, kept up to date | State rules have one source of truth. A fix made once reaches every hunt |
| Checklist template | **Copied** when the hunt's checklist is created | Claims and checks belong to each hunt. Later edits to the template don't touch existing hunts |
| Notes | **Not reused**: stays on the camp home, one tap away | No need to duplicate a gate code |

**Privacy:**
- Camp sections are seen by **everyone in the camp**. Every camp section page and editor shows the long-form badge "[lock] Only **Bird Creek Camp** can see this."
- In the camp Notes editor, add the caption "Everyone in Bird Creek Camp sees this, even people who aren't on a hunt." That matters for gate codes and phone numbers.
- Hunt sections follow hunt sharing (party only, or the whole camp once shared). Summary viewers never see them.
- A linked camp topic inside an unshared hunt keeps its "[tent] From Bird Creek Camp" badge, so nobody thinks lease-wide rules are party secrets, or that party-only lines are seen by the camp.

**Offline:**
- Camp sections are part of the camp home cache.
- **Ready for camp** counts them: "Bird Creek Camp, camp info and Rifle Opener 2026 saved". The gate code has to work with no signal at the gate.

---

## 5. States (all sections)

| State | What shows |
|---|---|
| Empty section | The type's empty copy (section 3) plus its one add action. Never a blank card |
| Loading, no cache | Skeleton: 56px header rows for each section, with no bodies |
| Offline, cached | Normal page plus the offline banner. Content edits save on the phone. Add, remove, reorder and rename show "Needs signal" in a sheet when tapped |
| Section not cached | Body: "This section isn't saved on this phone yet. Open it once with signal." |
| **Conflict: checklist claim** | First claim to reach camp wins. The second person's row flips, with a toast: "Cody R. already grabbed *Propane*." Their claim is dropped (nothing to merge) |
| **Conflict: check / uncheck, assign** | Last change wins. No message, because these are low-stakes and easy to fix |
| **Conflict: Notes or a Rules topic edited by two people** | Nothing is lost. The later edit is saved, and the other one is kept below it in a block with a `triangle-alert` header: "Travis K. edited this at the same time (Thu 9:40 PM). Keep what you need, then tap **Done merging**." The block is visible to the party until someone taps Done merging |
| Section removed while you were editing it offline | Your edits are kept with the removed section. Banner on the hunt page: "Travis K. removed *Notes*. Your changes are saved with it. Bring it back from **Add a section**." |
| Stack changed by someone else | Refresh. Toast: "Travis K. changed the sections. Here's the latest." |
| Error loading one section | Only that card shows: "Couldn't load Checklist." with ghost "Try again". The rest of the page works |

---

## 6. Mock screens

### Mock A: Hunt page with sections (Jason, Fri Oct 23, daylight)
1. Header bar: `chevron-left` "Bird Creek Camp" · "[lock] Party only" · `pencil`
2. Cover (4:3), Rifle Opener 2026 photo
3. `h1` "Rifle Opener 2026" · "[calendar] Nov 14–22, 2026" · "[trees] North pasture lease" · caption "Starts in 22 days"
4. Share card: "[lock] Only the party can see this" · "Dale M. and Wes P. see a summary: name, dates, area, who went and the harvest count." · secondary "[tent] Share with camp"
5. `h2` "Party · 4" · ghost "Edit party" · chips: "Jason B. (you)", "Travis K.", "Cody R.", "Uncle Ray · Guest"
6. **Checklist** card (expanded): header "[list-checks] Checklist · 9 of 14 packed · 3 need someone · ⌃ · ⋯". Chips "All 14 · Need someone 3 · Mine 4". The FOOD / GEAR / CAMP sample from 3a, with three "I'll bring it" buttons, "Show 4 more" and the "Add an item" row. One of Jason's rows ("Cots") shows `smartphone` to illustrate a saved-on-phone item.
7. **Rules** card (expanded): header "[scroll-text] Rules · Checked Oct 20 · ⌃ · ⋯". Disclaimer caption. Topic "Kansas firearm deer season" with "[tent] From Bird Creek Camp" and ghost "Edit in camp". Then OK / Not allowed / Good to know lists and the source line. Then topic "North pasture lease" with its lists, "Checked by Jason B. · Oct 22, 2026" and ghost "I checked it today". Ghost "+ Add a topic".
8. **Notes** card (expanded): header "[notebook-pen] Notes · Edited Thu · ⌃ · ⋯". The 3e sample text. Footer "Edited by Cody R. · Thu 8:14 PM". Secondary "Edit".
9. **Stands** card (collapsed): "[map-pin] Stands · 4 stands · ⌄ · ⋯"
10. **Photos** card (collapsed): "[images] Photos · 0 photos · ⌄ · ⋯"
11. Secondary full-width "[plus] Add a section"
12. Freshness line: "[smartphone] Saved on this phone · updated Fri 6:12 PM"
13. Tab bar: Camp · Crew · Me (no pinned Post / Log harvest in M2a)

### Mock B: "Add a section" sheet (same hunt, M2a)
1. `h2` "Add to Rifle Opener 2026" · `x`
2. Caption "[lock] Only the party sees what you add here."
3. Rows (72px):
   - [list-checks] **Checklist**: "Who's bringing what. Works without signal." (trailing caption "1 of 3")
   - [scroll-text] **Rules**: "What's OK and what's not on this hunt." (trailing "1 of 3")
   - [notebook-pen] **Notes**: "A notepad for anything: plans, meals, directions." (trailing "1 of 3")
   - [map-pin] **Stands**: "Camp pins you're hunting and who's sitting where." (disabled, "[check] Added")
   - [images] **Photos**: "Everyone's photos from the hunt, in one place." (disabled, "[check] Added")
   - [target] **Harvest board**: "Every deer, who got it, where, and the weather." (disabled, "Coming before the season")
   - [book-open] **Stories**: "Posts and photos from camp." (disabled, "Coming before the season")
   - [trophy] **Camp awards**: "Biggest buck, worst miss, camp cook." (disabled, "Coming before the season")
4. `h2`-weight "Recently removed": "[notebook-pen] Meal plan · removed by Cody R. · Oct 21" · secondary "Bring back"
5. Footer caption "12 sections max · 5 now"

---

## 7. Changes to other specs (once approved)
- **`hunts.md` section 3:** items 7–10 become the section stack (pointer added there). The M2a "More coming" card is replaced by the default sections that exist in M2a, plus disabled "Coming before the season" rows in the Add sheet.
- **`hunts.md` 4a (share sheet):** add a row: `list-checks` "Checklists, notes and rules". Change "Who sat which stand" to "Stands and who sat where".
- **`hunts.md` section 6 (read-only):** sections show with no `ellipsis` menus, no add rows, no "I'll bring it", and no "Add a section".
- **`system.md`:** add the icons from section 0, plus a "Section card" component (1f).
- **Ready for camp:** counts camp info (section 4).

## Questions for the PM / Jason
1. Should camp sections be managed by owner and admins only, with any member editing content (my proposal), or can any member add them?
2. Is Stands "assigned" (the plan) wanted in addition to the M3 daily claims? It's a small add, but new behavior.
3. Should Photos collect story and harvest photos (my proposal), or only photos added in the Photos section?
4. M2a scope: does the section framework, plus Checklist, Notes and Rules, ship in M2a ahead of M2b? Photos depends on the upload pipeline (cover upload already exists).

## Proposals (scope; for the PM, not the builder)
1. **Forecast section** (Open-Meteo, D-6): a 3-day wind, temp and moon card for the hunt area. Needs coordinates for the hunt area, and it's online-only. Ideas list.
2. **Checklist "remind me"**: notify people with unchecked claimed items the night before. Needs notifications (M3).
3. **"Save as camp template"** from a hunt's checklist ("Make this the camp's starter list"). Small, but adds a flow.
