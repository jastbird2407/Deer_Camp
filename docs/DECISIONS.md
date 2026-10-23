# Decision Log

_Maintained by the project-manager agent. Newest first._

| Date | Decision | Reason |
|---|---|---|
| 2026-10-23 | D-13: Two levels. A **group** (the big camp, any size) holds many **hunts** (individual trips), each with its own **party** members | Jason's direction: the big group sees all hunts; each trip has its own party |
| 2026-10-23 | D-14: A hunt not shared to the group shows the whole group a **summary** only; full details stay with the party | Jason's choice |
| 2026-10-23 | D-15: **Any party member** can share a hunt to the big group | Jason's choice |
| 2026-10-23 | D-16: Hunting spot **pins belong to the group** and any hunt can use them | Jason's choice: one shared map |
| 2026-10-23 | D-17: New feature: **photo import** to build past hunts. Upload past photos; read date and location from photo metadata; group them into prior trips | Jason's request. Detail and timing to be planned by PM |
| 2026-10-23 | D-5: Maps use MapLibre GL JS with USGS satellite and topo tiles, saved offline for the camp area only | Free, public domain, allowed to cache; Mapbox offline terms unclear for web |
| 2026-10-23 | D-6: Weather uses Open-Meteo | Free, no key, wind and moon data; switch if Deer Camp is ever sold |
| 2026-10-23 | D-7: Roster allows name-only guests without an app account | Keeps the yearbook complete; already in the schema |
| 2026-10-23 | D-8: Any signed-in user can create a camp | Supports other crews later |
| 2026-10-23 | D-9: Signed-out invite screen shows camp name, inviter, role, expiry only | Helps a buddy trust the link; reveals nothing else |
| 2026-10-23 | D-10: Resend is on hold. Use Supabase's built-in email for testing only; Resend moves to the ideas list | Jason's call; revisit before inviting the full crew |
| 2026-10-23 | D-11: Owner and admins add pins and trip years; members claim stands, log harvests, post | Keeps the private map tidy |
| 2026-10-23 | D-12: Approved now: QR code for invites, "Ready for camp" checklist, basic member management, auto-release stand claims at dark (M3), "good winds" tag per stand (M3). Later: printable yearbook, auto dark theme at sunset, custom hunting icons, GPX import. Ideas list: Resend email sender, trail-cam import. No: scouting AI, parcel lookup, cost splitting, email-bound invites, satellite messaging | Jason accepted all PM recommendations, with Resend and trail-cam import moved to the ideas list |
| 2026-10-23 | Vercel project `deer_camp` is connected to the repo; Supabase project created by Jason | Jason action J1 |
| 2026-10-23 | D-1: Repo will be private (Jason to switch it in GitHub settings) | Keep the brief, schema, and camp details off the public web |
| 2026-10-23 | D-2: Sign-in is email plus a 6-digit code | Works inside the installed iPhone app, free, no Google account needed |
| 2026-10-23 | D-3: Jason is camp owner, with one backup admin | One clear owner; a backup if Jason is unavailable |
| 2026-10-23 | D-4: Invites are one link per person, expire in 7 days, revocable | Safest option for private pins |
| 2026-10-23 | Next.js uses TypeScript and the App Router. Camp roles are owner, admin, and member. M1 schema is written as local migrations, with no Supabase project yet | Set in Jason's M1 direction and the builder's stack. No credentials exist yet |
| 2026-10-23 | Four-agent team: project-manager, builder, researcher, designer | Jason's setup for running the project |
| 2026-10-22 | Stack: Next.js PWA on Vercel, Supabase backend | Low cost, built-in auth, storage, realtime, and row-level security |
| 2026-10-22 | Offline-first design | Cell signal at camp is spotty |
| 2026-10-22 | Each camp is a private group from day one | Lets other crews join later |
