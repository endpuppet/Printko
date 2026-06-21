# PROJECT STATUS TRACKER

**CRITICAL INSTRUCTION FOR AI:** This is the single source of truth for this project. You MUST read this file before starting any work. You MUST update this file immediately after completing any task.

## 1. Project Overview

**Printko** is a custom assistant web app for the eufy E1 UV printer, centered around an animated character avatar named Printko. The app tracks prints, finances, projects, and inventory, and lets the user chat with the assistant. It is designed to connect to a Notion database for live data sync. The UI is Slovenian-first (with English toggle), uses light relaxed colors and minimalism, and ships with 9 weather-themed animated backgrounds — each with iconic animated elements (butterflies, birds, fish, sailboats, moon, snowman, etc.). The avatar has deep character — eyebrows, eyes, and mouths combine into 23 named emotions, the eyes smoothly track the mouse when near and look in the direction of motion, he occasionally turns to face left/right, sleeps when idle for 45s, and wakes up grumpy for 4s.

Current phase: **v10.0 — emotion system + iconic background animations shipped.** Notion sync is stubbed (UI wired, sample data shown, real API call not yet implemented). OpenRouter agent is offline-only with a context-aware local fallback.

## 2. File Map (The Architecture)

```
Printko/
├── index.html           # the entire app (HTML + CSS + JS inline, ~2760 lines) — CURRENT VERSION
├── AI_CODING_RULES.md   # 6 rules every AI contributor must follow
├── PROJECT_STATUS.md    # this file — single source of truth
├── README.md            # repo readme
├── .gitignore
└── archive/             # DEPRECATED older versions — DO NOT USE
    ├── README.md         # explains what's deprecated and why
    ├── eufy-dashboard-v6-1.html   # v6 (original upload, pre-redesign)
    ├── eufy-dashboard-v7.html     # v7 (3-column focus mode)
    ├── eufy-studio.html           # v8 (centered Piko avatar, before rename)
    └── printko-v9.html            # v9 (Printko with weather themes, before emotion system)
```

### Inside `index.html`
- **CSS section** (~lines 10–1120): 9 illustrated themes, eyebrow/eye/mouth emotion visibility rules, weather animation keyframes, extra ambient animations (butterfly, birds, fish, sailboat, moon, snowman), panel/modal styles, full responsive breakpoints (tablet/mobile/very-small/landscape/reduced-motion).
- **HTML body** (~lines 1120–1750): ambient background SVG decorations (with iconic elements per theme), weather-fx particle layer, topbar, main stage (avatar + speech + suggestions + chat bar + quick-access pills), 6 slide-in panels, settings modal.
- **JS section** (~lines 1750–2760): I18N strings (SL/EN), theme metadata, mock DB, state, utils, toast, theme management, language toggle, **EMOTIONS system table** (23 emotions → eye+eyebrow+mouth combos), avatar functions (emotion, blink, eye tracking with velocity, sleep/wake, face direction), weather-fx particle generation, speech reveal, suggestions, quick access, panel rendering, chat with keyword-based offline agent, settings persistence, boot/init.

## 3. Completed Features

- [x] **Centered avatar "Printko"** — friendly robot-printer SVG with antenna, face screen, body, status light. Hands/arms removed in v10 for cleaner look. (Implemented in: `index.html` avatar SVG block)
- [x] **9 illustrated weather themes** — Trta, Jelka, Morje, Solnce, Noč, Mleko, Dež, Sneg, Meglja. Cycled via topbar sun button or picked in Settings. (Implemented in: CSS theme blocks, ambient SVG, `THEMES`/`THEME_META`, `setTheme()`/`cycleTheme()`)
- [x] **Animated iconic background elements per theme** (v10 expansion):
  - Trta: butterfly flutters across with flapping wings
  - Jelka: 3 birds fly across at different speeds
  - Morje: sailboat bobs on waves + 2 fish swim back and forth
  - Solnce: rotating sun rays + pulsing core
  - Noč: crescent moon with glow pulse + 18 twinkling stars + occasional comet
  - Mleko: minimal (no decoration)
  - Dež: 40 animated raindrops + cloud shapes
  - Sneg: 30 drifting snowflakes + snowman with subtle sway
  - Meglja: 4 drifting fog clouds
  (Implemented in: ambient SVG blocks + CSS keyframe animations)
