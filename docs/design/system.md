# Deer Camp Design System (v0.1)

_Owner: designer. Task G1. Last updated 2026-10-23._

Rugged, warm and outdoor, but clean and modern: think canvas, timber and blaze orange on a well-set table, not camo. Flat surfaces, few lines, big type, no textures or gradients.

---

## 1. Principles
- **Glance first.** The live-hunt side has to be readable in one second at arm's length, in gloves.
- **One thumb.** Primary actions sit in the bottom third of the screen, full width.
- **Never color alone.** Every status gets an icon and a word, not just a color. This matters even more in the dark theme, which is nearly monochrome.
- **Always say where data is.** Every item a user creates shows whether it is on the phone or sent to camp (section 7).
- **Privacy is always visible.** Camp data carries a lock and the camp's name, never a hidden setting.

---

## 2. Themes and color tokens

Two themes. **Default: follow the phone's light or dark setting**, with a manual Sun/Moon toggle in the header menu that overrides it and is remembered on the device.

Components use **semantic tokens only** (no raw hex in components). The builder exposes them as CSS custom properties, for example `--dc-bg`.

### 2a. Daylight (high contrast for bright sun)
Warm off-white background, near-black ink, dark pine for actions, blaze orange for attention. Contrast was checked against `bg` (WCAG ratio in brackets).

| Token | Hex | Use | Contrast |
|---|---|---|---|
| `bg` | `#F7F3EC` | App background ("canvas") | n/a |
| `surface` | `#FFFFFF` | Cards, sheets, inputs | n/a |
| `surface-sunken` | `#EDE6DA` | Pressed rows, wells, skeletons | n/a |
| `text` | `#1C1A17` | Body and headings | 15.7:1 |
| `text-muted` | `#4F4A42` | Secondary text, timestamps | 7.9:1 |
| `border` | `#7A7166` | Input and card outlines | 4.3:1 |
| `primary` | `#2F4A2C` | Primary buttons ("pine") | 8.9:1 |
| `on-primary` | `#FFFFFF` | Text on primary | 9.8:1 |
| `accent` | `#B23A0B` | Blaze orange: focus ring, badges, live items | 5.4:1 |
| `on-accent` | `#FFFFFF` | Text on accent | 6.0:1 |
| `danger` | `#A61B1B` | Errors, destructive actions | 6.8:1 |
| `success` | `#2F6B2F` | "Sent to camp" | 5.8:1 |
| `warning` | `#8A5A00` | "Saved on phone", expiring soon | 5.4:1 |
| `info` | `#1F4E79` | Neutral notices | 7.8:1 |

- All text meets WCAG AAA (7:1) or AA (4.5:1) at minimum. Borders meet the 3:1 rule for UI parts.
- Use no light-gray-on-white text. In direct sun, `text-muted` is the lightest text allowed.

### 2b. Pre-dawn (dark, protects night vision)
Rules: near-black warm background, **no white and no blue or green anywhere**, low-luminance amber text, red-orange for alerts. Large bright fills are not allowed, so primary buttons are dark "ember" fills with a bright outline.

| Token | Hex | Use | Contrast |
|---|---|---|---|
| `bg` | `#0B0806` | App background | n/a |
| `surface` | `#16100B` | Cards, sheets, inputs | n/a |
| `surface-sunken` | `#211810` | Pressed rows, skeletons | n/a |
| `text` | `#D69A62` | Body and headings (amber) | 8.2:1 |
| `text-muted` | `#A8774D` | Secondary text | 5.2:1 |
| `border` | `#8A5C37` | Outlines | 3.5:1 |
| `primary` | `#3A1F10` | Primary button fill ("ember") | n/a |
| `primary-border` | `#C2672E` | Primary button outline | 5.0:1 |
| `on-primary` | `#E8A46A` | Text on primary | 7.2:1 |
| `accent` | `#C2672E` | Focus ring, live items | 5.0:1 |
| `danger` | `#F0603F` | Errors (red keeps night vision) | 6.1:1 |
| `success` | `#D69A62` | Same as text; the icon carries the meaning | 8.2:1 |
| `warning` | `#E0A040` | "Saved on phone" | 8.8:1 |
| `info` | `#A8774D` | Neutral notices | 5.2:1 |

