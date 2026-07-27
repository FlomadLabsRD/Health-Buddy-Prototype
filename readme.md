# Coach Dashboard MVP — FloHealth Buddy

> **Repository status:** This repo currently contains the **front-end only** for the Coach Dashboard MVP. Everything here is client-side: UI, layout, interaction behavior, and browser-local data. **The back end will be integrated soon** — API, database, authentication, and real AI services are not part of this commit. See **Back end — not yet integrated** below for exactly what is stubbed and what the server will need to provide.

## Overview
FloHealth Buddy is a mobile health application with **two distinct user modes** sharing one shell:

1. **Personal Health** — medications, daily check-in, wellness goals, nutrition, appointments, records, community events.
2. **Coach Dashboard (FloHealth Athletics)** — team readiness, attendance, athlete profiles, calendar, sport-specific score sheets, an interactive **field/court positioning board**, and positional analytics.

On first launch a splash screen asks the user to choose a **primary role**; the app then opens to that dashboard. The choice persists and the user can switch modes anytime from the header's 3-dot menu.

The signature feature of this build is the **field view + play recording + positional insights** loop: a coach arranges players on a field diagram, records that arrangement at a game timestamp, and the app derives pattern insights from the accumulated position data (deliberately distinct from box-score statistics, which live on the score sheets).

## Back end — not yet integrated
The front end is complete and interactive, but every data operation is currently local to the device. When the back end lands it will need to cover:

| Area | Today (front-end only) | Needed from the back end |
| --- | --- | --- |
| Auth &amp; accounts | None — no sign-in | User accounts, sessions, role (personal / coach) |
| Persistence | `localStorage` only, wiped by clearing the browser | Server-side storage for all records below |
| Roster &amp; teams | Sample athletes hardcoded | Team + athlete CRUD, multi-team per coach |
| Plays &amp; positions | `flo_plays_&lt;sport&gt;`, capped at 30 per sport, device-local | Durable play history, season-long, multi-device |
| Attendance &amp; check-ins | Component state, lost on reload | Persisted per athlete per date |
| Score sheets | Editable in-session only | Saved game records |
| Analytics | Computed in the browser from local plays | Server-side aggregation across full history |
| AI (Coach AI, drills, insights) | Static fixtures and keyword matching | Real model integration |
| Media &amp; records | Upload UI only, nothing stored | File storage |
| Devices &amp; sync | “Coming soon” placeholder | Wearable integrations |

The play record shape documented under **State Management** is the intended contract — it is the schema the API should mirror.

## About the Design Files
The file in this bundle (`FloHealth Mobile MVP.html`) is a **design reference created in HTML** — a working prototype demonstrating the intended look, layout, and behavior. **It is not production code to copy directly.**

It is a single-file React prototype: React 18 + ReactDOM via UMD CDN scripts, JSX transpiled in-browser by Babel Standalone, and all styling in one `<style>` block plus inline style objects. That structure exists purely so the prototype runs by opening a file in a browser.

The task is to **recreate these designs in the target codebase's existing environment** — React Native, Flutter, Swift/SwiftUI, Kotlin/Compose, or a React web app — using its established component library, navigation, styling approach, and state management. If no environment exists yet, choose the framework most appropriate for the product (this is a phone-first app, so React Native or a native stack is the natural fit) and implement there.

Do **not** ship in-browser Babel, and do not treat the inline style objects as a styling convention — translate them into the target platform's idiom (StyleSheet, Tailwind, styled-components, SwiftUI modifiers, etc.).

## Fidelity
**High-fidelity (hifi).** Colors, typography, spacing, border radii, copy, and interaction behavior are all final and intentional. Recreate the UI faithfully using the codebase's existing libraries and patterns. Exact values are documented under **Design Tokens** below.

The prototype is designed against a **390 × 844 pt** viewport (iPhone 14/15 class). The phone bezel shell (`.phone`) is prototype-only scaffolding to preview the app in a desktop browser — **discard it**; the real app fills the device screen.

---

## Screens / Views