- [x] **Emotion system with 23 named emotions** (v10 expansion) — each maps to a specific (eyes, eyebrows, mouth) combination:
  - neutral, happy, joyful, content, smug, thinking, confused, surprised, shocked, sad, crying, worried, grumpy, angry, speaking, listening, sleeping, dizzy, kiss, grateful, proud, sleepy, embarrassed
  - (Implemented in: `EMOTIONS` constant table, CSS visibility rules for `.emotion-<name>`, `setEmotion()`)
- [x] **15 eye shape variants** (v10 expansion) — normal, big, small, closed, half, slanted-up, slanted-down, wide, squint, x, star, swirl, wink, dizzy, + tear-drop overlay for sad/crying
- [x] **8 eyebrow shape variants** (v10 NEW) — neutral, raised, furrowed, sad, surprised, worried, smug, sleeping. Eyebrows are the key new feature for expressing emotion nuance.
- [x] **17 mouth shape variants** (v10 expansion) — neutral, smile, big-smile, grin, frown, sad-frown, o, big-o, smirk, flat, open-tongue, kiss, wavy, gasp, thinking, speaking, sleeping, grumpy
- [x] **Smart eye tracking** (v10 improved) — pupils smoothly follow mouse when within 350px of avatar; extrapolates look direction based on mouse velocity; 12% chance to focus-lock when near; 5% chance to glance when far; occasional random exploration every 4s. (Implemented in: `trackEyes()`, `lookAt()`, `focusEyesOn()`, `exploreEyes()`)
- [x] **Face direction switching** (v10 NEW) — every 5-9s Printko randomly faces forward, left, or right (3D Y-rotation) for more motion variety. Stops while sleeping/speaking. (Implemented in: `scheduleFaceDirection()`, `.facing-left`/`.facing-right` CSS, `.face-direction` SVG group)
- [x] **Sleep / wake cycle** — 45s idle → sleeps (closed eyes, Zzz, dim light). Click/msg → wakes grumpy for 4s (slanted eyes, frown, body shake, "Mmm… kar je?") → neutral. (Implemented in: `resetIdle()`, `goToSleep()`, `wakeUp()`, `IDLE_TIMEOUT`, `GRUMPY_DURATION`)
- [x] **Body animation** — float (4.5s), sway (6s), breathing (3.8s), shadow pulse. Arm waves removed in v10 (arms removed entirely). (Implemented in: `.body-group`, `.body-sway`, `.body-breath` CSS animations)
- [x] **Flat speech text below avatar in Caveat font** — Printko's replies use the Caveat handwriting font (same as "Pripravljen" status); lists/code blocks stay in Nunito/mono for readability. No comic-book speech cloud. (Implemented in: `.speech` CSS, `showSpeech()`)
- [x] **State descriptor in less intense color** — the avatar-status label ("Pripravljen", "Printko spi…", "Mmm… kar je?") uses `--tdim` color at 80% opacity instead of `--acc`, so it doesn't compete with the speech text. (Implemented in: `.avatar-status` CSS)
- [x] **Visible suggestion pills** (v10 fixed) — suggestions now have larger padding, 💬 emoji prefix, bolder font weight, and `:active` state for touch devices. Always visible. (Implemented in: `.suggestions`/`.sugg` CSS)
- [x] **Fully responsive for mobile** (v10 expanded) — breakpoints at 900px (tablet), 640px (mobile), 380px (very small phones), landscape mode (<500px height), and `prefers-reduced-motion`. Avatar, speech, suggestions, pills, chat bar, panels, and settings all reflow properly. (Implemented in: `@media` blocks at end of CSS)
- [x] **Slovenian default + English toggle** — every UI string, suggestion, status, and agent reply is bilingual. Persisted to `localStorage['es-lang']`. (Implemented in: `I18N`, `setLang()`, `toggleLang()`, `updateUIText()`)
- [x] **Quick-access pills** — 6 pills (Novice/Projekti/Zgodovina/Tiski/Črnilo/Finance) that open slide-in panels. (Implemented in: `renderQuickAccess()`, panel system)
- [x] **6 slide-in panels** — News, Projects, History, Ink, Finances, Prints. All render from mock `DB`. (Implemented in: `renderNews()`, `renderProjects()`, etc.)
- [x] **Offline agent with context-aware replies** — keyword-based reply generator (SL + EN) for ink, revenue, maintenance, projects, forecasts, comparisons, stock, and `/help` `/sync` commands. (Implemented in: `generateReply()`)
- [x] **Settings modal** — theme grid, language tabs, OpenRouter API key + model, Notion token + DB ID, sync button, about rows. Persisted to `localStorage`. (Implemented in: `openSettings()`, `saveKeys()`, `loadKeysUI()`)
- [x] **Toasts** — top-right toast stack for feedback. (Implemented in: `toast()`)
- [x] **Deprecated files archived** — v6, v7, v8, v9 files moved to `/archive/` with deprecation headers and a README explaining they should not be used. (Implemented in: `archive/` folder)