- Status in this theme is told apart by **icon and word**, not hue (section 7).
- Photos are dimmed to 70% brightness, with tap-to-view at full brightness. The map tile style for dark is set in M3.
- Set the manifest `theme_color` and the iOS status bar to `bg` for each theme so no bright browser chrome shows.
- Note for users (in Settings): "For the darkest screen, also turn your phone brightness down."

---

## 3. Type

- **Fonts:** Barlow Semi Condensed (headings, labels and buttons; a sturdy, outdoorsy face) and Barlow (body). Both use the free OFL license. **Self-host them through `next/font`** so they work offline. Fall back to `system-ui`.
- Sizes use `rem` so the phone's text-size setting scales them. Base `1rem` = 16px.

| Token | Size / line height | Weight | Font | Use |
|---|---|---|---|---|
| `display` | 36 / 40 | 700 | Semi Condensed | Year on the trip page |
| `h1` | 28 / 34 | 700 | Semi Condensed | Screen titles |
| `h2` | 22 / 28 | 600 | Semi Condensed | Section titles, card titles |
| `body-lg` | 18 / 26 | 400 | Barlow | **Default body**, inputs, list rows |
| `body` | 16 / 24 | 400 | Barlow | Secondary body, help text |
| `label` | 16 / 20 | 600 | Semi Condensed, uppercase, +0.04em tracking | Buttons, badges |
| `caption` | 14 / 18 | 500 | Barlow | Timestamps, sync badge text. **This is the floor: no text below 14px.** |
| `code` | 32 / 36 | 600 | Barlow, tabular figures, +0.3em tracking | 6-digit code entry |

---

## 4. Spacing, size and layout
- **Spacing scale (4px base):** `space-1` 4 · `space-2` 8 · `space-3` 12 · `space-4` 16 · `space-5` 24 · `space-6` 32 · `space-7` 48 · `space-8` 64.
- **Screen side padding:** 16px. Card padding: 16px. Gap between stacked controls: 12px.
- **Tap targets:** at least **48×48px** everywhere. Primary buttons are **56px** tall. Leave at least **8px** between neighboring targets (for gloves).
- **Radius:** 4px on badges, 8px on buttons and inputs, 12px on cards and sheets. Keep corners squared-off, not pill-round, to fit the rugged look.
- **Borders:** 1.5px (2px for focus and for the primary button in dark).
- **Elevation:** none in dark. In daylight, one soft shadow for sheets only. Cards use borders, not shadows.
- **Layout:** single column, max content width 560px, centered on large screens. The primary action is pinned to the bottom of the screen above the safe area (`env(safe-area-inset-bottom)`).
- **Motion:** 150ms ease-out, with no bounce. Respect `prefers-reduced-motion`.

---

## 5. Icons
- **Lucide** (`lucide-react`): ISC license, tree-shakeable, bundled so it works offline, with a consistent stroke.
- 24px default, 20px inside badges, **2px stroke**. Icons inherit `currentColor`.
- Every icon-only button has an `aria-label` and a 48px hit area.
- Core mapping: `lock` = private · `smartphone` = saved on phone · `circle-check` = sent to camp · `loader-circle` = sending · `triangle-alert` = needs attention · `cloud-off` = no signal · `tent` = camp · `map-pin` = pin · `users` = crew · `mail` = email · `share` = share · `sun` / `moon` = theme.
- **Proposal (scope):** a small custom set (tree stand, deer, antlers, drag) drawn to match Lucide's 2px grid, for M2–M3. Lucide has no hunting icons.

---

## 6. Core components

### Button
- **Variants:** `primary` (one per screen), `secondary` (outline), `ghost` (text only), `danger`.
- 56px tall (primary) or 48px (others). Full width on mobile. Label uses `label` type, max 3 words, verb first ("Send code", "Join camp").
- **States:** default · pressed (`surface-sunken` or 10% darker) · focus (2px `accent` ring, 2px offset) · **loading** (spinner replaces the leading icon, the label stays, the button keeps its size and is disabled) · **disabled** (50% opacity, plus a reason line under it, for example "Needs signal").
- **Needs-signal variant:** when an action needs the network and there is none, the button is disabled and shows `cloud-off` + "Needs signal". Never hide the button.

