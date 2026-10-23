# Camp Home, Hunts and Parties

_Owner: designer. Task G4 (M2a). Last updated 2026-10-23. Replaces the year-based screens in `trip-page.md` (sections 1, 3, 4, 5). Crew, "Ready for camp", camp picker and Me in `trip-page.md` still apply. Tokens and components: `system.md`. Sign-in and invites: `onboarding.md`._

**Decisions followed:** D-13 (camp → hunts → party) · D-14 (summary only until shared) · D-15 (any party member shares) · D-16 (pins belong to the camp) · D-18 (names: **Camp**, **Hunt**) · D-19 (photo import parked, not in this spec) · D-7 (name-only guests).

## Assumptions (Q1 to Q4 not answered yet; PM defaults used)
- **A1 (Q1) Summary** = hunt name, dates, area name, party names (members and guests), harvest count, shared yes/no. Nothing else: no cover, coordinates, photos, stories, harvest details, awards, stands or chat.
- **A2 (Q2)** Any camp member can create a hunt and is put in its party.
- **A3 (Q3)** Any party member can share and unshare.
- **A4 (Q4)** Owner and admins see only the summary of unshared hunts they weren't on.
- **A5 (designer)** A **season** runs Aug 1 to Jul 31, so a spring turkey hunt sits with the fall before it. Season label: "2025–26 season". Change if Jason prefers calendar years.
- **A6 (designer)** M2a editing needs signal (same rule as `trip-page.md` section 0). Create hunt, edit, party changes, share and unshare all show "Needs signal" offline.

## Sample data (same as the Claude Design canvas)
- Camp **Bird Creek Camp**. Members: Jason B. (owner), Travis K. (admin), Cody R., Dale M., Wes P. Guest: Uncle Ray. Today: Fri Oct 23, 2026.
- **Rifle Opener 2026**: Nov 14–22, 2026 · North pasture lease · party Jason B., Travis K., Cody R., Uncle Ray (guest) · **not shared** · created by Jason B.
- **Turkey Opener**: Apr 11–13, 2026 · Walnut Creek ground · party Wes P., Travis K. · **not shared** · 1 harvest. Jason sees the summary.
- **Rifle Opener 2025**: Nov 15–23, 2025 · North pasture lease · party Jason B., Travis K., Cody R., Dale M., Uncle Ray · **shared** by Travis K. on Dec 2, 2025 · 3 harvests. Wes P. sees it read-only.
- Stand pins (camp level, D-16): Creek Bottom, Ridge Ladder, Hedge Row Blind, South Food Plot.

---

## 0. Shared rules

**Two privacy layers, one badge per screen.**
- Camp screens (camp home, Crew, Me): header badge "[lock] Camp only" (unchanged).
- Hunt screens: the header badge is the **hunt badge** instead, because it is the more specific promise:
  - Unshared: `lock` + "Party only"
  - Shared: `tent` + "Shared with camp"
  - Different icon **and** word, never color alone. Same badge on every hunt card on the camp home.
- Long form (first view, composers, share sheets): "[lock] Only the party can see this." / "[tent] Everyone in **Bird Creek Camp** can see this."

**Three ways to see a hunt** (decided by the server; the app never shows a control it would refuse):
| Viewer | Unshared hunt | Shared hunt |
|---|---|---|
| In the party | Party view (section 3) | Party view, badge "Shared with camp" |
| Not in the party | Summary view (section 5) | Read-only view (section 6) |

- Party-gated controls (edit, Edit party, share, Post, Log harvest, Claim) are **hidden** for non-party viewers, not disabled.
- Party names always show, so nobody hits a dead end: there is always a named person to ask.

**Words:** "hunt", "party", "camp", "guest". Not "trip", "group", "roster", "members only". "Share with camp", never "publish" or "make public".

**Dates:** "Nov 14–22, 2026"; across months "Mar 30 – Apr 2, 2026"; single day "Nov 14, 2026". Always with the year, since hunts span years.

