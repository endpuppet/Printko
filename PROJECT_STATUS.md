# PROJECT STATUS TRACKER

**CRITICAL INSTRUCTION FOR AI:** This is the single source of truth for this project. You MUST read this file before starting any work. You MUST update this file immediately after completing any task.

## 1. Project Overview

**Printko** is a custom assistant web app for the eufy E1 UV printer, centered around an animated character avatar named **Printko** — now shaped like an actual printer (with paper feed slot, control panel face screen, output tray with paper, status LEDs). The app tracks prints, finances, projects, and inventory, and lets the user chat with the assistant via a FAB button (press to expand input, hold for speech recognition). The UI is Slovenian-first, uses light relaxed colors and minimalism, and ships with 9 weather-themed **layered scene backgrounds** (sky circling clockwise + horizon + ground/sea + clouds + animated birds/butterflies/leaves). The avatar has 31 emotions, eyes that track mouse/taps, sleeps when idle, wakes annoyed, and dynamically resizes (large when idle/listening, shrinks + moves up when report space is needed). Quick-access is via 6 meaningful report icons. Printko has a cozy personality (calls user by name, default: Lara) with a configurable preset bank + variation generator.

Current phase: **v13.0 — printer-shaped avatar + FAB + speech recognition + layered scene backgrounds + report icons shipped.** Notion sync is stubbed. OpenRouter agent is offline-only.

## 2. File Map (The Architecture)

```
Printko/
├── index.html           # the entire app (HTML + CSS + JS inline, ~3950 lines) — CURRENT VERSION
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
    └── printko-v12.html           # v12
```

### Inside `index.html`
- **CSS section** (~lines 10–1490): 9 themes, 31-emotion visibility rules, weather animations, **v13: report icons, FAB button + expanded panel + listening overlay, dynamic avatar sizing (has-report/is-listening), curved-arch blink (SVG path), layered scene (sky circling + horizon + ground), paper-sheet feed animation**.
- **HTML body** (~lines 1490–1950): **v13: layered scene background (scene-sky + scene-horizon + scene-ground + scene-deco)**, weather-fx, topbar, main stage (**printer-shaped avatar** + pop-out pills + speech + **report icons** + **FAB wrap with panel + listening overlay**), 6 slide-in panels, settings modal with personality subsection.
- **JS section** (~lines 1950–3950): I18N, themes, mock DB, state, **v13: SCENE_DEFS + renderScene()**, personality system (v12), EMOTIONS table (31), avatar functions, **v13: FAB logic (fabClick/fabClose/fabHoldStart/fabHoldEnd) + speech recognition (Web Speech API)**, **v13: dynamic sizing triggers (has-report on sendMsg, is-listening on hold)**, weather-fx, speech reveal, pop-out pills, **v13: report icon rendering**, panels, chat, settings, boot.

## 3. Completed Features

- [x] **Printer-shaped avatar (v13 NEW)** — Printko now looks like an actual printer: top paper feed slot, main chassis with face screen (control panel display), 3 control LED buttons on the right, paper output tray with a sheet of paper coming out (with text lines), status LED strip, side vents, "eufy E1 UV" brand label. The face screen holds all the eye/eyebrow/mouth emotion variants. (Implemented in: avatar SVG)
- [x] **Curved-arch blink (v13 FIXED)** — blink now uses SVG `d:path()` with arc commands matching the eye's circular shape, so the eyelid curves over the eye instead of being a flat rectangle. (Implemented in: `.avatar.blinking.emotion-* .piko .lid-l/lid-r` CSS with `d:path('M83 118 A 11 11 0 0 1 105 118...')`)
- [x] **Report icons replace quick-access pills (v13 NEW)** — 6 meaningful report icons: Črnilo (ink drop), Finance (euro), Projekti (folder), Tiski (printer), Zgodovina (clock), Novice (newspaper). Each has an SVG icon + label + optional badge. No blue tap highlight. (Implemented in: `.report-icons`/`.report-icon` CSS, `renderQuickAccess()`)
- [x] **FAB button with expand + hold-to-talk (v13 NEW)** — a floating action button (bottom-right) that:
  - **Click**: expands into a 3-line text input panel with send + close buttons
  - **Hold (300ms+)**: vibrates (if supported), turns red, shows listening overlay, starts Web Speech API recognition, and Printko grows larger
  - **Release**: stops recognition, auto-opens input panel with recognized text
  - (Implemented in: `.fab-wrap`/`.fab`/`.fab-panel`/`.fab-listening` CSS, `fabClick()`/`fabClose()`/`fabHoldStart()`/`fabHoldEnd()`, `SpeechRecognition` API)
