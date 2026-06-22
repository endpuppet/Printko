# PROJECT STATUS TRACKER

**CRITICAL INSTRUCTION FOR AI:** This is the single source of truth for this project. You MUST read this file before starting any work. You MUST update this file immediately after completing any task.

## 1. Project Overview

**Printko** is a custom assistant web app for the eufy E1 UV printer, centered around an animated character avatar named **Printko** — shaped like an actual printer (control panel face screen, output tray with paper, status LEDs). The app tracks prints, finances, projects, and inventory, and lets the user chat with the assistant via a redesigned FAB button (press = menu icons slide out in an arc + chat input expands, hold = speech recognition). The UI is Slovenian-first, uses light relaxed colors and minimalism, and now ships with **3 independent background axes — Location × Weather × Time-of-Day** (v15) that compose freely: 4 locations (forest / beach / ocean / mountains), 6 weather overlays (clear / cloudy / rain / snow / fog / storm), 4 time-of-day phases (dawn / day / dusk / night, auto-advancing every 10 min). The avatar has 31 emotions, **3 RGB antennas** (red / green / blue) that pulse to signal answer type (success / error / info), eyes that track mouse/taps, sleeps when idle, wakes annoyed, and floats higher above ground. Suggestions appear as **thought-bubble icon circles** around the avatar's head on tap (hidden by default, auto-hide after 8s). Printko has a cozy personality (calls user by name, default: Lara) with a configurable preset bank + variation generator. All UI elements constrained to max ¼ screen width. Structured chat-output panel sits between the input and the avatar.

Current phase: **v15.0 — independent Location × Weather × Time-of-Day backgrounds, 3 RGB antennas, thought-bubble suggestion icons, redesigned FAB layout with slide-out menu + structured chat panel shipped.** Notion sync is real (fetch + CORS fallback). OpenRouter agent is real (with offline fallback). Auto-weather (geolocation + Open-Meteo) now sets the `curWeather` axis.

## 2. File Map (The Architecture)

```
Printko/
├── index.html           # the entire app (HTML + CSS + JS inline, ~5450 lines) — CURRENT VERSION (v15)
├── AI_CODING_RULES.md   # 6 rules every AI contributor must follow
├── PROJECT_STATUS.md    # this file — single source of truth
├── README.md            # repo readme
├── .gitignore
└── archive/             # DEPRECATED older versions — DO NOT USE
    ├── README.md
    ├── eufy-dashboard-v6-1.html   # v6
    ├── eufy-dashboard-v7.html     # v7
    ├── eufy-studio.html           # v8
    ├── printko-v9.html            # v9
    ├── printko-v10.html           # v10
    ├── printko-v11.html           # v11
    ├── printko-v12.html           # v12
    ├── printko-v13.html           # v13
    └── printko-v14.html           # v14 (archived 2026-04 with deprecation header)
```

