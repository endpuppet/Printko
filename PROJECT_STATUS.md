# PROJECT STATUS TRACKER

**CRITICAL INSTRUCTION FOR AI:** This is the single source of truth for this project. You MUST read this file before starting any work. You MUST update this file immediately after completing any task.

## 1. Project Overview

**Printko** is a custom assistant web app for the eufy E1 UV printer, centered around an animated character avatar named Printko. The app tracks prints, finances, projects, and inventory, and lets the user chat with the assistant. It is designed to connect to a Notion database for live data sync. The UI is Slovenian-first (with English toggle), uses light relaxed colors and minimalism, and ships with multiple weather-themed animated backgrounds. The avatar has character — it explores the room with its eyes, occasionally focus-locks on the mouse or taps, sleeps when idle for a long time, and wakes up grumpy for a few seconds before returning to neutral.

Current phase: **v9.0 — character avatar + weather themes shipped.** Notion sync is stubbed (UI wired, sample data shown, real API call not yet implemented). OpenRouter agent is offline-only with a context-aware local fallback.

## 2. File Map (The Architecture)

- `/index.html` — the entire app as a single self-contained HTML file (HTML + CSS + JS inline). This is the only source file. ~2350 lines.
  - **CSS section** (lines ~10–960): 9 illustrated themes (Trta/Jelka/Morje/Solnce/Noč/Mleko/Dež/Sneg/Meglja), avatar emotion system, weather animation keyframes, panel/modal styles, responsive breakpoints.
  - **HTML body** (lines ~965–1290): ambient background SVG decorations, weather-fx particle layer, topbar, main stage (avatar + speech + suggestions + chat bar + quick-access pills), 6 slide-in panels (news/projects/history/ink/finances/prints), settings modal.
  - **JS section** (lines ~1295–2330): I18N strings (SL/EN), theme metadata, mock DB, state, utils, toast, theme management, language toggle, avatar functions (emotion, blink, eye exploration, sleep/wake), weather-fx particle generation, speech reveal, suggestions, quick access, panel rendering, chat with keyword-based offline agent, settings persistence, boot/init.
- `/AI_CODING_RULES.md` — the 6 rules every AI contributor must follow (read-before-write, surgical edits only, maintain this file, modularize, ask for clarification, never delete without asking).
- `/PROJECT_STATUS.md` — this file.

## 3. Completed Features