### 0. Role Splash (`RoleSplash`)
- **Purpose:** First-run role selection; sets which dashboard opens by default.
- **Layout:** Full-screen, vertically scrolling column on `--bg`. Centered header block with 52px top padding; two selectable cards; a full-width CTA.
- **Components:**
  - **Logo tile** — 56 × 56, radius 16, background `--t-500`, shadow `0 6px 20px rgba(21,147,133,.3)`. Contains `assets/flobrain-hand.png` at 32 × 36, `object-fit: contain`. *This asset is white artwork — it only reads against the teal tile.*
  - **Title** — "FloHealth Buddy", 800 / 23px / 1.1, `--ink`, letter-spacing −.02em.
  - **Subhead** — "Welcome!", 700 / 15px, `--t-600`.
  - **Body** — "Choose the dashboard you'd like to open by default. You can switch anytime in Settings.", 400 / 13px / 1.5, `--faint`, max-width 280, centered.
  - **Two option cards** (`.card` base, padding 16px 18px, gap 12): radio dot 20 × 20 circle — unselected `2px solid var(--line)`, selected `6px solid var(--t-500)`. Selected card: background `--t-50`, border `2px solid var(--t-400)`. Titles 700 / 15px `--ink`; descriptions 400 / 12px / 1.5 `--faint` indented 30px.
    - "Personal Health" — "Manage medications, wellness, appointments, and daily health."
    - "Coach Dashboard" — "Manage athletes, attendance, training sessions, and team insights."
  - **Continue button** — full width, radius 12, padding 14. Enabled: `--t-500` bg, white text, shadow `0 4px 14px rgba(21,147,133,.3)`. Disabled (nothing selected): `--line` bg, `--faint` text, no shadow, not clickable.
- **Behavior:** Selecting a card highlights it and enables Continue. Continue persists the role and mounts the matching dashboard. Transitions 150ms.

### 1. Personal Health — Home (`HomeTab`)
- **Purpose:** Daily hub — medications, check-in, goals, nutrition.
- **Layout:** Sticky `.app-header`, scrolling card column (cards are `margin: 0 12px 12px`), fixed bottom nav.
- **Components:** Today's Medications (checkbox rows, "View all" → Scheduler); Daily Check-In (`CheckInCard` — mood emoji row 😊/😐/😫/🤪, energy, intention; collapsible); Daily Motivation (`MotivationCard`, collapsible, cycles tips); Weekly Wellness Goals (`WellnessGoalsCard` → `GoalItem` with 7-day checkboxes, manual add, and AI-suggested goals); Health & Nutrition (`HealthNutritionCard` — Healthy Recipes, Nutrition Tracker, Healthy Foods Library with calorie search via `LibrarySearch`).
- **Collapsible pattern:** header row tappable, chevron `›` rotates 90° on open, 200ms.

### 2. Personal Health — Scheduler (`SchedulerTab`)
Mini month calendar grid (selected day filled `--t-500`, event dots beneath), day event list, and Medication Schedule cards with **+ Add** and reminder toggles. Event type colors: Practice `#1e40af`, Game `#9d174d`, Training `#5b21b6`, Recovery `#065f46`, Tournament `#92400e`.

### 3. Personal Health — Community (`CommunityTab`)
Two tabs: **Events** and **Trials & Research**. Category filter chips (equal-width, wrapping). Event cards → detail popover (Join Event / Share Event). **+ Organize** button aligned with the "Community" heading.

### 4. Personal Health — Records (`RecordsTab`)
Expandable record categories with an **Upload** button in the header.

### 5. Coach — Dashboard (`CDash`)
- **Purpose:** At-a-glance team status.
- **Sections, in order:** Team Overview (Attendance / Active / Recovery / Injured counts, driven by attendance entry); **Today's Schedule** (`TodaySchedule` — e.g. "Practice at 4", expands to a drill checklist with completion checkboxes); Team Readiness with three aligned controls — **Team** select, **Sheet** toggle, **Field** toggle; Recent Notes with **+ Note**; Weekly Workload; Practice Completion.
- **Attendance** (`TodayAttendance`) — floating popover anchored to the Attendance button, roster rows with status per athlete. Statuses/colors: Active `--t-500`, Absent `#888`, Recovery `#e09b2d`, Injured `#d94f4f`. Entries feed the Team Overview counts.
- **Team select** options: 🏈 Football, ⚾ Baseball, 🏀 Basketball, ⚽ Soccer, ➕ Create Team. Selecting a sport switches **both** the score sheet and the field view to that sport.

