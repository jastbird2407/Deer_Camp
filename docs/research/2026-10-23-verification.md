# R5 Verification Scan (2026-10-23)

_Researcher. Short scan. Decisions D-10 (Resend on hold) and D-12 (QR invites approved) apply._

## (a) Supabase built-in auth email
Source: [Supabase docs, Auth SMTP](https://supabase.com/docs/guides/auth/auth-smtp)
- **Rate limit:** the default service sends about **2 messages per hour**, and the cap can change without notice.
- **Recipient restriction:** without custom SMTP, mail goes **only to addresses on the project's team** (org Team tab). Others fail with "Email address not authorized."
- **No SLA:** best-effort only. The docs say it is for exploration and testing with team members.
- **Custom SMTP:** removes the team-only restriction. The default limit then becomes 30 per hour, adjustable on the Rate Limits page. Listed providers: Resend, AWS SES, Postmark, Twilio SendGrid, ZeptoMail, Brevo. (Same source.) Free-tier terms for each were not checked here (unverified).

**Is it enough to test sign-in with 2-3 people?**
- Only if all 2-3 testers are added as team members on the Supabase org first. That means they need Supabase accounts and invites. Awkward for hunting buddies. Verdict: fine for Jason plus maybe one other team member, **not** for testing with buddies.
- 2 emails per hour is tight. Each code request uses one, so 3 testers with a retry will hit the cap and wait.
- Workarounds without SMTP (from general Supabase practice, unverified here): create test users in the dashboard, or use test phone/OTP-free flows. Builder should confirm.
- Before inviting the real crew, custom SMTP is required (matches the D-10 revisit note).

## (b) iOS Home Screen web app vs. Safari
- Apple (WWDC23 session 10120, ["What's new in WebKit"](https://developer.apple.com/wwdc23/10120)) says Home Screen web apps have **separate cookies and storage from the browser**. Signing in in Safari does **not** sign in the web app.
- The same session says local storage is **not copied** when the app is created. It suggests keeping auth in cookies. It says Safari copies cookies on Mac Dock adds, but it does **not say** whether iOS copies them (unverified for iOS).
- Email links open in the default browser, so they will not sign in an installed app. Apple suggests a **one-time code** entered in the app, or passkeys.
- WebKit bug [181849](https://bugs.webkit.org/show_bug.cgi?id=181849): Apple's Brent Fulgham called the isolation "by design" in 2022 (isolated entities, no shared state). Bug still open.
- **Implication:** the plan "join in Safari, install, sign in again in the app" is correct. A 6-digit code typed into the installed app fits Apple's advice. Magic links would not work for the installed app.

## (c) Open-Meteo moon fields
- The [forecast docs](https://open-meteo.com/en/docs) list **"Moonrise", "Moonset" and "Moon Phase"** under Daily Weather Variables (display labels).
- **Not confirmed:** the exact API parameter names. The docs page fetch showed no identifiers, and the Daily Parameter Definition table showed no moon entries. `moon_phase`, `moonrise`, `moonset` are the likely names (unverified guess).
- **No live call possible:** WebFetch of the API URL was refused (robots_disallowed), and the GitHub source fetch failed. Builder should test once in a browser or terminal: `https://api.open-meteo.com/v1/forecast?latitude=39.12&longitude=-97.70&daily=moon_phase,moonrise,moonset&timezone=America%2FChicago` and check for an error.
- Fallback if absent: compute moon phase locally with a small formula (no network, works offline). Worth doing anyway since saved harvest weather must work offline (unverified suggestion).

## (d) Feature matrix
- Updated the Deer Camp invite wording in `/home/claude/deer_camp/docs/research/feature-matrix.md` (both tables): per-person link plus QR of the same link (D-12). Takeaway 4 reworded to match.

## Recommendations
1. **High value, low effort:** For R5 testing, add testers to the Supabase team only if they are 1-2 people. Otherwise set up custom SMTP early; do not rely on built-in email with buddies.
2. **High value, low effort:** Keep email codes (not magic links) for the installed iOS app.
3. **Medium value, low effort:** Verify the Open-Meteo moon parameter names with one real call; add a local moon-phase calculation as a fallback.
4. **Avoid:** storing auth in local storage only, since it is not carried into the installed app.
5. For the PM: D-10 revisit is required before the full crew is invited. Cheapest custom SMTP choice is unverified; a follow-up scan could compare free tiers.