- [x] **Dynamic avatar sizing (v13 NEW)** — Printko is **large by default** (idle). When the user sends a message (report space needed), the `.stage` gets `has-report` class → Printko shrinks to 150px and moves up 10px. When the FAB is held (listening), `.stage` gets `is-listening` → Printko grows to 230px. Returns to large after speech completes (3s delay). (Implemented in: `.stage.has-report .avatar`, `.stage.is-listening .avatar` CSS, `sendMsg()` adds has-report, `setAvatarState('idle')` removes it after 3s, `fabHoldStart/End` toggles is-listening)
- [x] **Layered scene backgrounds (v13 NEW)** — each theme now renders 3 layers:
  1. **Sky** (circles slowly clockwise over 120s) — gradient + sun/moon/stars/clouds/comet depending on theme
  2. **Horizon** — a subtle horizontal line at 30% from bottom
  3. **Ground/sea** (bottom 30%) — theme-specific: grass, forest (pine trees), sea (waves + fish + sailboat), desert, snow (snowman), wet, flat, dark, or fog
  - (Implemented in: `SCENE_DEFS` constant, `renderScene()`, `.scene-sky`/`.scene-horizon`/`.scene-ground` CSS)
- [x] **More animated elements (v13 NEW)** — each scene includes more ambient life:
  - Birds: 5 flying across at different speeds (all themes with sky)
  - Butterflies: 3 fluttering with wing flap animation (Trta, Solnce)
  - Falling leaves: 8 drifting down (Trta, Jelka)
  - Fish: 2 swimming back and forth (Morje)
  - Sailboat: bobbing on waves (Morje)
  - Snowman: swaying (Sneg)
  - Stars: 40 twinkling (Noč)
  - Comet: occasional fly-by (Noč)
  - Clouds: 5 drifting (all themes with clouds)
  - (Implemented in: `renderScene()` deco layer)
- [x] **All weather conditions (v13)** — 9 themes covering: sunny (Trta, Solnce), forest (Jelka), sea (Morje), night (Noč), minimal (Mleko), rain (Dež), snow (Sneg), fog (Meglja). Each with appropriate sky, ground, and animated elements.
- [x] **Web Speech API integration (v13 NEW)** — hold-to-talk uses `window.SpeechRecognition` / `window.webkitSpeechRecognition` with `sl-SI` or `en-US` locale based on UI language. Interim results populate the input + listening overlay text. Gracefully degrades if not supported. (Implemented in: `speechRecog` object, `fabHoldStart/End`)
- [x] **31-emotion system** (v10-v12) — 8 families, 15 eye shapes, 8 eyebrow shapes, 17 mouth shapes.
- [x] **Personality system** (v12) — user name (default Lara), tone (cozy/formal/playful), avatar shape (round/square/hex/blob), preset bank with 4 categories + variation generator.
- [x] **Pop-out suggestion pills** (v11) — 6 pills around avatar head, tap triggers reaction.
- [x] **Smart eye tracking** — mouse follow within 350px, velocity-based look direction, focus-lock.
- [x] **Global click-to-look** (v12) — any click on empty space makes Printko look at that point.
- [x] **Sleep/wake cycle** — 45s idle → sleeps, wakes annoyed (v11) with personality preset (v12).
- [x] **Face direction switching** — every 5-9s, face stays centered (v12 fix).
- [x] **Cozy speech in Caveat font** — Printko's replies use handwriting font.
- [x] **Fully responsive** — tablet, mobile, very small, landscape, reduced-motion.
- [x] **Slovenian default + English toggle** — all strings bilingual.
- [x] **6 slide-in panels** — News, Projects, History, Ink, Finances, Prints.
- [x] **Offline agent** — keyword-based context-aware replies.
- [x] **Settings modal** — themes, language, personality subsection, API keys.

## 4. In-Progress / Next Features

- [ ] **Real OpenRouter API call** — `callLiveAgent()` not yet implemented. (Status: Queued)
- [ ] **Real Notion sync** — `syncNotion()` only toasts. (Status: Queued)
- [ ] **AI-powered variation generation** — currently local template combinator. (Status: Queued)
- [ ] **Auto weather detection** — geolocation + weather API to auto-pick theme. (Status: Queued)
- [ ] **Printko voice (TTS)** — Web Speech API for spoken replies. (Status: Queued)
- [ ] **Project / print detail views** — clicking a card should open detail drawer. (Status: Queued)
- [ ] **Add/edit forms** — panels are read-only. (Status: Queued)
- [ ] **Chat history persistence** — in-memory only. (Status: Queued)
- [ ] **More scene elements** — fireflies for night, rainbows after rain, lightning in storms. (Status: Queued)
- [ ] **More printer-shaped details** — paper advancing animation when printing, ink cartridges visible. (Status: Queued)

