# Deer Camp — Project Brief

## Concept
A private web app that works like a **yearbook for a hunting crew's trips**. Each crew gets its own private group. We start with our own crew and can offer it to other hunting groups later.

## Structure (D-13 to D-16)
- **Group:** the whole crew, any size (called a "camp" in the schema). Private from every other group.
- **Hunt:** one trip (dates, area). A group has many hunts, several per year if needed.
- **Party:** the members (plus name-only guests) who went on a hunt.
- **Visibility:** the party sees everything about its hunt. The rest of the group sees only a **summary** (proposed: name, dates, area, party names, harvest count; see ROADMAP Q1) until a party member **shares** the hunt to the group. Any party member can share it.
- **Pins belong to the group** and any hunt can use them.

## Core Features

### Group Home
- All the group's hunts, newest first, grouped by season
- Full hunts for the ones you were on or that were shared; summaries for the rest

### Hunt Page
- Name, dates, cover photo, and area
- Party: everyone who went
- Harvest board: who got what, with photos, date, and stand
- Stories wall: party members post notes, stories, and photos
- Camp awards (Biggest Buck, Worst Miss, Camp Cook of the Year, etc.)
- Share-to-group control

### Photo Import (D-17)
- Build past hunts from old photos: upload many at once, read date and location from the photo metadata, group them into trips, then confirm and create hunts
- Location is read on the device and **removed from every stored photo**; only a hunt-level location (or a group pin) is kept, private to the group
- Desktop upload is the main path (phones often remove location from photos picked in a browser)

### Hunting Spot Map
- Pins for each stand or hunting spot, shared across the group's hunts
- **Private to the group only**
- Harvests linked to the pin where they happened, to show which spots produce over the years

### Stand Assignments
- Assign hunters to pins
- See at a glance which spots are taken and which are open

### Live Hunt
- Group chat during the hunt ("deer headed your way," "need help dragging")
- Live updates to stand assignments

### Weather
- Forecast for the camp's specific location, or for each stand pin
- Wind direction, temperature, and moon phase
- Weather saved with each harvest record

## Constraints
- **Cell signal at camp is spotty.** The app has to be built to work offline first:
  - Installable phone app (PWA)
  - Messages, stand claims, and notes are stored on the phone and sent when signal returns
  - Messages stay short and text-only; photos upload later on better signal
- **Free tiers:** photos are resized on the device before upload; originals are never stored (Supabase free storage is 1 GB)

## Proposed Tech Stack
- **Frontend:** Next.js (React), built as a PWA
- **Hosting:** Vercel
- **Backend:** Supabase (sign-in, Postgres database, photo storage, live updates)
- **Data privacy:** Supabase row-level security so each group sees only its own data, and only the party sees an unshared hunt's details
- **Maps:** Mapbox or Leaflet
- **Weather:** a weather API such as Open-Meteo or OpenWeather
- **Cost:** free tiers should cover one crew

## Multi-Group Design
Each group is its own private space from day one, which makes it easy to add other crews later. Hunts and parties live inside one group and never cross groups.

## The Team (agents)
- **project-manager:** plans, routes work, guards scope
- **builder:** writes code, commits, pushes
- **researcher:** studies competing apps and recommends improvements
- **designer:** designs flows and reviews UI against scope

## Next Steps
1. Design how people sign in and join a group (done)
2. Lay out the database (groups, members, hunts, parties, harvests, pins, posts, messages)
3. Build the group home and hunt page first, then the map, stand assignments, and live hunt
4. Photo import for past hunts