### 6. Coach — Field View (`FieldDiagram`) ⭐ core feature
- **Purpose:** Position players, then record who was where at a moment in the game.
- **Canvas:** inline SVG, `viewBox="0 0 330 180"`, width 100%, radius 8, `touch-action: none`. Sport-specific field art: soccer pitch, basketball court (`#c17f3b`), football field (`#2d5a27` with yard lines + end zones), baseball diamond (`#4a7c3f` outfield, `#b5824a` infield), plus rink/track/pool variants.
- **Player dots:** `<circle r={10}`, fill = team color, `stroke #fff` 1.5 (2.5 while dragging, opacity .85). Initials centered, 7px, weight 700, white. Position label below at `cy+18`, 5.5px, white with `rgba(0,0,0,.55)` 1.6px halo (`paint-order: stroke`); hidden when it would duplicate the initials.
- **Both teams render.** Home = `#159385` default, Away = `#d1495b` default, each changeable via a 6-swatch color row (`#159385`, `#2563eb`, `#d1495b`, `#e08a1e`, `#7c3aed`, `#475569`). Rosters by sport: football 11 v 11 (offense vs defense across the LOS), baseball 9 fielders v batter + 3 base runners, basketball 5 v 5 (mirrored halves), soccer 11 v 11 (mirrored).
- **Formation dropdowns** — Offense and Defense (Formation only, for soccer/baseball). Selecting one **instantly repositions that unit**, preserving any athlete initials already assigned and relabeling positions to the new scheme. Every list ends with **Custom**, which leaves dots untouched.
  - Football offense: I Formation, Shotgun, Singleback, Trips Right, Trips Left, Goal Line
  - Football defense: 4-3, 3-4, Nickel, Dime, Goal Line Defense
  - Basketball offense: 5-Out, 4-Out 1-In, 3-Out 2-In, Horns, Box, High Post, Low Post, Motion Offense
  - Basketball defense: Man-to-Man, 2-3 Zone, 3-2 Zone, 1-3-1 Zone, 2-1-2 Zone, Full Court Press, Half Court Press, Matchup Zone
  - Soccer: 4-4-2, 4-3-3, 3-5-2, 4-2-3-1
  - Baseball: Standard Defensive Alignment, Infield In, Double Play Depth, Shift Left, Shift Right
- **Gestures — important:**
  - **Drag** a dot to reposition. Movement > 3px in either axis marks the gesture as a drag. Coordinates clamp to 8 … (dimension − 8).
  - **Tap** (pointerdown → pointerup with no movement past that threshold) opens the **player editor** popover: team badge ("My Team" / "Opponent" with color dot), **Initials** field (max 4, auto-uppercase), **Position** field (max 5, auto-uppercase), **Confirm Position** button. Width 150, flips above/below the dot based on `y > .55`, clamps horizontally to 20–80%, dismissed by tapping the backdrop.
  - Tap was chosen over long-press deliberately: drag is already disambiguated by the movement threshold, so a tap cannot be misread, and long-press would add a hidden ~500ms delay to the most frequent action with no affordance.
  - Implement with pointer events *and* touch fallbacks; listeners attach to `window` on pointerdown and detach on pointerup.
- **Record Play:** captures the full arrangement plus **Period** (sport-specific: Q1–Q4/OT, 1st/2nd Half/Extra Time, Inn 1–9), **Clock** (numeric, auto-formats to `M:SS`), and **Result** (— / Scored / Stopped / Turnover / Penalty / Big Gain). The selected offense and defense formation names are stored on the record.
- **Play Log:** each entry shows `Period · Clock`, result chip, timestamp, and position count, with:
  - **View Lineup** — expands to every athlete paired with their position, grouped My Team / Opponent. Unassigned slots show `—` rather than repeating the position code.
  - **Replay** — restores that exact arrangement and colors.
  - **×** — deletes the entry.
  - List has 56px bottom padding so the floating Coach AI button never covers the last row.

### 7. Coach — Score Sheets (`SportScoresheet`)
Sport-specific, tabular, editable: `FootballScoresheet` (per-quarter rows: ball on, down, player, rush yds, pass from/to/yds, notes; plus penalties, kicking game, returns), `BaseballScoresheet` (9 innings × 9 batting rows, AB/R/H/RBI/BB/SO/E), `BasketballScoresheet`, `SoccerScoresheet` (match info, half scores, per-player shots/assists/goals/conceded/saves/fouls/offside/cards), and `CustomScoresheet` driven by coach-defined metrics. **Score sheets own statistics; the field view owns positions.**