## 5. Architectural Decisions & Constraints

- **Single self-contained `index.html`** — no build step, no dependencies, no server.
- **CSS-variable-driven theming** — `data-theme="name"` on `<html>`.
- **Emotion system = CSS class swap + EMOTIONS table** — SVG contains all variants; CSS shows the matching trio.
- **31-emotion vocabulary is canonical** — adding requires EMOTIONS table + CSS rule.
- **PERSONALITY system is canonical** (v12) — `personality` state, `PRESETS` bank, `pickPreset(category)`. Do NOT hardcode greetings.
- **Avatar is printer-shaped (v13 NEW)** — the SVG has paper feed slot, chassis, face screen, output tray, paper, LEDs. Do NOT revert to the old blob shape. The `data-shape` attribute from v12 still controls the chassis corner radius (round/square/hex/blob).
- **Curved-arch blink uses SVG `d:path()` (v13 NEW)** — the eyelid is a path with arc commands matching the eye circle, not a flat rect. Do NOT revert to `height:20px` rect blink.
- **FAB button is the primary input (v13 NEW)** — the old always-visible chat bar is gone. The FAB at bottom-right expands on click, holds for speech. Do NOT re-add a permanent chat bar.
- **Dynamic sizing via `.stage.has-report` and `.stage.is-listening` (v13 NEW)** — Printko is large by default, shrinks when report shows, grows when listening. Do NOT remove these classes.
- **Layered scene backgrounds (v13 NEW)** — `SCENE_DEFS` constant defines sky/ground/elements per theme. `renderScene()` builds the SVG. Do NOT revert to the old single-layer ambient decorations.
- **Sky circles clockwise (v13 NEW)** — `.scene-sky` has `animation:skyCircle 120s linear infinite`. Do NOT remove or reverse.
- **Report icons replace quick-access pills (v13 NEW)** — `.report-icons` with `.report-icon` buttons. Do NOT revert to the old pill layout.
- **Web Speech API for hold-to-talk (v13 NEW)** — `speechRecog` uses `SpeechRecognition` with `sl-SI`/`en-US`. Falls back gracefully. Do NOT replace with a third-party service.
- **Wake-up is "annoyed"** (v11) — `wakeUp()` uses `pickPreset('wakeups')`.
- **Avatar click uses personality greeting** (v12) — `avatarClick()` uses `pickPreset('greetings')`.
- **Pop-out pills around head** (v11) — absolute positioning in `.avatar-wrap`.
- **No blue tap highlight** (v11) — `-webkit-tap-highlight-color:transparent` everywhere.
- **Global click-to-look** (v12) — any click on empty space.
- **Face stays centered** (v12) — no translateX on facing-left/right.
- **Slovenian default** — all strings in both SL + EN.
- **Light relaxed colors** — Noč is the only dark theme.
- **No comic-book speech cloud** — plain Caveat text below avatar.
- **Avatar has NO hands/arms** (v10) — do NOT re-add.
- **`localStorage` keys prefixed `es-`** — `es-theme`, `es-lang`, `es-or-key`, `es-notion-key`, `es-notion-db`, `es-model`, `es-personality`.
- **Deprecated files in `/archive/`** — v12 added in v13.

## 6. Known Bugs / Technical Debt

- **Vision-model false negatives** on subtle emotions — verify via `getComputedStyle`.
- **`chatHistory` is in-memory only** — lost on reload.
- **No real API calls yet** — OpenRouter and Notion are stubs.
- **Speech recognition requires HTTPS or localhost** — won't work on `file://` in some browsers. User should serve via local server or deploy.
- **`d:path()` CSS property for blink** — works in modern browsers (Chrome 88+, Firefox 97+). Falls back gracefully (no blink animation) on older browsers.
- **FAB hold on desktop** — `onmousedown`/`onmouseup` are not yet wired (only touch). Desktop users can click to expand + type. Add mouse hold in a future iteration.
- **Scene sky rotation may cause slight rendering overhead** — the SVG is large (2000×2000). Acceptable on modern devices.
- **Report icons don't show real-time data** — badges are static (e.g. "7" prints, "€191" profit). Should pull from DB dynamically in a future iteration.
- **Paper sheet animation is subtle** — only 2px translateY. Could be more dramatic when a print is actually happening.
- **The `GRUMPY_DURATION` constant name is misleading** (v11) — governs annoyed phase.