### Inside `index.html`
- **CSS section** (~lines 11–2140): 9 legacy themes (kept for backward compat), 31-emotion visibility rules, weather animations, v13 report icons + FAB + dynamic avatar sizing, v14 fireflies/rainbow/lightning/paper animation, **v15: independent background axes CSS vars (--sky-top/mid/bot, --time-overlay), 3-RGB-antenna glow + signal animations, thought-bubble suggestion bubbles, custom FAB icon, slide-out menu fan, structured chat-output panel, axis-card settings UI, time-of-day slider, all UI constrained to max 25vw, Printko floats higher (14px amplitude) with softer shadow**.
- **HTML body** (~lines 2141–2730): layered scene background + time-of-day overlay, weather-fx, lightning flash, topbar (lang / **location cycle button** / settings), main stage (**3-RGB-antenna printer avatar** with top paper feed slot REMOVED, status label DIRECTLY BELOW avatar, thought-bubble suggestions, speech, **structured chat-output panel**, **hidden legacy report-icons row**, **redesigned FAB with custom waveform+bubble+dots icon**, **slide-out fab-menu**, expanded chat input at center-bottom, listening overlay), 6 slide-in panels, settings modal with **Location / Weather / Time-of-Day axis sections + manual time slider**.
- **JS section** (~lines 2731–end): I18N (SL + EN with axis labels), THEMES + THEME_META (legacy, kept for backward compat), **LOCATION_DEFS (4) + WEATHER_DEFS (6) + TIME_DEFS (4) + THEME_TO_AXES mapping**, mock DB, state (incl. **curLocation / curWeather / curTimeOfDay / timeAutoAdvance / suggestionsVisible**), utils, toast, **setLocation / setWeather / setTimeOfDay / cycleLocation / startTimeAutoAdvance / pauseTimeAutoAdvance / updateAxisUI / renderAxisGrids / applyTimeCSS**, theme (legacy setTheme now derives axes), language, avatar (emotions, blink, eye tracking, sleep/wake, **signalAnswer(type)** for RGB antennas), personality (v12), **showSuggestions / hideSuggestions / toggleSuggestions / resetSuggestionHideTimer + SUGGESTION_ICONS array**, **renderFabMenu / collapseFabMenu**, FAB logic (v13 fabClick/fabClose/fabHoldStart/fabHoldEnd updated for slide-out menu + signalAnswer), **openChatOutput / closeChatOutput / appendChatUser / appendChatAI / clearChatOutput**, weather-fx (now driven by curWeather), speech reveal (also writes to chat-output panel), TTS (v14), panels, chat (sendMsg signals antenna + opens chat-output), real OpenRouter API (v14), real Notion sync (v14), auto-weather via geolocation + Open-Meteo (now sets curWeather via weatherCodeToWeather), settings, boot.

## 3. Completed Features

