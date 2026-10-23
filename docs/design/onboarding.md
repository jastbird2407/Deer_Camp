# Sign-in and Join-a-Camp Flow

_Owner: designer. Task G2. Last updated 2026-10-23. Uses tokens and components from `system.md`._

**Target:** a buddy with low tech comfort goes from tapping an invite link to inside the camp in **under 2 minutes**, and stays signed in at camp with no signal.

**Decisions this spec follows (DECISIONS.md, 2026-10-23):**
- **D-2:** email plus a 6-digit code.
- **D-4:** one invite link per person, expiring in 7 days, revocable.
- Roles: owner, admin, member.

Placeholders: `{Camp}` = camp name, `{Inviter}` = display name of the person who made the invite, `{date}` = short date such as "Oct 30".

---

## 0. Shared rules for every flow
- **Screen frame:** header (back button, title, privacy badge once inside a camp) · content · **primary button pinned to the bottom** (56px).
- **One task per screen, one primary button.** Nothing on these screens needs scrolling on a 375×667 phone.
- **Offline:** any step that needs the network shows the "no signal" banner and puts its button in the "Needs signal" state. Typed input is always kept.
- **Loading:** buttons show their loading state. Full-screen spinners only on cold start, after 400ms, as a skeleton screen.
- **Stay signed in:** sessions never time out by inactivity. At camp, the app **never signs anyone out because it can't reach the server**. See flow 6.
- **Time budget (measured from tapping the link):** link opens (5s) → tap Join (5s) → type email (20s) → switch to mail, read code, come back (40s) → enter code (10s) → confirm name (10s) → inside camp. About 90 seconds. **Installing the app comes after joining and isn't counted.**

---

## Flow 1: First sign-in (no invite)
**Goal:** get into Deer Camp with my email, without a password.

**Steps:**
1. **Welcome screen:** app name, a one-line pitch, "Sign in" (primary).
2. **Email screen:** email field, "Send code".
3. **Code screen:** a single 6-digit field that auto-submits on the 6th digit. Paste and autofill both work (iOS fills codes from Mail; `autocomplete=one-time-code`).
4. **Name screen** (first time only): "What does the crew call you?", prefilled from the invite label if there is one.
5. **Then one of:** the camp home (if the person is in a camp), the camp picker (if in more than one), or the "No camp yet" screen.

