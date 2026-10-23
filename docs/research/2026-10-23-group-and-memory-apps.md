# R2: Group-Trip, Club and Shared-Memory Apps

_Researcher, 2026-10-23. Milestone 1, task R2. Sources were checked on this date._

**Legend:** `[unverified]` = no source confirmed it. `[vendor]` = the source is the company itself, a competitor or a sponsored piece. `[old]` = the source is more than two years old.

## Summary
- **Hunting-club tools** (CampKeeper, BlindBook, VSIB) cover stand sign-in, harvest logs and dues. **CampKeeper is the closest direct competitor** to Deer Camp's stand board and harvest board. It is new and has no reviews yet. None of these tools does the **yearbook** side: a trip-year page, stories and awards.
- **Memory apps** (FamilyAlbum, Day One, Google Photos, iCloud) do the yearbook side well. Their strengths are invite-only privacy, comments, recap videos and printed books. None of them knows about hunts, stands or harvests.
- **Invites:** The easiest flows use a **QR code or link with no account needed** (CampKeeper guests, Google Photos link, Kittysplit). The safest flows tie an invite to a person or role (FamilyAlbum roles, BlindBook renewable invites, Day One's 30-member cap). GroupMe's "anyone with the link joins" is the cautionary example.

---

## A. Group-trip and hunting-club tools

### CampKeeper: Deer Club Manager (iOS)
- **What and who:** Manages deer leases and hunting clubs, and also works for solo hunters. Version 1.0 shipped Aug 6. The year is not shown and is likely 2026 `[unverified]` ([App Store](https://apps.apple.com/app/id6792546790)).
- **Features:**
  - A weekly stand reservation grid with morning and evening slots, club booking rules, and one-tap check-in and check-out. **Forgotten check-ins close automatically overnight.**
  - A harvest log (species, points, weight, age, weapon, photos). **The stand fills in automatically from the check-in.** Harvests post to a club feed with likes and comments, and season stats are tracked.
  - A property map with pins for stands, feeders and cameras. Dues tracking. A 15-second "hunt reel" video maker. A PDF report for landowners ([App Store](https://apps.apple.com/app/id6792546790)).
- **Pricing:** Free for clubs of up to 3 members. The club plan is $119.99/yr, paid by the admin. A solo lifetime plan is $29.99 ([App Store](https://apps.apple.com/app/id6792546790)).
- **Offline:** Maps can be downloaded ahead. **Check-ins and harvests save on the phone and sync when signal returns** ([App Store](https://apps.apple.com/app/id6792546790)).
- **Privacy:** The App Privacy label says "Data Not Collected." Check-ins are location points only, with no live tracking ([App Store](https://apps.apple.com/app/id6792546790)).
- **Praise and complaints:** No ratings yet ([App Store](https://apps.apple.com/app/id6792546790)).

### BlindBook: Hunt Log Platform (iOS)
- **What and who:** A waterfowl-first hunt log that also covers deer and turkey. It serves "Hunt Crews," clubs and outfitters ([App Store](https://apps.apple.com/us/app/blindbook/id6751916153)).
- **Features:**
  - A hunt log with photos, harvests and **hunt-day weather** (wind relative to the blind, temperature, pressure).
  - Booking for blinds and stands, with double-booking warnings. Crews share a log, photos and a schedule.
  - In-app messaging (BlindBook Connect). Offline maps ([App Store](https://apps.apple.com/us/app/blindbook/id6751916153)).
- **Pricing:** Free to log. Pro in-app purchases run $9.99–$149.99, with billing period unclear. Crew and club pricing is not listed ([App Store](https://apps.apple.com/us/app/blindbook/id6751916153)).
- **Praise and complaints:** Rated 5.0★ from only 18 ratings. Reviewers praise it for replacing several apps ([App Store](https://apps.apple.com/us/app/blindbook/id6751916153)).

### VSIB: Virtual Sign-In Board (web) and Hunters Sign In (iOS)
- **What and who:** A web app that lets club members sign in to stands or zones. A free iOS companion app handles only sign-in and sign-out, and shows wind and temperature ([App listing](https://iphoneaddict.fr/apps/sports/hunters-sign-in-263.html)).
- **Features:** Reserve a spot, see who is in the field, handle guests, log stand use and sightings for year-over-year trends, and send the president a 9 PM alert about hunters still signed in ([GON forum, 2018–2025](https://forum.gon.com/threads/vsib-anyone-else-using-it.921401/page-2)).
- **Pricing:** About $5 per person in 2018. Current pricing is `[unverified]` ([GON](https://forum.gon.com/threads/vsib-anyone-else-using-it.921401/page-2)).
- **Praise:** "Works great" (2019) and liked overall (2025) ([GON](https://forum.gon.com/threads/vsib-anyone-else-using-it.921401/page-2)).
- **Complaints:**
  - Members forget to sign out, and all-day reservations block others.
  - Members without smartphones are left out.
  - One club moved from VSIB to HuntStand and then **back to paper** ([GON](https://forum.gon.com/threads/vsib-anyone-else-using-it.921401/page-2)).
- The iOS companion app was last updated in 2019 `[old]`.

### GroupMe (group chat used by clubs and crews)
- **What and who:** Free group messaging from Microsoft.
- **Invite:** The owner turns on a share link and sends it by text or email, or shows a QR code. **"Anyone who receives it can join."** Groups hold up to 5,000 members. Microsoft's help page describes no approval step ([Microsoft support](https://support.microsoft.com/en-us/GroupMe/how-do-i-create-a-share-link-in-groupme)).
- **Relevance:** This is what crews use today for "deer headed your way." It needs signal and has no tie to stands or harvests `[unverified for offline behavior]`.

### Wanderlog (group trip planner)
- **What and who:** A collaborative trip itinerary app.
- **Features:** Invite friends, edit together in real time, edit or view-only permissions, shared notes and files, and group expense splitting ([Pilot comparison, Sep 2026](https://www.pilotplans.com/compare/wanderlog-alternative-feature-comparison) `[vendor: competitor]`).
- **Pricing and offline:** **Offline access is Pro-only**, at about $39.99/yr ([Pilot](https://www.pilotplans.com/compare/wanderlog-alternative-feature-comparison)).
- **Praise and complaints:** Rated 4.9 on the App Store but 1.8 on Trustpilot. Specific complaints were not summarized ([Pilot](https://www.pilotplans.com/compare/wanderlog-alternative-feature-comparison)).

_Side note (cost splitting, outside the brief):_ Splitwise requires everyone to have an account ([Kittysplit](https://www.kittysplit.com/en/splitwise-alternative) `[vendor: competitor]`). It has capped free expenses at 3–4 per day since late 2023, with Pro at $5/mo or $40/yr in the US ([IT Voice, Dec 2023](https://www.itvoice.in/splitwise-has-introduced-restrictions-on-the-number-of-free-expenses-users-can-add)). Kittysplit works from a link with no account. Splitwise and Kittysplit are not counted as R2 products.

---

## B. Shared-album, journal and yearbook apps

### FamilyAlbum
- **What and who:** A private family photo and video album, best known for baby photos.
- **Features:**
  - Unlimited free storage, invite-only viewing, and comments and reactions.
  - Recap videos (every 3 months on the free tier, plus monthly and yearly on paid tiers).
  - Printed photobooks ([FamilyAlbum blog, Jul 2026](https://blog.family-album.com/blog/is-familyalbum-really-free-what-you-get-without-paying/) `[vendor]`).
- **Pricing:** Free. Premium Family is $5.99/mo or $59/yr. Pro is $10.99/mo or $109/yr. **One subscription covers the whole album** ([FamilyAlbum blog](https://blog.family-album.com/blog/is-familyalbum-really-free-what-you-get-without-paying/)).
- **Invite:** Only Album Admins can invite. The inviter picks a role, and the invitee scans a **QR code on the inviter's phone** ([FamilyAlbum help](https://help.family-album.com/hc/en-us/articles/360052306734-How-do-I-use-a-QR-code-to-invite-someone-to-my-album); [Admins only](https://help.family-album.com/hc/en-us/articles/360038640513-How-can-I-use-this-app-with-my-partner)).

### Day One Shared Journals
- **What and who:** A journaling app with shared journals for families and small groups.
- **Features:**
  - Up to **30 members**. Members add entries and comment.
  - The owner sets whether entries can be edited only by the author or by anyone.
  - **End-to-end encrypted**, and entries can't be published to the web ([Day One](https://dayoneapp.com/shared-journals/)).
- **Pricing:** Invitees need a free account. Creating a shared journal appears to need Premium, though Day One's FAQ contradicts itself `[unverified]`. Free members can add one photo per entry ([Day One](https://dayoneapp.com/shared-journals/)).
- **Invite:** The method is not documented on the page `[unverified]`.

### Google Photos shared albums
- **Invite:**
  - **Direct share** to Google accounts sends an email and an in-app notice.
  - **Link share** works for people without a Google account. The owner can turn the link off and choose whether others can add photos.
  - **Removing a person also removes the photos they added** ([Engadget, 2020](https://www.engadget.com/google-photos-direct-album-sharing-172923466.html) `[old]`).
- **Relevance:** This is a low-friction way crews already collect trip photos.

### iCloud Shared Albums (Apple Photos)
- **Limits:** Up to 100 participants. Albums made on version 26 or earlier hold 5,000 items, with photos reduced to 2,048 px and video at 720p. Albums made on version 27+ are limited only by the owner's iCloud storage ([Apple support](https://support.apple.com/en-au/148868)).
- **Invite:** Through an Apple ID. Whether non-Apple users can view through a public website is not confirmed by this source `[unverified]`.

---

## C. How they invite people in (and how easy it is for non-tech users)

| Product | Method | Account needed? | Ease for a low-tech hunter | Risk |
|---|---|---|---|---|
| CampKeeper | Start or join a club. **Guests scan a QR code at the gate with no app** | Members yes. Guests no | High for guests | Member invite flow not documented `[unverified]` |
| BlindBook | Admin sends an invite. **Renew expired invites. Printable QR code** for self-onboarding | Yes | Medium | Low (admin-controlled) |
| VSIB | Not documented. A member without a smartphone can sign in on a buddy's phone | Yes | Low-Medium | — |
| GroupMe | Share link or QR code. **Anyone with the link joins** | Yes (assumed) `[unverified]` | High | **High: forwarded links let strangers in** |
| Wanderlog | "Invite friends." Edit or view-only roles | Not documented | Medium | Low |
| FamilyAlbum | Admin picks a role, then the invitee scans a QR code on the admin's phone | Not stated | High in person | Low |
| Day One | Owner invites. 30-member cap | Yes (free account) | Medium | Low (E2E encrypted) |
| Google Photos | Direct to an account, or a link that can be turned off | Link: no | High | Medium (link can be forwarded) |
| iCloud Shared Albums | Apple ID invite | Yes (Apple ID) | High for iPhone users, poor for Android | Low |

**Patterns worth noting**
- **In-person QR codes** (FamilyAlbum, BlindBook, CampKeeper) fit deer camp well. The crew is physically together at least once a year.
- **Roles chosen when the invite is made** (FamilyAlbum, BlindBook) match the D-4 default: per-person invites, an expiry date, and the owner can revoke them.
- **"Renew expired invite"** (BlindBook) heads off the most common support problem.
- **Guest without an app** (CampKeeper) supports D-7, name-only roster guests.

---

## Recommendations
_Ranked by value to the crew relative to effort to build. These are proposals for the PM and Jason, not decisions._

| # | Type | Recommendation | Value | Effort |
|---|---|---|---|---|
| 1 | Copy | **Per-person invite link plus an in-person QR code of the same invite,** with a role, an expiry and revoke. Add a one-tap "resend or renew" for expired invites. This matches the D-4 default and covers buddies who are together at camp. | High | Low-Medium |
| 2 | Copy | **CampKeeper's harvest-from-check-in:** when a hunter logs a harvest while claiming a stand, pre-fill the stand pin, then attach the weather snapshot (R1 #2). | High | Medium |
| 3 | Copy | **Auto-release stand claims at dark** (CampKeeper closes forgotten check-ins overnight, and VSIB sends a 9 PM alert). This addresses the main reason clubs go back to paper. | High | Low |
| 4 | Gap | **The yearbook itself:** a trip-year page, stories, awards and a year-over-year view. No club tool has it, and no memory app knows about stands or harvests. This is Deer Camp's reason to exist. | High | Medium |
| 5 | Copy | **Name-only guests** (CampKeeper guests, VSIB's "sign in on a buddy's phone"). Supports D-7, so the roster and awards stay complete for the hunter who won't install anything. | High | Low |
| 6 | Copy | **Owner-paid, crew-wide plan** (FamilyAlbum, CampKeeper) if Deer Camp is ever offered to other crews. One person pays and the crew joins free `[proposal for Jason, future]`. | Medium (future) | — |
| 7 | Do better | **Comments and reactions on harvests and stories** (FamilyAlbum, CampKeeper feed, Day One). Keep them text-only offline. | Medium | Low-Medium |
| 8 | Proposal for Jason | **End-of-season recap**, such as a printable PDF yearbook or a photo recap (FamilyAlbum recaps and photobooks, CampKeeper PDF and reels). This is outside the brief but a natural "yearbook" export. | Medium | Medium |
| 9 | Avoid | **Open "anyone with the link joins" invites** (GroupMe). A forwarded text would let strangers see private pins. | High (privacy) | — |
| 10 | Avoid | **Removing a member deletes their content** (Google Photos). The yearbook should keep a departed hunter's harvests and stories. | High | Low |
| 11 | Proposal for Jason | **Cost splitting and dues** (CampKeeper dues, Splitwise, Kittysplit) are outside the brief. If wanted, a link-based "who owes what" with no extra accounts is the low-friction pattern. | Low-Medium | Medium |