- [x] **Independent Location × Weather × Time-of-Day backgrounds (v15 NEW)** — 3 axes that compose freely. 4 locations (forest with pine trees + birds + leaves; beach with sand dunes + palms + gulls + crabs; ocean with waves + fish + sailboat + dolphins; mountains with peaks + snow caps + eagles + avalanche-when-snowing). 6 weather overlays (clear with sun rays; cloudy with drifting clouds; rain with raindrops; snow with snowflakes; fog with fog clouds; storm with rain + lightning). 4 time-of-day phases (dawn pink; day blue; dusk orange/purple; night indigo + stars + moon + fireflies). Time auto-advances every 10 min (or manual via slider). All color transitions animated via `transition: background 2s ease, fill 2s ease`. (Implemented in: `LOCATION_DEFS` / `WEATHER_DEFS` / `TIME_DEFS` constants, `THEME_TO_AXES` mapping, `renderScene()` rewrite, `applyTimeCSS()`, `.scene-time-overlay` div, CSS vars `--sky-top/mid/bot` + `--time-overlay`)
- [x] **3 RGB antennas + signalAnswer (v15 NEW)** — Top paper feed slot REMOVED. Single antenna replaced with 3 antennas of different lengths: left (x=95, 20px, RED `#e84a4a`), center (x=120, 30px, GREEN `#4ad04a`), right (x=145, 25px, BLUE `#4a8ae8`). Each has a glowing tip that pulses in its color. `signalAnswer(type)` adds `.signal-success` / `.signal-error` / `.signal-info` class for 3s; matching antenna pulses bright while other two dim. Idle = all 3 pulse gently. (Implemented in: avatar SVG `<g class="antenna antenna-red/green/blue">` blocks, `.piko .antenna-tip` + `.antenna-red/green/blue .antenna-tip` CSS with `@keyframes antennaSignalGreen/Red/Blue`, `signalAnswer()` function wired into `sendMsg()` + `avatarClick()` + `askSuggestion()`)
- [x] **Thought-bubble suggestion icons on avatar tap (v15 NEW)** — Suggestions now HIDDEN by default; only appear when Printko is tapped (avatarClick toggles them). Rendered as 6 fun icon circles (📊 chart / 💰 money / 📋 clipboard / 🔮 crystal ball / 📈 bar chart / 📦 package) floating around his head like thought bubbles, with gentle floating + pop-in stagger animation. Tap an icon → Printko reacts + sends the associated question. Auto-hide after 8s of no interaction or when chat starts. (Implemented in: `SUGGESTION_ICONS` array, `renderSuggestions()` rewrite, `showSuggestions()` / `hideSuggestions()` / `toggleSuggestions()` / `resetSuggestionHideTimer()`, `.suggestions-pop` + `.sugg-bubble` CSS, updated `avatarClick()`)
- [x] **FAB + UI layout redesign (v15 NEW)** — Custom FAB SVG icon merging speech waveform + message bubble + 3 dots (options). All UI elements constrained to max ¼ viewport width (`min(25vw, 300px)`). FAB press behavior: menu icon-buttons (6 report icons) slide out from behind the FAB in a fan/arc pattern + small chat input appears at center-bottom. Structured chat-output panel sits between input and Printko (above input, below avatar) showing conversation in Caveat font with headings/lists/bold/code, scrollable. FAB itself stays as the anchor. (Implemented in: `.fab-wrap` 25vw width, `.fab-menu` slide-out arc CSS, custom FAB SVG, `.chat-output` structured panel CSS, `renderFabMenu()` / `collapseFabMenu()`, `openChatOutput()` / `appendChatUser()` / `appendChatAI()`, updated `fabClick()` + `sendMsg()` + `showSpeech()`)
- [x] **Status label directly below avatar (v15 NEW)** — moved `.avatar-status` to sit DIRECTLY BELOW the avatar SVG, BEFORE the speech text. It never moves. (Implemented in: HTML reorder inside `.avatar-wrap`, `.avatar-status` margin-top/bottom adjustments)
- [x] **Backward-compat theme mapping (v15 NEW)** — Old 9 themes (trta / jelka / morje / solnce / noc / mleko / dez / sneg / meglja) kept for backward compat and mapped to (location, weather, time) triples via `THEME_TO_AXES`. `setTheme(name)` derives the 3 axes from this map. Old saved `es-theme` localStorage key still works.
- [x] **Auto-weather now sets curWeather axis (v15 UPDATED)** — `detectWeather()` calls `weatherCodeToWeather(code)` and `setWeather(w)` instead of `setTheme(theme)`. New `weatherCodeToWeather()` maps WMO codes to `clear / cloudy / rain / snow / fog / storm`. Old `weatherCodeToTheme()` kept for backward compat.
- [x] **Time-of-day auto-advance (v15 NEW)** — `startTimeAutoAdvance()` runs a `setInterval` every 10 minutes that cycles dawn → day → dusk → night → dawn. `pauseTimeAutoAdvance(ms)` pauses for 30 min when the user moves the manual slider. (Implemented in: `startTimeAutoAdvance()` / `pauseTimeAutoAdvance()`, `TIME_ADVANCE_INTERVAL=600000` constant, slider `oninput` handler in settings modal)
- [x] **v14 features retained** — Real OpenRouter API call (`callLiveAgent`), real Notion sync (`syncNotion`), auto-weather via geolocation + Open-Meteo, TTS via Web Speech API, fireflies/rainbow/lightning/paper animation. All preserved and working.
- [x] **v13 features retained** — Printer-shaped avatar, FAB button with expand + hold-to-talk, dynamic avatar sizing (has-report / is-listening), layered scene backgrounds, report icons, Web Speech API integration.
- [x] **v12 features retained** — Personality system (name, tone, shape, presets, variation generator).
- [x] **v11 features retained** — 31 emotions, attitude emotions, annoyed wake-up.
- [x] **v10 features retained** — Emotion system, iconic backgrounds, mobile responsive, no hands/arms.

## 4. In-Progress / Next Features

- [ ] **Real OpenRouter streaming** — currently single-shot completion. (Status: Queued)
- [ ] **More location-specific elements** — beach seagulls could carry shells; mountains could have avalanches triggered by storm. (Status: Queued)
- [ ] **Time-of-day reactive avatar** — Printko's screen could glow brighter at night. (Status: Queued)
- [ ] **AI-powered variation generation** — currently local template combinator. (Status: Queued)
- [ ] **Project / print detail views** — clicking a card should open detail drawer. (Status: Queued)
- [ ] **Add/edit forms** — panels are read-only. (Status: Queued)
- [ ] **Chat history persistence** — in-memory only. (Status: Queued)
- [ ] **Report icon real-time data** — badges are static. (Status: Queued)
- [ ] **FAB hold on desktop** — `onmousedown`/`onmouseup` not yet wired (touch only). (Status: Queued)