### Input
- 56px tall, `body-lg` text, label always visible above the field (never placeholder-only), and help text below.
- Use the right keyboard: `type=email` + `autocomplete=email`; the code field uses `inputmode=numeric` + `autocomplete=one-time-code`.
- **Error:** 2px `danger` border, `triangle-alert` icon, and a message below the field in plain words. The typed value is kept.
- Has a clear (x) button when it holds a value.

### List row
- At least 56px tall (72px with two lines). Layout: leading icon or avatar (40px) · title (`body-lg`) and subtitle (`caption`, muted) · trailing slot (sync badge, chevron or action).
- The whole row is the tap target. Secondary actions go in a trailing 48px button or a swipe, never swipe-only.

### Card
- `surface` background, 1.5px `border`, 12px radius, 16px padding.
- Optional header (h2 + trailing privacy or sync badge), body, and footer actions (48px).
- Cards that hold camp data show the privacy badge in the header (or once per screen in the header bar when every card is camp data).

### Toast and banner
- **Banner:** persistent and full width under the header. Used for states that last: no signal, items waiting to send, a session needing sign-in. Icon + one sentence + an optional action. It is dismissible only when the state is harmless.
- **Toast:** short confirmation ("Invite link copied"). Bottom of the screen, above the pinned action, 4 seconds, and announced with `aria-live="polite"`. **Never the only place important info appears.**
- **Tones:** info, success, warning, danger, each mapped to its token plus an icon.

### Sync-status badge
- Small chip: icon (20px) + `caption` text. Used per item (in rows and cards) and app-wide (in the header).
- Item states are defined in section 7.
- **App-wide header badge:** shows only when something isn't sent: `smartphone` + "3 waiting". Tapping it opens a sheet listing the waiting items and a "Try sending now" button.

### Privacy badge
- `lock` icon + text. **Never dismissible and never color-only.**
- **Short form** (in headers and cards): "[lock] Camp only". **Long form** (on first view, composers, the map and invites): "[lock] Only **{Camp name}** can see this."
- It appears in: the camp header bar (always), the pin map and pin details, every composer (post, harvest, message), the invite screens, and the join preview.
- In daylight it uses `text` on `surface-sunken`. In dark it uses `text` on `surface`.
- `[lock]` in these specs means the Lucide `lock` icon.

---

## 7. The standard "saved on phone" vs. "sent to camp" pattern

Anything a user creates (messages, stand claims, notes, posts, harvests) shows one of these states, the same way everywhere:

| State | Icon | Text (caption) | Daylight token | Dark token | When |
|---|---|---|---|---|---|
| Sending | `loader-circle` (spins) | "Sending…" | `text-muted` | `text-muted` | Has signal, upload in progress |
| **Saved on phone** | `smartphone` | "Saved on phone" | `warning` | `warning` | Stored locally and waiting for signal |
| **Sent to camp** | `circle-check` | "Sent to camp" | `success` | `success` | Server confirmed it |
| Didn't send | `triangle-alert` | "Didn't send. Tap to retry" | `danger` | `danger` | Rejected by the server (not just offline) |

- **The shape is what tells the states apart:** phone = only on this phone; check = everyone in camp has it. This works in the dark theme without hue.
- **Full text** shows on the item's detail view and on your own items in lists. In dense lists (chat), show the icon only, with the full text as its accessible label. This matches the familiar message-app pattern.
- "Sent to camp" fades to the icon only after 3 seconds in chat, so the stream stays calm. "Saved on phone" never fades.
- Photos have their own sub-state: "Photo waits for better signal" (`image` icon + `smartphone`). The text part of a post can be sent while the photo still waits.
- **Wording rule:** always "on phone" and "to camp". Never use "sync", "queue", "server" or "cloud" in the UI.

---

## 8. Voice and copy
- Short, plain and friendly, like a buddy talking. Use second person. Avoid tech words ("session", "token", "authenticate", "PWA").
- Errors say **what happened + what to do**: "That code doesn't match. Check the newest email and try again."
- Use the camp's name, not "group" or "workspace".
- Write times in local short form ("Fri 6:12 PM", "expires Oct 30").

---

## 9. Open items
- **Proposal:** switch automatically to the pre-dawn theme between sunset and sunrise at the camp location. This needs camp coordinates and an opt-in, so it expands scope. Route it to the PM.
- **Proposal:** custom hunting icon set (section 5).
- To check in R3: how far iOS standalone mode honors `theme_color` for each theme.