### 8. Coach — Athletes (`CAths`)
Roster list → athlete profile. Tapping a name goes **straight to the profile** (no intermediate list). Profile: header (name, height, weight, goal), horizontally aligned Check-ins toggle / **+ Add Check-in** / Stopwatch — all three the same size; mini attendance; check-in block (2 × 3 stat grid); AI Athlete Summary below the check-in block; Training Notes with **+ Note**. **+ Add Athlete** opens `AddAthleteModal` (sport/team/organization auto-derive from the selected team).
- **Stopwatch** (`StopwatchModal`): starts counting the instant the button is tapped (not when the modal opens) and stops on the same tap target. Button is teal when idle, red labeled **Reset** after stopping. Inline counter shows beside the athlete info while running. Format `MM:SS.cs`.

### 9. Coach — Calendar (`CCal`)
Mini month grid + scheduled events; event detail → game management (roster, lineup, game plan, notes). A **Community** section at the bottom with four categories: Coaching & Education, Athlete Development, Health & Safety, Recruiting & Growth, plus League Information.

### 10. Coach — Teams / Team Insight (`CStats`)
Shared **Team** picker at the top drives both analytics sections.
- **📈 Play Analytics** (`PlayAnalytics`) — computed directly from recorded plays, no prediction: Most Used Formation, Most Successful Formation, Most Used Play (offense-vs-defense pairing), Plays Recorded; **Success Rate by Formation** (bar per formation, % and count); **Frequently Used Lineups** (top 3 by usage, with success %); **Favorite Plays by coach usage** (ranked 1–3); **Play Success Timeline** (bar strip oldest → latest, full-height bar = positive, 46% = not); **Player Position Heat Map** (all recorded home positions as `r=7` `#2DD4BF` circles at opacity .16 over a field rect — density reveals hot zones). "Positive" = result of Scored or Big Gain. Empty state prompts the coach to record plays.
- **🧠 Positional Insights** (`PositionalInsights`) — inputs: **Session** (Game / Practice / Both), **Time Frame** (Last Game, Last 4 Games, Last 7 Days, Last 30 Days, Full Season, Custom Range → From/To date pickers), then **Generate Insights** (shows "Analyzing positions…" for ~550ms). Output is seven categories: Formation Effectiveness, Positioning Trends, Opponent Tendencies, Player Movement, Successful Lineups, Formation Recommendations, Practice Recommendations. Formation Effectiveness is **computed from stored plays** and carries a **FROM YOUR DATA** badge; the remaining six currently render sport-specific example patterns from `INSIGHT_LIB` and are the intended targets for real analysis in production.

### 11. Shared chrome
- **Header** — `.app-header`: eyebrow (`FLOHEALTH BUDDY` personal / `FLOHEALTH ATHLETICS` coach, 700/10px, letter-spacing 1.2px, uppercase, `--t-500`), greeting (700/18px `--ink`), date line, and a 3-dot menu button top-right. No search or bell icons.
- **3-dot menu** (`DeviceMenu`) — anchored popover at `top: 58, right: 12`, radius 14, `1.5px solid var(--t-300)`, min-width 200. Items in order: **Add Device**, **Sync Devices**, **Device Settings**, plus the **Switch to Coach / Personal View** toggle. Available in both modes.
- **Modals** — `UserProfileModal` (Personal: name/patient ID/DOB/address; Emergency contact: name/relation/phone; Primary care: provider/practice/phone; **Edit Profile** button), `AddDeviceModal` ("Connect a Health Device or Wearable to auto sync your data", status "No devices connected", **Scan for Devices** → "Device connections will be available in a future update", marked **Coming Soon**), `DeviceSettingsModal` (select Device 1 → password, two-factor authentication, biometric login). All use a `rgba(0,0,0,.5)` scrim, radius 20, `2px solid var(--t-300)` teal stroke, width 85%, max-height 80%.
- **Health AI / Coach AI** (`HealthAIPanel`) — a **floating panel over the current screen**, never a separate page, opened by a teal FAB. Personal mode = "Health AI" (medications, symptoms, procedures). Coach mode = "Coach AI" with sports-related prompts (Injury & Recovery, Training Knowledge, Coaching & Leadership).
  - **Practice Focus** (coach only): focus select — Passing, Shooting, Defense, Speed & Agility, Strength, Conditioning, Teamwork, Recovery, Position Skills, Custom — then Sport / Age Group / Players fields and **Generate Drill**.
  - **Generate Drill** renders **Recommended Drills cards** (not a chat reply): emoji + name, chips for **Focus / Time / Players**, and a **▶ View Instructions** expander with numbered steps. Two drills per focus area; the Players chip reflects the entered count.