## 5. Architectural Decisions & Constraints

- **Single self-contained `index.html`** — no build step, no dependencies, no server.
- **CSS-variable-driven theming** — `data-theme="name"` on `<html>` (legacy) + CSS vars `--sky-top/mid/bot` + `--time-overlay` (v15) driven by JS.
- **v15: 3 INDEPENDENT BACKGROUND AXES (canonical)** — `curLocation` × `curWeather` × `curTimeOfDay` are independent state vars. `LOCATION_DEFS` / `WEATHER_DEFS` / `TIME_DEFS` are the canonical definitions. `renderScene()` combines all 3. Do NOT revert to the old single-theme-driven background.
- **v15: THEMES array kept for backward compat** — `THEME_TO_AXES` maps old theme names to (location, weather, time) triples. `setTheme()` derives axes. Do NOT remove the THEMES array.
- **v15: Topbar sun button cycles LOCATIONS** (not themes). `cycleLocation()` is the canonical function. `cycleTheme()` is kept for backward compat.
- **v15: Auto-weather sets curWeather axis** — `detectWeather()` → `weatherCodeToWeather()` → `setWeather()`. Do NOT revert to setTheme.
- **v15: Time-of-day auto-advances every 10 min** — `startTimeAutoAdvance()` + `TIME_ADVANCE_INTERVAL=600000`. Manual slider pauses for 30 min. Do NOT remove auto-advance.
- **v15: All color transitions animated** — `transition: background 2s ease, fill 2s ease` on `.scene-sky` / `.scene-ground` / `.scene-horizon` and `body`. Do NOT remove transitions.
- **v15: Printko floats higher (14px amplitude)** — `@keyframes float{...50%{transform:translateY(-14px)}}` and softer shadow (`opacity:.12` + `filter:blur(1px)`). Do NOT revert to the old 6px float.
- **v15: 3 RGB antennas are canonical** — `.antenna-red` / `.antenna-green` / `.antenna-blue` groups in the SVG. `signalAnswer(type)` adds `.signal-success/error/info` for 3s. Do NOT revert to single antenna. Top paper feed slot is REMOVED — do NOT re-add it.
- **v15: Suggestions are hidden by default** — `.suggestions-pop` has `opacity:0;visibility:hidden` until `.show` class is added. `avatarClick()` toggles them. Auto-hide after 8s. Do NOT make suggestions always visible.
- **v15: Suggestions are icon bubbles (not text pills)** — `.sugg-bubble` with emoji icons. Do NOT revert to text pills.
- **v15: Custom FAB SVG icon** — merged waveform + bubble + 3 dots. Do NOT revert to mic icon.
- **v15: All UI max 25vw** — FAB, chat panel, input, menu buttons all constrained to `min(25vw, 300px)` on desktop. Do NOT let UI elements grow wider than ¼ of the viewport.
- **v15: FAB press = menu slides out + input appears** — `fabClick()` adds `.pressed` to `.fab-wrap` which fans out the 6 report icons in an arc. Do NOT revert to the old "click = expand input only" behavior.
- **v15: Status label DIRECTLY below avatar** — sits between avatar-bottom and speech text, never moves. Do NOT move it back below speech.
- **v15: Structured chat-output panel** — `.chat-output` at `position:fixed; left:50%; bottom:140px; width:min(25vw,300px)`. Shows conversation in Caveat font with headings/lists/bold/code. Scrollable. Do NOT remove.
- **Emotion system = CSS class swap + EMOTIONS table** — SVG contains all variants; CSS shows the matching trio.
- **31-emotion vocabulary is canonical** — adding requires EMOTIONS table + CSS rule.
- **PERSONALITY system is canonical** (v12) — `personality` state, `PRESETS` bank, `pickPreset(category)`. Do NOT hardcode greetings.
- **Avatar is printer-shaped (v13)** — the SVG has chassis, face screen, output tray, paper, LEDs. The `data-shape` attribute from v12 controls the chassis corner radius. Top paper feed slot REMOVED in v15.
- **Curved-arch blink uses SVG `d:path()` (v13)** — the eyelid is a path with arc commands. Do NOT revert to flat rect blink.
- **FAB button is the primary input (v13)** — the old always-visible chat bar is gone. v15 redesign: FAB press = slide-out menu + chat input appears at center-bottom. Do NOT re-add a permanent chat bar.
- **Dynamic sizing via `.stage.has-report` and `.stage.is-listening` (v13)** — Printko is large by default, shrinks when report shows, grows when listening. Do NOT remove these classes.
- **Layered scene backgrounds (v13, REWRITTEN in v15)** — `renderScene()` now combines 3 axes. Legacy `SCENE_DEFS` constant kept for backward compat. Do NOT revert to single-theme-driven scene.
- **Sky circles clockwise (v13)** — `.scene-sky` has `animation:skyCircle 120s linear infinite`. Do NOT remove or reverse.
- **Web Speech API for hold-to-talk (v13)** — `speechRecog` uses `SpeechRecognition` with `sl-SI`/`en-US`. Falls back gracefully.
- **Wake-up is "annoyed"** (v11) — `wakeUp()` uses `pickPreset('wakeups')`.
- **Avatar click uses personality greeting + toggles suggestions (v15)** — `avatarClick()` toggles suggestions AND uses `pickPreset('greetings')`.
- **Pop-out icon bubbles around head (v15)** — absolute positioning in `.avatar-wrap`. Hidden by default.
- **No blue tap highlight** (v11) — `-webkit-tap-highlight-color:transparent` everywhere.
- **Global click-to-look** (v12) — any click on empty space.
- **Face stays centered** (v12) — no translateX on facing-left/right.
- **Slovenian default** — all strings in both SL + EN.
- **No comic-book speech cloud** — plain Caveat text below avatar.
- **Avatar has NO hands/arms** (v10) — do NOT re-add.
- **`localStorage` keys prefixed `es-`** — `es-theme`, `es-lang`, `es-or-key`, `es-notion-key`, `es-notion-db`, `es-model`, `es-personality`, `es-tts`, `es-auto-weather`, **v15 new: `es-location`, `es-weather`, `es-time`**.
- **Deprecated files in `/archive/`** — v14 archived in v15 with deprecation header.