**Freshness line, offline banner, loading skeletons, load error:** as `trip-page.md` section 0.

---

## 1. Camp home

**User goal:** see every hunt in my camp, get to mine in one tap, and know which ones I can't see in full.

**Layout (top to bottom):**
1. **Header bar:** "Bird Creek Camp" (`h2`, chevron only if in 2+ camps) · "[lock] Camp only" · sync badge (only when something is waiting).
2. **Banners** (only when they apply): offline · welcome after joining · "Please sign in again".
3. **Ready for camp** card (section 1a). Collapses to a 56px row "[circle-check] Ready for camp ›" when all done.
4. **Invite your crew** card: owner and admins only, while the owner is the only member (`onboarding.md` flow 2).
5. **Happening now** (only during a hunt the viewer can read; replaces "Upcoming" for that hunt): big full card (1b) with a Live strip on top: "[radio] Live now · Day 3 of 9" (M3 adds "· 3 on stand").
6. **Upcoming**: `h2` "Upcoming". Big full cards, soonest first.
7. **This season**: `h2` "This season (2026–27)". Hunts that ended since Aug 1, newest first. Compact cards. Hidden when empty.
8. **Past seasons**: one `h2` per season, newest first: "2025–26 season", "2024–25 season". Compact cards and summary cards, newest first.
9. **Freshness line:** "[smartphone] Saved on this phone · updated Fri 6:12 PM".
10. **Pinned primary** (every member): `plus` "Add a hunt".
11. Bottom tab bar: Camp · Crew · Me.