---

## Interactions & Behavior
- **Navigation:** bottom tab bar, fixed, background `--t-500`, inactive `rgba(255,255,255,.55)` → active `#fff` (color transition 140ms). Personal tabs: Home, Scheduler, Community, Records. Coach tabs: Home, Athletes, Calendar, Teams.
- **Mode switching:** via the 3-dot menu; swaps tab set, header eyebrow, and AI assistant identity. State resets to the home tab.
- **Collapsibles:** chevron rotates 90°, 180–200ms.
- **Drag:** pointer + touch events, 3px movement threshold distinguishes drag from tap, coordinates clamped inside the field.
- **Generate Insights:** 550ms simulated analysis, button disabled and relabeled while busy.
- **Transitions:** 140–180ms on color/background/border; keep motion restrained.
- **No hover-dependent affordances** — this is a touch UI. Minimum 44px hit targets.

## State Management
Prototype state is local React `useState`, hoisted only where shared. In production most of this belongs in a store/server.

Key state: `showSplash` + primary role; `mode` (`personal` | `athletic`); `activeTab` / `coachTab`; `menuOpen` and per-modal booleans; goals, meds, mood/energy/intention; selected team (sport); attendance status map (athleteId → status); field `nodes` array, `dragIdx`, `editIdx`, `homeColor`, `awayColor`, `offForm`, `defForm`; play log array; insight inputs and generated output; stopwatch `running` / `elapsed` / `laps`.

**Persistence (localStorage in the prototype — move to real storage/API):**
- `flo_primary_role` → `"personal"` | `"coach"`
- `flo_plays_<sport>` → array of play records, newest first, capped at 30. Sport keys are `football`, `baseball`, `court` (basketball), `field` (soccer).

**Play record shape:**
```js
{
  id: 1721900000000,        // timestamp, also used for date-range filtering
  period: "Q2",
  clock: "7:45",
  result: "Scored",         // "" | Scored | Stopped | Turnover | Penalty | Big Gain
  off: "Shotgun",           // offense formation name at capture
  def: "Nickel",            // defense formation name at capture
  ts: "Jul 25, 9:30 AM",    // display timestamp
  homeColor: "#159385",
  awayColor: "#d1495b",
  nodes: [
    { x: 0.26, y: 0.50, team: "home", pos: "QB", init: "MR" }
    // x/y are normalized 0–1 against the 330 × 180 field box
  ]
}
```
`nodes` is the whole point: it is what makes "who was where at this timestamp" answerable and what the analytics aggregate.

## Design Tokens

### Colors
| Token | Hex | Use |
|---|---|---|
| `--t-50` | `#f0fdf9` | tinted fills, selected card bg |
| `--t-100` | `#ccfbef` | light accents |
| `--t-200` | `#99f6e0` | subtle borders |
| `--t-300` | `#4dd9c0` | modal borders |
| `--t-400` | `#2DD4BF` | active borders, heat-map dots |
| `--t-500` | `#159385` | **primary** — nav bar, CTAs, home team |
| `--t-600` | `#0c3d37` | headings on tint, emphasis text |
| `--bg` | `#eef4f2` | app background |
| `--surface` | `#fff` | cards, headers, modals |
| `--line` | `#e4ebe9` | hairline borders, dividers |
| `--ink` | `#1a1f2e` | primary text |
| `--faint` | `#7a8899` | secondary text, labels |

Away team `#d1495b`. Swatches: `#159385`, `#2563eb`, `#d1495b`, `#e08a1e`, `#7c3aed`, `#475569`.
Status: Active `--t-500`, Absent `#888`, Recovery `#e09b2d`, Injured `#d94f4f`.
Events: Practice `#1e40af`, Game `#9d174d`, Training `#5b21b6`, Recovery `#065f46`, Tournament `#92400e`.
Fields: soccer/football turf `#2d5a27`, baseball outfield `#4a7c3f` + infield `#b5824a`, court `#c17f3b`, rink `#bcd8f1`, track `#c8956c`, pool `#1a7ab5`.
Page behind the phone shell: `#1a2e2b` (prototype scaffolding only).

> There is **no `--t-700`**. An earlier build referenced it and rendered white-on-white. Use `--t-600` for the darkest teal.

### Typography
Family: **Inter** (300/400/500/600/700 from Google Fonts), fallback `system-ui, sans-serif`.

