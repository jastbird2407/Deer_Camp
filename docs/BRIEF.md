# Deer Camp — Project Brief

## Concept
A private web app that works like a yearly **yearbook for a hunting crew's trip**. Each group gets its own camp space. We start with our own crew and can offer it to other hunting groups later.

## Core Features

### Yearly Trip Page
- Year, cover photo, and camp location
- Roster of everyone who made the trip
- Harvest board: who got what, with photos, date, and stand
- Stories wall: anyone in the group can post notes, stories, and photos
- Camp awards (Biggest Buck, Worst Miss, Camp Cook of the Year, etc.)

### Hunting Spot Map
- Pins for each stand or hunting spot
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

## Proposed Tech Stack
- **Frontend:** Next.js (React), built as a PWA
- **Hosting:** Vercel
- **Backend:** Supabase (sign-in, Postgres database, photo storage, live updates)
- **Data privacy:** Supabase row-level security so each camp sees only its own data
- **Maps:** Mapbox or Leaflet
- **Weather:** a weather API such as Open-Meteo or OpenWeather
- **Cost:** free tiers should cover one crew

## Multi-Group Design
Each camp is its own private group from day one, which makes it easy to add other crews later.

## The Team (agents)
- **project-manager:** plans, routes work, guards scope
- **builder:** writes code, commits, pushes
- **researcher:** studies competing apps and recommends improvements
- **designer:** designs flows and reviews UI against scope

## Next Steps
1. Design how people sign in and join a camp
2. Lay out the database (camps, members, years, harvests, pins, posts, messages)
3. Build the yearly trip page first, then the map, stand assignments, and live hunt