**Sample screen (Jason, Oct 23):** Ready for camp card · Upcoming: Rifle Opener 2026 (big full card) · 2025–26 season: Turkey Opener (summary card), Rifle Opener 2025 (compact full card) · freshness line · "Add a hunt". ("This season" is empty on Oct 23, so it's hidden.)

### 1a. Ready for camp card (update to `trip-page.md` section 6)
- Same rows as before. The **saved** row now counts hunts:
  - Done when: camp home, all summaries, and every **upcoming or happening** hunt the user can read (party data, cover, chat cache in M3) are on the phone, and storage is kept.
  - Done text: "Bird Creek Camp and Rifle Opener 2026 saved · updated Fri 6:12 PM". With 2+ hunts: "Bird Creek Camp and 2 hunts saved · …".
  - To-do text: "Rifle Opener 2026 isn't saved yet" · button "Save now" (Needs signal offline).
- Past hunts are saved once opened (B12); they don't count toward "ready".

### 1b. Full card, big (upcoming / happening; viewer is in the party, or the hunt is shared)
- Cover 16:9, 12px top radius. No cover: `surface-sunken` block with `tent` icon and the hunt name (never a broken image). Dark theme: dimmed 70%.
- Badge row: hunt badge ("[lock] Party only") · right side, `caption`: "Starts in 22 days" / "Day 3 of 9" / "Starts tomorrow".
- `h2` "Rifle Opener 2026"
- `body` "[calendar] Nov 14–22, 2026 · [trees] North pasture lease"
- `caption` "Jason B. (you), Travis K., Cody R., Uncle Ray"
- If shared but not in the party: extra `caption` "You weren't on this hunt. Read only."
- Whole card is one tap target to the hunt page.

### 1c. Full card, compact (past, viewer can read it)
- Row card, min 88px: 64px square cover thumb (or `tent` tile) · `h2`-weight name "Rifle Opener 2025" · `caption` "Nov 15–23, 2025 · North pasture lease" · `caption` "5 in party · 3 harvests" · hunt badge "[tent] Shared with camp" under the text.
- Chevron trailing.

### 1d. Summary card (not in the party, not shared)
Visibly different without color: **dashed** 1.5px border, `surface-sunken` fill, **no cover or thumb**, a 64px lock tile instead.
- Row: 64px `surface-sunken` tile with `lock` icon (24px) · text block:
  - Name "Turkey Opener" (`h2` weight)
  - `caption` "Apr 11–13, 2026 · Walnut Creek ground"
  - `caption` "Wes P., Travis K. · 1 harvest"
  - Badge "[lock] Party only"
  - `caption` (`text-muted`): "[eye-off] Summary only · Ask Wes P. or Travis K. to share"
- Chevron trailing; tap opens the summary view (section 5).
- "Ask" name rule: 1 member → "Ask Wes P. to share"; 2 → "Ask Wes P. or Travis K. to share"; 3+ → "Ask Wes P. or anyone in the party to share" (first name = creator if still in the party, else first member A–Z). Guests are never named as the person to ask.
- Harvest count: hidden in M2a (harvests ship in M2b); shows "0 harvests" only after M2b.

### States
| State | What shows |
|---|---|
| Empty (no hunts) | Card: `h2` "No hunts yet" · body "Add this fall's hunt, or one from years back. You pick who went." · the pinned "Add a hunt" stays as the only primary |
| Only summaries (user on no hunt yet) | Normal list. No extra copy; summary cards carry their own hint |
| Loading, no cache | Skeleton: header, one tall card, three 88px rows |
| Offline, cached | Normal + offline banner. "Add a hunt" shows "Needs signal" |
| Offline, nothing cached | `onboarding.md` flow 6 empty state |
| Error, no cache | `trip-page.md` section 0 load error ("Couldn't load Bird Creek Camp") |
| Hunt unshared while cached | At next sync the full card turns into a summary card; cached details are purged. No toast |
| Added to a party by someone else | Card turns into a full card at next sync. One-time info banner: "Travis K. added you to Turkey Opener." |

---

## 2. Create a hunt (any member)

**User goal:** set up a hunt, current or from years back, in under a minute, and pick who went.

**Entry:** camp home "Add a hunt". Edit uses the same form from the hunt page `pencil` (party only), titled "Edit hunt", primary "Save changes", and skips step 2.

### Step 1 of 2: "Add a hunt"
Header: close (`x`) · title "Add a hunt" · caption "Step 1 of 2".
1. **Hunt name** (required, 2–60 characters). Help: "Like *Rifle Opener 2026* or *Uncle Ray's bow hunt*."
2. **First day** (required, date). **Last day** (optional). Help under both: "Past dates are fine. Add hunts from years back."
3. **Area** (optional, up to 80 characters). Help: "Like *North pasture lease*. **Everyone in Bird Creek Camp sees the area name**, even before you share." (It is in the summary; say so here.)
4. **Cover photo** (optional): 16:9 well, `image-plus` + "Add cover photo". After pick: preview + ghost "Change" / ghost "Remove". Help: "Only the party sees the cover until you share."
5. Privacy card: "[lock] **Only the party sees this hunt.** The rest of Bird Creek Camp sees just the name, dates, area, who went and the harvest count. You can share it with the camp later."
6. Pinned primary: "Next: who went?" (future first day: "Next: who's going?").

### Step 2 of 2: "Who went?" / "Who's going?"
Header: back · title "Who went?" · caption "Step 2 of 2".
1. Body: "Everyone you add sees all of Rifle Opener 2026."
2. `h2` "From Bird Creek Camp". Checklist rows (56px, checkbox left, whole row taps): Jason B. (you), Travis K., Cody R., Dale M., Wes P.
   - **Default: only the creator is checked.** The creator's row is checked and locked with caption "You're in the party." (He can leave later from Edit party if someone else is in it.)
3. `h2` "Guests". Body: "For someone who came but isn't on the app."
   - Chips (48px) of past guests in this camp: "+ Uncle Ray". Tap toggles it into the list.
   - Field "Guest's name" (1–40) + secondary "Add guest".
   - Added guests show as rows with outline `user` icon, "Guest" subtitle, trailing `x` (48px).
4. Caption: "[lock] Guest names stay inside Bird Creek Camp."
5. Pinned primary: "Create hunt · 4 people" (count updates).

**After create:** hunt page opens (party view). Toast: "Rifle Opener 2026 is set up. Only the party can see it." The new hunt also appears on the camp home.

**States and copy:**
- **Loading:** primary spins; with a cover, caption "Uploading photo…".
- **Offline:** primary "Needs signal". Help: "Adding a hunt needs signal so the party gets it."
- **Name empty:** "Give the hunt a name."
- **Last day before first day:** "The last day is before the first day."
- **First day more than 1 year ahead:** "That's over a year out. Check the date."
- **Same name and first day already exist in the camp** (and the user can read it): "There's already a Rifle Opener 2026 starting Nov 14." + ghost "Open it". If the user can only see its summary: "Bird Creek Camp already has a hunt with that name and date, run by Jason B. Ask to be added to its party." Primary stays enabled (it's a warning, not a block).
- **Photo not usable:** "That file isn't a photo we can use. Try a different one."
- **Photo upload failed, hunt saved:** hunt opens; cover well shows "[triangle-alert] Photo didn't upload. Tap to retry."
- **Save error:** danger banner "Couldn't add the hunt. Check your signal and try again." All fields kept.
- **Builder notes:** cover resized on the phone and EXIF stripped (as `trip-page.md` section 4). Hunt is created unshared with the creator in the party in one server call.

---

## 3. Hunt page, party view

**User goal:** everything about my hunt on one screen: who's here, where they're sitting, what got shot, and the stories.

> **Update (D-20, pending Jason's approval):** Items 1–6 below are the fixed header. Items 7–10 (Stands today, Harvest board, Stories, Camp awards) become **sections (modules)** in an ordered, editable stack, alongside Checklist, Photos, Rules and Notes. Live hunt stays fixed; weather stays a caption. See `docs/design/modules.md`.

Sample: Jason viewing Rifle Opener 2026. Sections marked **(M2b)** / **(M3)** are mocked as future states; in M2a they collapse to one placeholder card (see States).

**Layout (top to bottom):**
1. **Header bar:** back `chevron-left` "Bird Creek Camp" · hunt badge "[lock] Party only" · sync badge (when waiting) · `pencil` icon button (aria-label "Edit Rifle Opener 2026").
2. **Live hunt bar (M3)**, only while the hunt is happening and the viewer is in the party: 56px full-width `accent` bar, sticky under the header: "[radio] Live hunt · 3 on stand · 2 new" + chevron. Tap opens the hunt chat with quick replies ("On stand", "Deer moving", "Need help"). Not shown before or after the hunt.
3. **Cover:** full width 4:3. No cover: `surface-sunken` block with `tent` and ghost "Add cover photo".
4. **Title block:** `h1` "Rifle Opener 2026" · `body-lg` "[calendar] Nov 14–22, 2026" · `body-lg` "[trees] North pasture lease" · `caption` "Starts in 22 days" / "Day 3 of 9" / "Ended Nov 22".
5. **Share card** (unshared only):
   - `lock` + `h2` "Only the party can see this"
   - body "Dale M. and Wes P. see a summary: name, dates, area, who went and the harvest count."
   - secondary button `tent` "Share with camp" (opens 4a).
   - After the last day, the card leads with: body "Hunt's over. Want the rest of the camp to see it?" (same button).
   - When shared, the card is replaced by a 56px row: "[tent] Shared with camp by Travis K. · Dec 2" · ghost "Unshare" (opens 4b).
6. **Party:** `h2` "Party · 4" + trailing ghost "Edit party" (opens section 7). Chips (48px, wrap): initials avatar + "Jason B. (you)", "Travis K.", "Cody R.", outline `user` + "Uncle Ray · Guest". Tap a member chip opens the member sheet (`trip-page.md` section 7).
7. **Stands today (M3):** `h2` "Stands today" + `caption` weather line "NW 8 mph · 31°F · Waning crescent". Rows (56px): `map-pin` · pin name · subtitle status · trailing action:
   - "Creek Bottom" · "Cody R." · none
   - "Ridge Ladder" · "You" · ghost "Release"
   - "Hedge Row Blind" · "Open" · secondary "Claim"
   - "South Food Plot" · "Taken" (held by another party; no names, per ROADMAP M3 note) · none
   - Own claims show the sync badge ("Saved on phone" until sent).
8. **Harvest board (M2b):** `h2` "Harvest board · 2". Rows (88px): 64px photo thumb · "Travis K. · 8-point buck" · `caption` "Sun Nov 15 · Ridge Ladder" · `caption` "NW 8 mph · 31°F · Waning crescent" · sync badge on own entries. Empty: "No harvests yet. Tap **Log harvest** when someone connects."
9. **Stories (M2b):** `h2` "Stories". Post cards: author, time, text, photo (dimmed in dark), sync badge on own. Empty: "No stories yet. Post the first one."
10. **Camp awards (M2b):** `h2` "Camp awards". Rows: `trophy` · "Biggest Buck" · "Travis K."; "Worst Miss" · "Cody R."; "Camp Cook" · "Uncle Ray". Empty: "Awards get handed out at the end of the hunt."
11. Freshness line.
12. **Pinned actions (M2b):** two buttons side by side, 56px, 8px gap: secondary `pencil-line` "Post" · primary `plus` "Log harvest". Composers carry the long-form hunt badge.

**States:**
| State | What shows |
|---|---|
| M2a (before M2b/M3) | Sections 7–10 replaced by one muted card: `h2` "More coming" · "Harvests, stories, stands and camp awards come before the season." No pinned actions |
| Loading, no cache | Skeleton cover, two lines, chip row |
| Offline, cached | Normal + offline banner. Edit, Edit party, Share show "Needs signal" in a sheet when tapped. Post / Log harvest / Claim work and save on the phone (M2b/M3) |
| Offline, not cached | Body: "Rifle Opener 2026 isn't saved on this phone yet. Open it once with signal." |
| Error | Load error, title "Couldn't load Rifle Opener 2026" |
| Removed from the party while away | At next sync: unshared → page switches to summary view with info banner "You're no longer in the Rifle Opener 2026 party." Items saved on the phone for this hunt that didn't send: "{n} things you saved didn't send." |
| Unshared by another party member | Badge flips to "Party only"; share card returns. No banner (party view is unchanged for party members) |

---

## 4. Share and unshare

### 4a. Share confirm sheet
- `h2` "Share Rifle Opener 2026 with Bird Creek Camp?"
- body "Everyone in the camp will be able to see:"
- list (icon + text, 48px rows, not tappable): `image` "Photos and stories" · `target` "Harvests and weather" · `trophy` "Camp awards" · `map-pin` "Who sat which stand" · `message-circle` "Live hunt chat"
- body "Dale M. and Wes P. can read it but can't post. Anyone in the party can unshare it later."
- Primary `tent` "Share with camp" · ghost "Not now".
- **After:** badge flips to "[tent] Shared with camp"; share card becomes the "Shared with camp by Jason B. · Oct 23" row. Toast "Shared with Bird Creek Camp."
- **Offline:** primary "Needs signal". Help "Sharing needs signal so the camp gets it right away."
- **Error:** "Couldn't share. Check your signal and try again." Sheet stays open.
- **Already shared by someone else:** close the sheet, refresh, toast "Travis K. already shared it."
- **Party is the whole camp** (nobody else to share with): hide the share card; caption under Party: "Everyone in Bird Creek Camp is in this party."

### 4b. Unshare confirm sheet
- `h2` "Make Rifle Opener 2025 party only?"
- body "Wes P. will go back to seeing just the summary. The photos, stories and harvests leave his phone the next time it has signal."
- caption "Anything already seen or screenshotted can't be taken back."
- Primary `lock` "Make party only" · ghost "Keep it shared".
- **After:** badge "[lock] Party only"; share card returns. Toast "Rifle Opener 2025 is party only again."
- Offline / error / already unshared: same pattern as 4a ("Couldn't unshare…", "Jason B. already made it party only.").

---

## 5. Hunt summary view (not in the party, not shared)

**User goal:** know the hunt happened and who to ask, without seeing the party's private stuff.

Sample: Jason opens Turkey Opener.

**Layout:**
1. **Header bar:** back "Bird Creek Camp" · hunt badge "[lock] Party only". No edit, no sync badge for this hunt.
2. **No cover block** (not even a placeholder, so nothing looks broken or hidden-behind-a-blur).
3. **Title block:** `h1` "Turkey Opener" · "[calendar] Apr 11–13, 2026" · "[trees] Walnut Creek ground".
4. **Party:** `h2` "Party · 2". Chips: "Wes P.", "Travis K." (guests shown as "Name · Guest"). Member chips open the member sheet.
5. **Harvests (M2b on):** `h2`-weight row "[target] 1 harvest". No details.
6. **Privacy card** (dashed border, `surface-sunken`):
   - `eye-off` + `h2` "You're seeing the summary"
   - body "This hunt is private to its party. Photos, stories, harvests and chat stay with Wes P. and Travis K. unless one of them shares it with the camp."
   - body "**Ask Wes P. or Travis K. to share it.**"
   - caption "Were you on this hunt? Ask them to add you to the party."
7. Freshness line.
- No pinned action. No buttons that send requests (in-app "ask to share" would be new scope; see Proposals).

**States:**
- **Offline:** works fully from cache (summaries are part of the camp home cache).
- **Shared while open:** at next sync the page switches to the read-only view (section 6) with toast "Wes P. shared Turkey Opener with the camp."
- **Added to the party while open:** switches to party view with info banner "Travis K. added you to Turkey Opener."
- **Error:** the summary comes with the camp home, so there is no separate load error.

---

## 6. Shared hunt, non-party view (read-only)

Sample: Wes P. opens Rifle Opener 2025.

**Same layout as section 3, with these changes:**
- Header: hunt badge "[tent] Shared with camp". **No** `pencil`.
- **No** Live hunt bar input. During a live shared hunt (M3), the bar reads "[radio] Live hunt · read only" and opens the chat without the composer or quick replies.
- Share card replaced by an info row (56px): "[tent] Shared by Travis K. · Dec 2, 2025. You weren't on this hunt, so you can read but not post."
- Party: no "Edit party".
- Stands today (M3): names shown, **no** Claim / Release.
- Harvest board, stories, awards: fully readable, no add or react controls.
- **No pinned Post / Log harvest.** The bottom is empty (the tab bar stays).

**States:**
- **Unshared while open or cached:** at next sync the page switches to the summary view, with info banner "This hunt is party only again." Cached details are purged.
- Offline, error, loading: as section 3.

---

## 7. Edit party sheet (party members only)

**User goal:** fix who went: add the buddy we forgot, add Uncle Ray, take off someone who didn't make it.

**Entry:** hunt page "Edit party". Full-height sheet.

**Layout:**
1. `h2` "Party for Rifle Opener 2026" · close `x`.
2. caption (unshared): "[lock] Everyone in the party sees all of this hunt." (Shared: "[tent] This hunt is shared with the camp.")
3. **In the party** (`h2`-weight "In the party · 4"): rows (56px): avatar · name · subtitle "Owner" / "Admin" / "Member" / "Guest" · trailing 48px `ellipsis` menu:
   - Member: "Take off this hunt"
   - Guest: "Edit name", "Take off this hunt"
   - Self: "Leave this party"
   - **Last member account** (guests don't count): no menu; caption under the row "A hunt needs at least one Bird Creek Camp member in its party."
4. **Add from camp** (`h2`-weight): checklist of camp members not in the party: "Dale M.", "Wes P.". Caption: "People you add see everything on this hunt." Secondary "Add 1" (count updates). Empty: "Everyone in Bird Creek Camp is already in this party."
5. **Add a guest:** past guest chips + "Guest's name" field + secondary "Add guest" (as section 2, step 2).
6. No pinned primary: each action saves on its own, with a toast. Close returns to the hunt page.

**Confirm sheets:**
- **Take off (member):** `h2` "Take Cody R. off Rifle Opener 2026?" · body "Cody R. stays in Bird Creek Camp. He'll see only the summary of this hunt unless it's shared." (M2b adds: "His 1 harvest and 2 stories stay on the hunt.") · danger "Take off" / ghost "Keep Cody R.".
  - Shared hunt variant body: "Cody R. stays in Bird Creek Camp and can still read this hunt, because it's shared. He won't be able to post."
- **Take off (guest):** `h2` "Take Uncle Ray off Rifle Opener 2026?" · danger "Take off" / ghost "Keep".
- **Leave this party (self):** `h2` "Leave the Rifle Opener 2026 party?" · body (unshared) "You'll see only the summary after this. Someone in the party would have to add you back." · danger "Leave party" / ghost "Stay".
- Toasts: "Dale M. added to Rifle Opener 2026." · "Cody R. taken off Rifle Opener 2026." · "Uncle Ray added."

**States:**
- **Offline:** all actions show "Needs signal".
- **Duplicate guest:** "Uncle Ray is already in this party."
- **Empty name:** "Type a name."
- **Error:** "Couldn't update the party. Try again." Sheet stays open, input kept.
- **Changed by someone else:** refresh; toast "The party changed. Here's the latest."

---

## Screens to mock (Claude Design)
1. Camp home, Jason, Oct 23 (Ready card, Rifle Opener 2026 big card, Turkey Opener summary card, Rifle Opener 2025 compact card, "Add a hunt").
2. Camp home, Nov 16 (Happening now with Live strip). Daylight and pre-dawn.
3. Camp home, empty ("No hunts yet").
4. Create hunt, step 1 (filled with Rifle Opener 2026).
5. Create hunt, step 2 "Who's going?" (Jason checked, Travis and Cody checked, Uncle Ray chip added).
6. Hunt page, party view, unshared (refine the existing canvas screen to this spec), daylight; plus the M2a variant with the "More coming" card.
7. Hunt page, party view, live (Nov 16), pre-dawn theme.
8. Share confirm sheet.
9. Hunt page, party view, shared: Jason on Rifle Opener 2025 (badge "Shared with camp", row "Shared with camp by Travis K. · Dec 2").
10. Unshare confirm sheet.
11. Summary view (Jason on Turkey Opener).
12. Read-only shared view (Wes P. on Rifle Opener 2025).
13. Edit party sheet, plus the "Take off" and "Leave this party" confirms.

---

## Revisited from `trip-page.md` open questions
- **Q1 (members add themselves):** settled by the model. Any party member manages the party; a non-party member can't add himself and is told whom to ask.
- **Q4 (delete):** still no delete in M2a. With any member able to create hunts, duplicates and test hunts are more likely. **Recommend** to the PM: "Delete hunt" for a party member when the hunt has no harvests, posts or awards. Until then, fix by editing.
- **Q6 (removed members show as "Guest"):** still true on parties, and now on summaries too. Still low priority.

## Proposals (for the PM, not the builder)
1. **"Ask to share" / "Ask to join" button** on the summary view that notifies the party. Useful, but it needs notifications (M3). For now the copy names who to ask.
2. **Share nudge** after a hunt ends: a one-time banner on the camp home for party members, "Rifle Opener 2026 is over. Share it with the camp?" The share card already does this on the hunt page; the banner would be extra.

## Notes for the PM / builder
- Area name and hunt name are in the summary, so the create form says the camp sees them. If Jason wants the area private too, drop it from Q1 and change the help text.
- `hunt_summaries` must also return the **creator** (or party order) for the "Ask {name}" hint, and whether the viewer is in the party is implied by which view the app gets.
- The Turkey Opener card shows "1 harvest" only after M2b.
- Two hunts at the same time (rare): each party sees only its own Live bar and chat; the other party's held stands show "Taken" with no names.