**Layout (code screen):**
- Header: back button
- h1: "Check your email"
- body: "We sent a 6-digit code to **{email}**."
- Code field (`code` type style, centered)
- ghost: "Send a new code" (disabled with a countdown "in 0:45")
- ghost: "Use a different email"
- caption: "Don't see it? Check spam or junk. The email comes from Deer Camp."
- Primary (bottom): "Sign in" (only needed if auto-submit didn't fire)

**Copy:**
- Welcome h1: "Deer Camp". Body: "Your crew's private hunting yearbook." Button: "Sign in".
- Email h1: "What's your email?" Help: "We'll send you a code. No password needed." Button: "Send code".
- Name h1: "What does the crew call you?" Help: "This is how you'll show up on the roster." Button: "Done".
- No-camp screen h1: "You're not in a camp yet". Body: "Ask your camp's owner to text you an invite link. Or start your own camp." Buttons: "Start a camp" (secondary). There's no primary on this screen, because waiting for a link is the normal path.

**States:**
- **Loading:** "Send code" spins, then moves to the code screen. "Sign in" spins while checking.
- **Offline:** banner "No signal. You need signal to sign in the first time." "Send code" shows "Needs signal".
- **Invalid email:** "That doesn't look like an email address. Check for typos."
- **Wrong code:** clear the field and keep focus. "That code doesn't match. Check the newest email from Deer Camp and try again."
- **Expired code:** "That code has expired. Tap **Send a new code**." The resend button turns primary.
- **Too many tries or sends:** "Too many tries. Wait a minute, then send a new code."
- **Email never arrives:** after 60 seconds, show the caption above in `warning` tone plus "Send a new code".

---

## Flow 2: Create a camp (the creator becomes owner)
**Goal:** set up a private space for my crew.

**Steps:**
1. "Start a camp" (from the no-camp screen, or from the camp picker menu).
2. **Name screen:** camp name field (required, 2–40 characters), with privacy text.
3. "Create camp" creates the camp and makes the creator the owner in one step on the server.
4. The **camp home** opens with an "Invite your crew" card on top.

**Layout:** h1 "Name your camp" · field "Camp name" (example in help text: "Like *Bird Creek Camp* or *The Old Homestead*") · privacy card: "[lock] Your camp is private. Only people you invite can see its pins, photos, posts and messages." · Primary: "Create camp".

**Copy:** success toast: "{Camp} is ready." Empty camp home card: h2 "Invite your crew", body "Send each buddy their own link. Links work for 7 days.", button "Invite someone".

**States:**
- **Loading:** the button spins.
- **Offline:** "Create camp" shows "Needs signal". Help line: "Creating a camp needs signal, so everyone can join it."
- **Error:** "Couldn't create the camp. Try again." The name is kept.
- **Empty:** the camp home shows only the owner on the roster, plus the invite card.

**Default:** any signed-in user can create a camp, to support multiple crews later. Flagged for Jason, who may want to limit this to himself for now.

---

## Flow 3: Owner or admin invites someone
**Goal:** get a private link to one buddy by text in a few taps.

**Steps:**
1. Camp → **Crew** → "Invite someone".
2. **Invite screen:**
   - "Who's this for?" (name, required; only the camp sees it, and it prefills the buddy's display name).
   - Role: segmented control with "Member" (default) and "Admin". Admin shows only to the owner (see the schema section).
3. "Create invite link".
4. **Share screen:** the phone's share sheet opens (`navigator.share`) with prefilled text. Fallback buttons: "Text it" (`sms:` link), "Copy link".
5. Return to **Crew**, where the invite appears under "Waiting to join".

**Layout (share screen):** h1 "Send this to {name}" · card with the link (truncated) and "Expires {date}" · warning note: "[lock] Anyone with this link can join {Camp} until it's used. Send it only to {name}." · Primary: "Share link" · secondary: "Copy link".

**Prefilled message text:** "{Inviter} invited you to {Camp} on Deer Camp, our private hunting yearbook. Tap to join (works until {date}): {link}"

**Crew list, "Waiting to join" rows:** "{name}" · caption "Member · expires {date}" (in `warning` tone when less than 24 hours remain) · trailing menu: "Share again", "Cancel invite". Expired invites show as "Expired {date}" with "Send a new link". Used invites disappear and the person shows in the roster.

**Revoke:** "Cancel invite" → confirm sheet "Cancel {name}'s invite? The link will stop working." → "Cancel invite" (danger) / "Keep it". Toast: "Invite canceled."

**States:**
- **Loading:** the create button spins.
- **Offline:** "Create invite link" and "Cancel invite" show "Needs signal". Help: "Invites need signal so the link works right away." (Invites are not queued; a link that doesn't work yet would confuse people.)
- **Error:** "Couldn't make the link. Try again."
- **No permission (a member):** the "Invite someone" button is hidden. Members see a caption: "Ask {Owner} to invite new people."
- **Empty:** "No one waiting. Invite someone and their link will show here."

---

## Flow 4: Accept an invite

### 4a. Signed out (the main path)
**Goal:** tap the link from my buddy and land inside the camp.

**Steps:**
1. Tap the link → **Join preview**, loaded with only the camp name, inviter, role and expiry (no other camp data before joining).
2. "Join {Camp}" → email screen (flow 1, step 2). The invite token stays in the page's state and `sessionStorage`, so the code round trip doesn't lose it.
3. Code screen.
4. Name screen, prefilled with the name the inviter typed.
5. The invite is redeemed on the server → the **camp home**, with a welcome banner and the "Get the app" card (flow 5).

**Join preview layout:**
- Tent icon (48px)
- h1: "Join {Camp}"
- body: "**{Inviter}** invited you to their private hunting camp."
- Privacy card: "[lock] Private camp. Only members can see its pins, photos, posts and messages."
- caption: "You'll join as a **Member**. Invite expires {date}."
- Primary: "Join {Camp}"
- ghost: "Already have an account? Sign in" (same flow; it just sets expectations)

**Copy after joining:**
- Banner (success): "You're in. Welcome to {Camp}."
- Card: h2 "Get the app before you go", body "Put Deer Camp on your home screen so it works at camp with no signal.", button "Show me how".

### 4b. Already signed in
**Steps:** tap the link → join preview, with the button "Join {Camp}" and the caption "Joining as {display name} ({email})", plus ghost "Not you? Sign out". Tap Join → the camp home with the welcome banner. Two taps total.
- **Already a member:** h1 "You're already in {Camp}". Primary "Open {Camp}". The invite is **not** used up, so the owner can resend it to the right person.

### 4 — Error states (each a full screen, Tent icon swapped for `triangle-alert`)
| Case | h1 | Body | Action |
|---|---|---|---|
| Expired | "This invite expired" | "It stopped working on {date}. Ask {Inviter} to send you a new link." | none (primary hidden); ghost "Sign in" if they already have an account |
| Already used | "This invite was already used" | "Each invite works for one person. If that wasn't you, ask {Inviter} for a new link." | ghost "Sign in" |
| Revoked | "This invite was canceled" | "Ask {Inviter} to send you a new link." | ghost "Sign in" |
| Not found or mistyped | "We can't find this invite" | "The link may be incomplete. Try tapping it again from the text, or ask for a new one." | none |
| Offline on the preview | banner "No signal" | "You need signal to join. Your link still works until {date}." (If no date is cached: "Your link will still work once you have signal.") | "Try again" (Needs signal) |
| Status changed during sign-in | (redeem fails after the code) | Same screens as above. The account still exists, so the user lands on the no-camp screen next time | |

- Wrong or expired code: same handling as flow 1.
- **Showing the camp and inviter name on error screens** helps the person ask the right buddy. Only someone holding the link can see this. Flagged as a decision for the PM.

---

## Flow 5: Install the app (PWA)
**Goal:** get Deer Camp on my home screen so it opens at camp without signal.

**Entry points:** the "Get the app" card after joining (dismissible; it comes back on the next launch until the app is installed or dismissed twice) · Settings → "Install the app". Hide all of them when already installed (`display-mode: standalone` or `navigator.standalone`).

**Android (Chrome, Samsung Internet):**
1. "Show me how" → if the browser fires `beforeinstallprompt`, show a single button "Install Deer Camp" → the native prompt → done.
2. Fallback (no prompt event): a 2-step illustrated sheet: "Tap **⋮** (top right)" → "Tap **Install app** or **Add to Home screen**".
3. The installed app shares the browser's sign-in, so the user is still signed in. Toast: "Deer Camp is on your home screen."

**iPhone (Safari; Chrome on iOS 16.4+ also works through Share):**
1. "Show me how" → an illustrated sheet with large numbered steps and real icons:
   - Step 1: "Tap **Share** [square with up arrow] at the bottom. (On newer iPhones, tap **⋯** first, then **Share**.)"
   - Step 2: "Scroll down and tap **Add to Home Screen**."
   - Step 3: "Tap **Add**."
   - Step 4: "**Open Deer Camp from your home screen and sign in one more time.** iPhone keeps the home-screen app separate from Safari. You only do this once."
2. If the person is in an in-app browser (opened from the Gmail or Facebook app), first show: "Open this page in Safari first: tap **⋯** or the compass icon, then **Open in Safari**."

**Important iOS fact (verify in R3):** the home-screen app on iPhone does **not** share Safari's sign-in or storage. So on iPhone:
- The invite is redeemed in Safari (step 4a). Membership lives on the server, so nothing is lost.
- The first launch from the home screen shows the email screen, with the heading "Welcome back. Sign in once more to finish setup." (Detect a standalone first launch with no session.) It takes about 30 seconds with code autofill.
- Data saved in Safari's storage is lost after 7 days of no use. The installed app is exempt, which is a further reason to install.

**States:**
- **Offline:** installing works offline, but the first sign-in inside the iPhone app needs signal. Card copy: "Do this before you leave town. Signing in the first time needs signal."
- **Error:** none. The OS handles install, and our sheet is only instructions.

---

## Flow 6: Open the app at camp with no signal
**Goal:** open the app and see my camp, even with zero bars.

**Steps and behavior:**
1. The app shell loads from the service worker cache.
2. The session is read from the device. **If the access token has expired and refresh fails because there's no network, the user stays signed in locally.** No error is shown.
3. The camp home shows data saved on the phone, with the offline banner.
4. New messages, stand claims and notes are saved on the phone (sync badge: "Saved on phone") and send when signal returns (system.md section 7).

**Layout:** normal camp home + banner (warning tone): "[cloud-off] No signal. Showing what's saved on this phone (updated {Fri 6:12 PM}). New posts will send when you're back in range." The header sync badge shows "{n} waiting" once anything is saved.

**States:**
| Case | What the user sees |
|---|---|
| Signed in, data cached | Camp home + offline banner (above) |
| Signed in, nothing cached yet | Empty state: h1 "Nothing saved on this phone yet", body "Open Deer Camp once with signal so it can download your camp." |
| Signed out, offline | Sign-in screen + banner "No signal. You need signal to sign in. Next time, sign in before you head to camp." Button shows "Needs signal" |
| Signal returns | Banner changes to "Back in range. Sending {n}…" → toast "All sent to camp." The banner clears |
| Back online but the session was revoked or has expired on the server | Banner (danger): "Please sign in again. Everything saved on this phone is still here." Sign-in → queued items send. Queued items are held on the device and sent **only for the same user** |
| Removed from the camp while away | Full screen: "You're no longer in {Camp}. {n} things you saved didn't send." Button "OK". Local camp data is cleared |

**Builder notes:**
- Turn off Supabase "time-box user sessions" and "inactivity timeout". Never call `signOut` on a network error.
- Cache the camp data needed for the camp home and roster after every successful online load.

---

## Flow 7: Sign out
**Goal:** sign out of this phone, for example to hand it to someone else.

**Steps:** Settings → "Sign out" (danger, ghost) → confirm sheet → sign out of **this device only** (Supabase `scope: 'local'`) → **clear camp data cached on the phone** (pins are private) → welcome screen.

**Confirm sheet copy:**
- **Nothing waiting:** h2 "Sign out of Deer Camp?" body "Your camp's pins, photos and posts will be removed from this phone. Sign back in any time with your email." Buttons: "Sign out" (danger) / "Cancel".
- **Items waiting:** h2 "{n} things haven't sent yet". Body: "If you sign out now, they'll be deleted from this phone. Wait until you have signal to send them first." Buttons: "Stay signed in" (primary) / "Sign out and delete them" (danger).
- **Offline:** signing out still works locally. Add the line: "You'll need signal to sign back in."

**States:**
- **Loading:** brief spinner while local data clears.
- **Error:** if the server revoke fails, sign out locally anyway with no message. The session expires on the server later.

---

## What changes with other sign-in methods (D-2 alternatives)
- **Magic link:** removes the code screen, but the link opens in the phone's default browser or the mail app's own browser, **not the installed iPhone app**, so the user lands signed in in the wrong place. The invite token must be carried through the redirect URL. "Check your email" copy changes to "Tap the link in the email". Expect more support questions.
- **SMS code:** same code screen (SMS autofill is the smoothest on both platforms). The email field becomes a phone field. It costs money per text and needs a Twilio (or similar) account. A `phone` field is needed on the account. No spam-folder problem.
- **Google:** one button, no code. It leaves out buddies without Google accounts, so email code would still be needed as a fallback, which means two paths on every screen. Google sign-in inside the installed iPhone app can bounce out to Safari. Not recommended as the only option.

---

## Schema needs (for B2)
**Tables:**
- `profiles`: `user_id` (primary key, references `auth.users`), `display_name` (required, 1–30 characters).
- `camps`: `id` (uuid), `name` (2–40 characters), `created_by`, `created_at`.
- `camp_members`: `camp_id`, `user_id`, `role` (`owner` | `admin` | `member`), `joined_at`, `invited_by` (nullable), `invite_id` (nullable).
  - Unique (`camp_id`, `user_id`).
  - **Exactly one owner per camp** (partial unique index on `camp_id` where `role = 'owner'`).
- `camp_invites`:
  - `id`, `camp_id`, `token_hash` (unique; **store only a hash**, show the raw token once).
  - `role` (`member` | `admin`; never `owner`), `invitee_name` (required), `created_by`, `created_at`.
  - `expires_at` (default `now() + 7 days`), `used_at`, `used_by`, `revoked_at`, `revoked_by`.

**Rules:**
- **Single use.** An invite becomes used when it is redeemed.
- **7-day expiry.** Status is computed in this order: revoked > used > expired > pending.
- **Who can invite:** the owner and admins. **The owner can grant `admin` or `member`; admins can grant only `member`.** (This is a design default; flag if B2 differs.)
- **Who can revoke:** the owner and admins, for any pending invite in their camp.
- **RLS:** the owner and admins of that camp can read invites. Members can't see invites at all. No one can read `token_hash` through the API.

**Functions (security definer):**
- `create_camp(name)` → creates the camp and the owner membership in one transaction.
- `create_invite(camp_id, role, invitee_name)` → returns `{invite_id, token, expires_at}`. Checks the role rule above.
- `preview_invite(token)` → **callable while signed out**, rate-limited. Returns **only** `camp_name`, `inviter_display_name`, `role`, `expires_at` and `status` (`pending` | `expired` | `used` | `revoked` | `not_found`). No camp IDs, member lists or pins.
- `redeem_invite(token)` → signed in only. Locks the row, checks the status, inserts the membership and marks the invite used, all in one transaction.
  - Returns `ok` | `already_member` | `expired` | `used` | `revoked` | `not_found`.
  - **`already_member` does not use up the invite.**
- `revoke_invite(invite_id)`.
- Profile upsert for `display_name` on first sign-in.

**Possible differences from the builder (flag to the PM):**
- `preview_invite` for signed-out users.
- The admin-can't-grant-admin rule.
- `already_member` not using up the invite.
- `invitee_name` on invites.
- The specific error codes above.

**Auth configuration:**
- The email OTP template must include `{{ .Token }}` (a 6-digit code), not only a link.
- Code lifetime: 1 hour or less.
- Resend cooldown: 60 seconds.
- Keep the session settings described in flow 6.

---

## Proposals (scope expansions, for the PM; not for the builder)
1. **"Ready for camp" checklist** (Settings): app installed · signed in in the app · camp downloaded {time} · nothing waiting to send. This directly supports the offline goal.
2. **Email-bound invites:** an optional "their email" field so only that address can redeem. Tighter security, but it adds a step and a failure mode (the wrong email).
3. **Link a guest roster entry on join** (D-7): when inviting, pick an existing name-only guest so their past harvests carry over to the new account.
4. **Member management** (change role, remove member, transfer ownership). This is not in the G2 scope, but it's needed before other crews use the app.

## Open questions
1. **Jason action (verify):** Supabase's built-in email service is heavily rate-limited and may send only to the project team's addresses. Real invites likely need a custom SMTP provider (for example Resend's free tier) and a sender domain before M2. Confirm in R3 or with the builder.
2. Should any signed-in user be able to create a camp, or only Jason for now?
3. Can admins create admin invites? (Default: no, owner only.)
4. Is it OK to show the camp name and inviter on the signed-out preview and error screens? (Default: yes, to link holders only.)
5. The iPhone "sign in twice" step (flow 5) is the biggest risk to the 2-minute and low-tech goals. R3 should confirm current iOS behavior. If the iOS storage split is confirmed, consider telling iPhone users to install first, then open the invite. However, a link opened later still lands in Safari, so the current order is the more reliable one.