## 6. Known Bugs / Technical Debt

- **Vision-model false negatives** on subtle emotions — verify via `getComputedStyle`.
- **`chatHistory` is in-memory only** — lost on reload.
- **Speech recognition requires HTTPS or localhost** — won't work on `file://` in some browsers.
- **`d:path()` CSS property for blink** — works in modern browsers (Chrome 88+, Firefox 97+). Falls back gracefully.
- **FAB hold on desktop** — `onmousedown`/`onmouseup` are not yet wired (only touch). Desktop users can click to expand + type. Add mouse hold in a future iteration.
- **Scene sky rotation may cause slight rendering overhead** — the SVG is large (2000×2000). Acceptable on modern devices.
- **Report icon badges are static** — should pull from DB dynamically in a future iteration.
- **`THEME_TO_AXES` mapping is lossy** — e.g. `noc` (night) maps to mountains, but the original v14 `noc` theme had a unique dark/indigo palette. Time-of-day `night` approximates this but isn't identical.
- **Time-of-day overlay uses `mix-blend-mode:multiply`** — may interact unexpectedly with weather particle colors in some browsers.
- **`transform-box:fill-box` on antenna tips** — required for SVG `transform-origin:center` to work on circles. Supported in modern browsers; older browsers may show antenna tips scaling from a wrong origin.
- **25vw constraint on tiny phones** — on a 380px-wide phone, 25vw = 95px which is very narrow for the chat-output panel. The `@media(max-width:380px)` rule relaxes this to `min(60vw, 280px)` for readability.
- **The `GRUMPY_DURATION` constant name is misleading** (v11) — governs annoyed phase.
- **Legacy `.antenna-glow` class still referenced in old CSS** — applies to the outer halo circles of the new 3-antenna design; the v15 `.antenna-tip` rules override the animation. No breakage but the old `transform-origin:120px 22px` is stale.
