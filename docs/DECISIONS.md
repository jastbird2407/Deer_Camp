# Decision Log

_Maintained by the project-manager agent. Newest first._

| Date | Decision | Reason |
|---|---|---|
| 2026-10-23 | D-1: Repo will be private (Jason to switch it in GitHub settings) | Keep the brief, schema, and camp details off the public web |
| 2026-10-23 | D-2: Sign-in is email plus a 6-digit code | Works inside the installed iPhone app, free, no Google account needed |
| 2026-10-23 | D-3: Jason is camp owner, with one backup admin | One clear owner; a backup if Jason is unavailable |
| 2026-10-23 | D-4: Invites are one link per person, expire in 7 days, revocable | Safest option for private pins |
| 2026-10-23 | Next.js uses TypeScript and the App Router. Camp roles are owner, admin, and member. M1 schema is written as local migrations, with no Supabase project yet | Set in Jason's M1 direction and the builder's stack. No credentials exist yet |
| 2026-10-23 | Four-agent team: project-manager, builder, researcher, designer | Jason's setup for running the project |
| 2026-10-22 | Stack: Next.js PWA on Vercel, Supabase backend | Low cost, built-in auth, storage, realtime, and row-level security |
| 2026-10-22 | Offline-first design | Cell signal at camp is spotty |
| 2026-10-22 | Each camp is a private group from day one | Lets other crews join later |