## 4. In-Progress / Next Features

- [ ] **Real OpenRouter API call** — `callLiveAgent()` is not yet implemented. The settings UI accepts the key and model, but `sendMsg()` always uses the offline `generateReply()`. Need to add the fetch to `https://openrouter.ai/api/v1/chat/completions` with the saved key, stream the response, and fall back to offline on error. (Status: Queued)
- [ ] **Real Notion sync** — `syncNotion()` only toasts. Need to actually query the user's Notion databases via the Notion API and replace the mock `DB` contents. Will require a proxy because Notion API doesn't support browser CORS for direct calls. (Status: Queued)
- [ ] **Auto weather detection** — optional geolocation + weather API (e.g. Open-Meteo) to auto-pick a theme matching local conditions. (Status: Queued)
- [ ] **Printko voice (TTS)** — optional text-to-speech when speaking, using the Web Speech API. (Status: Queued)
- [ ] **Project detail view** — clicking a project card currently just toasts. Should open a detail drawer. (Status: Queued)
- [ ] **Print detail view** — same, for print jobs. (Status: Queued)
- [ ] **Add/edit forms for all entity types** — the slide-in panels are read-only. Need a modal form builder. (Status: Queued)
- [ ] **Chat history persistence** — currently `chatHistory` is in-memory only. Should persist to `localStorage`. (Status: Queued)
- [ ] **Emotion-reactive agent replies** — when the agent detects a sad/negative query, Printko could show `worried` or `sad` emotion during the reply. When detecting gratitude, show `grateful`. (Status: Queued)
- [ ] **Avatar customization** — let user pick alternative avatar shapes/colors (e.g. different antenna style, body pattern). (Status: Queued)

## 5. Architectural Decisions & Constraints