- [x] **Centered avatar "Printko"** — friendly robot-printer SVG with antenna, face screen, body, side arms, status light. (Implemented in: `index.html` avatar SVG block ~lines 1086–1245)
- [x] **9 illustrated weather themes** — Trta (vine), Jelka (pine, user's favorite), Morje (sea), Solnce (sun), Noč (night), Mleko (milk), Dež (rain), Sneg (snow), Meglja (fog). Each has distinct light relaxed colors and ambient decorations. Cycled via topbar sun button or picked in Settings. (Implemented in: `index.html` CSS theme blocks lines ~33–183, ambient SVG lines ~951–1060, `THEMES`/`THEME_META` constants, `setTheme()`/`cycleTheme()`/`renderThemeGrid()`)
- [x] **Animated weather backgrounds** — each theme has subtle motion: vine leaves sway, pine trees sway, sea waves translate horizontally + bubbles rise, sun rays rotate + core pulses, stars twinkle + occasional comet flies, raindrops fall, snowflakes drift + rotate, fog clouds drift across. (Implemented in: `index.html` CSS keyframes lines ~239–303, `renderWeatherFx()` particle generator for rain/snow)
- [x] **Multiple emotion mouth + eye sets** — neutral, happy (^_^ eyes + big smile), sad (small eyes + tear drop + frown), surprised (wide eyes + O mouth), thinking (offset mouth line), speaking (animated mouth ellipse), sleeping (closed arc eyes + flat mouth + Zzz), grumpy (slanted eyes + curved-down mouth). CSS-driven visibility via `emotion-*` class on avatar. (Implemented in: `index.html` SVG eye/mouth variants lines ~916–999, CSS visibility rules lines ~431–457, `setEmotion()`)
- [x] **Eye exploration + focus lock** — pupils look around the room every 4s; 15% of mouse moves trigger a 2.5s focus lock on the cursor; touch taps trigger a longer focus lock. (Implemented in: `lookAt()`, `exploreEyes()`, `scheduleEyeExplore()`, `focusEyesOn()`, `trackEyes()`, `avatarTapFocus()`)
- [x] **Sleep / wake cycle** — after 45s of no user activity, Printko falls asleep (closed eyes, Zzz floating up, slow antenna pulse, status light dimmed). Clicking the avatar or sending a message wakes him up grumpy for 4s (slanted eyes, frown, body shake animation, "Mmm… kar je?" status) before returning to neutral. (Implemented in: `resetIdle()`, `goToSleep()`, `wakeUp()`, `IDLE_TIMEOUT=45000`, `GRUMPY_DURATION=4000`, idle listeners in `boot()`)
- [x] **Body animation** — gentle float (4.5s), body sway (6s left/right rocking), breathing (3.8s subtle scale pulse), arm waves (5s, faster when happy, stopped when sleeping), shadow pulse synced to float. (Implemented in: `.body-group`, `.body-sway`, `.body-breath`, `.arm-l`, `.arm-r` CSS animations)
- [x] **Flat speech text below avatar (no comic cloud)** — removed the bordered speech bubble with triangle pointer; speech now renders as plain centered text directly below the avatar. (Implemented in: `.speech` CSS lines ~470–500, HTML structure in stage)
- [x] **Slovenian default + English toggle** — every UI string, suggestion, status, and agent reply is bilingual. SL is default. Toggle via topbar SL/EN button or Settings language tabs. Persisted to `localStorage['es-lang']`. (Implemented in: `I18N` object, `setLang()`, `toggleLang()`, `updateUIText()`)
- [x] **Quick-access pills** — 6 pills (Novice/Projekti/Zgodovina/Tiski/Črnilo/Finance) that open slide-in panels from the right. Ink pill shows live white-ink % badge. (Implemented in: `renderQuickAccess()`, `openPanel()`/`closePanel()`/`closeAllPanels()`, panel HTML lines ~1295–1340)
- [x] **6 slide-in panels** — News (with category tabs), Projects (progress bars + status tags), History (unified timeline with type filters), Ink (6-channel levels + refills), Finances (profit summary + transactions), Prints (job list with quality stars). All render from the mock `DB`. (Implemented in: `renderNews()`, `renderProjects()`, `renderHistory()`, `renderInk()`, `renderFinances()`, `renderPrints()`)
- [x] **Offline agent with context-aware replies** — keyword-based reply generator (SL + EN keywords) for ink usage, revenue, maintenance, projects, forecasts, comparisons, stock levels, and `/help` `/sync` slash commands. Falls back gracefully. (Implemented in: `generateReply()`)
- [x] **Settings modal** — theme grid with live previews, language tabs, OpenRouter API key + model selector, Notion token + database ID fields, sync button, about rows. All persisted to `localStorage`. (Implemented in: `openSettings()`, `saveKeys()`, `loadKeysUI()`, `syncNotion()`)
- [x] **Toasts** — top-right toast stack for success/info/warn/error feedback. (Implemented in: `toast()`)
- [x] **Mobile responsive** — avatar scales down, layout reflows at <640px. (Implemented in: `@media(max-width:640px)` block)

## 4. In-Progress / Next Features

- [ ] **Real OpenRouter API call** — `callLiveAgent()` is not yet implemented. The settings UI accepts the key and model, but `sendMsg()` always uses the offline `generateReply()`. Need to add the fetch to `https://openrouter.ai/api/v1/chat/completions` with the saved key, stream the response, and fall back to offline on error. (Status: Queued)
- [ ] **Real Notion sync** — `syncNotion()` only toasts. Need to actually query the user's Notion databases (projects, prints, news) via the Notion API and replace the mock `DB` contents. Will require a proxy because Notion API doesn't support browser CORS for direct calls. (Status: Queued)
- [ ] **Auto weather detection** — the user mentioned backgrounds should be "based on possible weather outside." Currently the user cycles themes manually. Future: optional geolocation + weather API (e.g. Open-Meteo) to auto-pick a theme matching local conditions. (Status: Queued)
- [ ] **More iconic background elements per scene** — user specifically praised Jelka (pine trees) as the sweetest. Need to add more iconic elements to other themes (e.g. birds for Jelka, fish for Morje, moon for Noč, snowman for Sneg). (Status: Queued)
- [ ] **Printko voice (TTS)** — optional text-to-speech when speaking, using the Web Speech API. (Status: Queued)
- [ ] **Project detail view** — clicking a project card currently just toasts. Should open a detail drawer with milestones, timeline, and edit form. (Status: Queued)
- [ ] **Print detail view** — same, for print jobs. (Status: Queued)
- [ ] **Add/edit forms for all entity types** — the slide-in panels are read-only. Need a modal form builder (similar to v7's `schemas` + `openAddModal`) to add/edit prints, projects, materials, etc. (Status: Queued)
- [ ] **Chat history persistence** — currently `chatHistory` is in-memory only. Should persist to `localStorage` so conversations survive reload. (Status: Queued)
- [ ] **More avatar emotions** — excited (for successful prints), confused (for unrecognized queries), proud (for completed projects). (Status: Queued)

## 5. Architectural Decisions & Constraints

- **Single self-contained `index.html`** — no build step, no dependencies, no server. The user can open the file directly in any browser. Do NOT split into multiple files or add a bundler without explicit permission.
- **CSS-variable-driven theming** — every theme defines the same set of CSS custom properties (`--bg-1`, `--card`, `--acc`, `--avatar-l`, etc.) on `[data-theme="name"]`. Switching themes is just `document.documentElement.dataset.theme = name`. Do NOT introduce a runtime CSS-in-JS system.
- **Avatar emotions via CSS class swap** — the SVG contains all eye/mouth variants; CSS visibility rules show only the pair matching the `emotion-*` class on `.avatar`. JS only swaps the class. Do NOT regenerate the SVG or use JS to toggle individual element `display` — the CSS approach is cleaner and animatable.
- **Slovenian (SL) is the default language.** English is a toggle. All new strings MUST be added to both `I18N.sl` and `I18N.en`. Do NOT add English-only strings.
- **Light relaxed colors and minimalism** — the user explicitly praised this aesthetic. Avoid dark themes as defaults (Noč is the only dark theme, and it's a deliberate "night" exception). Avoid cluttered UI.
- **Weather themes cycle via the topbar sun button** — the user wants manual control via that button. Do NOT remove the cycle button even if auto-weather detection is added later.
- **No comic-book speech cloud** — the user explicitly requested plain text below the avatar. Do NOT re-add a bordered bubble or triangle pointer.
- **Mock data in `DB` constant** — all panels render from the in-memory `DB` object. When real Notion sync is implemented, it should populate/replace `DB` properties, not bypass the rendering functions.
- **`localStorage` keys prefixed `es-`** — `es-theme`, `es-lang`, `es-or-key`, `es-notion-key`, `es-notion-db`, `es-model`. Do NOT rename these (would lose user settings on upgrade).
- **Idle/sleep timing constants** — `IDLE_TIMEOUT=45000` (45s), `GRUMPY_DURATION=4000` (4s), `EYE_EXPLORE_INTERVAL=4000` (4s), `EYE_FOCUS_DURATION=2500` (2.5s). These are tuned for a calm, non-intrusive feel. Do NOT make sleep trigger faster than 30s or the grumpy phase longer than 6s without user testing.
- **RULE 6 (never delete without asking)** — the previous versions (`eufy-dashboard-v7.html`, `eufy-studio.html`) are preserved in `/home/z/my-project/download/` as historical reference. Do NOT delete them.

## 6. Known Bugs / Technical Debt

- **Vision-model false negatives on emotions** — when screenshots are verified by vision models, `surprised` and `sad` emotions are sometimes reported as "neutral" because the differences (slightly bigger eyes for surprised, smaller pupils + tear drop for sad) are subtle. DOM-level tests confirm the correct eye/mouth variants ARE displayed. No actual bug — just verify via `getComputedStyle(.eyes-surprised).display === 'block'` rather than screenshots.
- **Theme decoration visibility is intentionally subtle** — vine leaves, fog clouds, etc. use low opacity (0.18–0.35) to maintain the minimalist aesthetic. If the user reports them as "missing," bump opacity rather than adding more elements.
- **`chatHistory` is in-memory only** — conversations are lost on reload. See In-Progress #7.
- **No real API calls yet** — OpenRouter and Notion integrations are UI-only stubs. See In-Progress #1 and #2.
- **`generateReply()` ink breakdown is simplified** — it returns a flat 0.4ml per channel per print instead of summing the actual `p.ink` objects (which don't exist in the current mock `DB.prints` schema). When real ink-tracking is added, this needs fixing.
- **No keyboard shortcut for send** — Enter sends (good), but there's no Shift+Enter for newline hint shown in the UI.
- **Mobile panel width** — slide-in panels are `min(440px, 90vw)`. On very small phones (<400px) the inner content can feel cramped. Consider `min(440px, 95vw)` for <400px screens.