| Role | Spec |
|---|---|
| Screen title / greeting | 700 · 18px / 1.2 |
| Splash title | 800 · 23px / 1.1 · −.02em |
| Card title | 700 · 15px / 1 |
| Section eyebrow | 700 · 10–11px / 1 · uppercase · +.6–1.2px |
| Body | 400 · 12–13px / 1.4–1.5 |
| Micro label | 700 · 8–9px / 1 · uppercase · +.4px |
| Button | 700 · 10–12px / 1 |
| Nav label | 500 · 10px / 1 · +.3px |
| Dot initials (SVG) | 700 · 7px |
| Dot position (SVG) | 700 · 5.5px |

### Spacing
4px base. Card gutter 12px, card padding 16px, header padding `14px 16px 12px`, control gaps 5–8px, section gaps 8–12px, scroll bottom padding 70px (nav clearance), 56px extra below the play log (FAB clearance).

### Border radius
2 (micro bars) · 4–5 (chips, badges) · 6–8 (inputs, small buttons) · 10–12 (buttons, tinted blocks) · 14 (menu popover) · 16 (cards) · 20 (modals) · 44 (phone shell, prototype only) · 999 (pills).

### Shadows
- Card: none (hairline border instead)
- Modal: `0 8px 32px rgba(0,0,0,.2)`
- Popover: `0 8px 24px rgba(0,0,0,.18)`
- Player editor: `0 6px 20px rgba(0,0,0,.28)`
- Teal CTA: `0 4px 14px rgba(21,147,133,.3)`
- Logo tile: `0 6px 20px rgba(21,147,133,.3)`

## Assets
- `assets/flobrain-hand.png` — FloLabs hand mark on the splash logo tile. **White artwork**, so it requires a dark or teal backing; request a dark-ink export for light backgrounds.
- **Inter** via Google Fonts — bundle locally for production.
- All other iconography is inline SVG (`NavIcon`, `CheckIcon`) or emoji used as category glyphs. Replace emoji with the codebase's real icon set where one exists.

## Files
This repo currently holds the front-end deliverable:

- `FloHealth Mobile MVP.html` — the complete prototype (all screens, both modes). Single file: `<style>` block near the top holds tokens and shared classes; component functions follow in one `<script type="text/babel">`.
- `assets/flobrain-hand.png` — brand mark used on the splash screen.
- `README.md` — this document.

Server code, API definitions, and database schema will be added when the back end is integrated.

Useful entry points when reading the source (search by function name):
`RoleSplash` · `App` · `HomeTab` · `SchedulerTab` · `CommunityTab` · `RecordsTab` · `CDash` · `FieldDiagram` (+ module-level `FORMATIONS`, `UNIT_LABELS`) · `SportScoresheet` · `CAths` · `CCal` · `CStats` · `PlayAnalytics` · `PositionalInsights` (+ `INSIGHT_LIB`, `INSIGHT_CATS`, `TEAM_OPTS`) · `HealthAIPanel` (+ `DRILL_LIB`) · `CreateTeamModal` · `TodayAttendance` · `StopwatchModal` · `DeviceMenu` · `UserProfileModal` · `AddDeviceModal` · `DeviceSettingsModal`

## Implementation notes & known gaps
1. **Two modes, one shell** — model role as app-level state, not duplicated screens.
2. **Field view is the highest-value and highest-risk piece.** Budget real time for gesture handling (drag vs tap, clamping, touch parity) and keep positions normalized 0–1 so the board scales across screen sizes.
3. **Score sheet vs field view is a deliberate separation** — statistics on the sheet, positions on the board. The insights feature exists specifically to mine the position data.
4. **Six of seven insight categories are illustrative examples**, not computed. Only Formation Effectiveness derives from stored data. These are product requirements awaiting a real analysis backend.
5. **Play log caps at 30 entries per sport** and is device-local — needs server persistence for multi-device and season-long history.
6. **Opponent dots default to position codes** because there's no opposing roster; the coach can tap to enter initials or jersey numbers.
7. **Custom Sport teams** (`CreateTeamModal`) let a coach define their own categories and score-sheet metrics — the schema is dynamic, so design the data model for user-defined metrics from the start.
8. **Session filter is not yet wired to play records** — Game/Practice is currently a framing label; if it should filter, add a session field to the play record at capture time.
9. **No auth, no backend, no real AI.** Chat replies, drills, and most insights are keyword-matched or static fixtures. This is the front-end MVP — back-end integration is the next milestone (see **Back end — not yet integrated** at the top).