- **Single self-contained `index.html`** — no build step, no dependencies, no server. Do NOT split into multiple files or add a bundler without explicit permission.
- **CSS-variable-driven theming** — every theme defines the same set of CSS custom properties on `[data-theme="name"]`. Switching themes is just `document.documentElement.dataset.theme = name`. Do NOT introduce runtime CSS-in-JS.
- **Emotion system = CSS class swap + EMOTIONS table** — the SVG contains all eye/eyebrow/mouth variants; CSS visibility rules show only the trio matching the `emotion-<name>` class on `.avatar`. The `EMOTIONS` JS table documents which (eyes, eyebrows, mouth) each emotion uses, but the actual rendering is pure CSS. Do NOT regenerate the SVG or use JS to toggle individual element `display`.
- **23-emotion vocabulary is the canonical set** — adding a new emotion requires: (1) adding it to the `EMOTIONS` table, (2) adding a CSS visibility rule `.avatar.emotion-<name> .piko .eyes-<X>, .eyebrows-<Y>, .mouth-<Z>{display:block}`. Do NOT add emotions that reuse the same combo as an existing one — pick a distinct combo.
- **Slovenian (SL) is the default language.** English is a toggle. All new strings MUST be added to both `I18N.sl` and `I18N.en`.
- **Light relaxed colors and minimalism** — the user explicitly praised this aesthetic. Avoid dark themes as defaults (Noč is the only dark theme, deliberate "night" exception). Avoid cluttered UI.
- **Weather themes cycle via the topbar sun button** — the user wants manual control via that button. Do NOT remove the cycle button even if auto-weather detection is added later.
- **No comic-book speech cloud** — the user explicitly requested plain text below the avatar. Do NOT re-add a bordered bubble or triangle pointer.
- **Speech text uses Caveat font** (handwriting style, same as "Pripravljen") — this is Printko's voice. Lists and code blocks inside speech use Nunito/mono for readability. Do NOT change the speech font.
- **State descriptor (avatar-status) uses `--tdim` color at 80% opacity** — deliberately less intense than the speech text. Do NOT change to `--acc` or full opacity.
- **Avatar has NO hands/arms** (removed in v10) — do NOT re-add them without explicit permission.
- **Mock data in `DB` constant** — all panels render from the in-memory `DB`. When real Notion sync is implemented, it should populate/replace `DB` properties, not bypass the rendering functions.
- **`localStorage` keys prefixed `es-`** — `es-theme`, `es-lang`, `es-or-key`, `es-notion-key`, `es-notion-db`, `es-model`. Do NOT rename these.
- **Idle/sleep timing constants** — `IDLE_TIMEOUT=45000` (45s), `GRUMPY_DURATION=4000` (4s), `EYE_EXPLORE_INTERVAL=4000` (4s), `EYE_FOCUS_DURATION=2500` (2.5s). Do NOT make sleep trigger faster than 30s or the grumpy phase longer than 6s without user testing.
- **Eye tracking proximity threshold = 350px** — pupils smoothly track when mouse is within 350px of avatar center; outside that, only occasional glances. Do NOT make it always-track (too intense) or never-track-far (too dead).
- **Deprecated files in `/archive/`** — per RULE 6, never delete old versions. The archive folder has a README explaining they should not be used. Do NOT remove files from `/archive/` unless explicitly instructed.

## 6. Known Bugs / Technical Debt

- **Vision-model false negatives on emotions** — when screenshots are verified by vision models, subtle emotions (content, sleepy, embarrassed) may be reported as "neutral" because the differences are slight. DOM-level tests confirm the correct eye/eyebrow/mouth variants ARE displayed. Verify via `getComputedStyle(.eyes-<name>).display === 'block'` rather than screenshots.
- **Theme decoration visibility is intentionally subtle** — vine leaves, fog clouds, etc. use low opacity (0.18–0.35) to maintain the minimalist aesthetic. If reported as "missing," bump opacity rather than adding more elements.
- **`chatHistory` is in-memory only** — conversations lost on reload. See In-Progress #7.
- **No real API calls yet** — OpenRouter and Notion integrations are UI-only stubs. See In-Progress #1 and #2.
- **`generateReply()` ink breakdown is simplified** — returns a flat 0.4ml per channel per print instead of summing actual `p.ink` objects (which don't exist in the current mock `DB.prints` schema). Fix when real ink-tracking is added.
- **Face direction 3D rotation may not render on very old browsers** — uses `rotateY()` which requires CSS 3D transforms. Falls back gracefully (just no rotation). Acceptable.
- **Mobile landscape mode is tight** — at <500px height, the avatar shrinks to 110px and speech is smaller. Functional but cozy. Could add a "compact mode" toggle if reported as issue.
- **Snowman arms are static** — the snowman sways but its stick arms don't wave. Minor — could add later.
