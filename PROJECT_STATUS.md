# PROJECT STATUS TRACKER

**CRITICAL INSTRUCTION FOR AI:** This is the single source of truth for this project. You MUST read this file before starting any work. You MUST update this file immediately after completing any task.

## 1. Project Overview

**Printko** is a custom assistant web app for the eufy E1 UV printer, centered around an animated character avatar named Printko. The app tracks prints, finances, projects, and inventory, and lets the user chat with the assistant. It is designed to connect to a Notion database for live data sync. The UI is Slovenian-first (with English toggle), uses light relaxed colors and minimalism, and ships with 9 weather-themed animated backgrounds — each with iconic animated elements (butterflies, birds, fish, sailboats, moon, snowman, etc.). The avatar has deep character — eyebrows, eyes, and mouths combine into 31 named emotions including a sassy/annoyed attitude family, the eyes smoothly track the mouse when near and look in the direction of motion, he occasionally turns to face left/right, sleeps when idle for 45s, and wakes up **annoyed** with attitude for 4s. Suggestion pills pop out around his head; tapping them makes him react before answering (no blue highlight).

Current phase: **v11.0 — attitude emotions + pop-out pills + annoyed wake-up shipped.** Notion sync is stubbed (UI wired, sample data shown, real API call not yet implemented). OpenRouter agent is offline-only with a context-aware local fallback.

## 2. File Map (The Architecture)

```
Printko/
├── index.html           # the entire app (HTML + CSS + JS inline, ~2930 lines) — CURRENT VERSION
├── AI_CODING_RULES.md   # 6 rules every AI contributor must follow
├── PROJECT_STATUS.md    # this file — single source of truth
├── README.md            # repo readme
├── .gitignore
└── archive/             # DEPRECATED older versions — DO NOT USE
    ├── README.md         # explains what's deprecated and why
    ├── eufy-dashboard-v6-1.html   # v6 (original upload, pre-redesign)
    ├── eufy-dashboard-v7.html     # v7 (3-column focus mode)
    ├── eufy-studio.html           # v8 (centered Piko avatar, before rename)
    ├── printko-v9.html            # v9 (Printko with weather themes, before emotion system)
    └── printko-v10.html           # v10 (emotion system, before attitude/pop-out pills) — added v11
```

### Inside `index.html`
- **CSS section** (~lines 10–1240): 9 illustrated themes, eyebrow/eye/mouth emotion visibility rules for 31 emotions, weather animation keyframes, extra ambient animations (butterfly, birds, fish, sailboat, moon, snowman), **pop-out suggestion pill positioning** (absolute, around avatar head), panel/modal styles, full responsive breakpoints (tablet/mobile/very-small/landscape/reduced-motion).
- **HTML body** (~lines 1240–1830): ambient background SVG decorations (with iconic elements per theme), weather-fx particle layer, topbar, main stage (avatar + pop-out pills + speech + chat bar + quick-access pills), 6 slide-in panels, settings modal.
- **JS section** (~lines 1830–2930): I18N strings (SL/EN), theme metadata, mock DB, state, utils, toast, theme management, language toggle, **EMOTIONS system table (31 emotions → eye+eyebrow+mouth combos, organized by family)**, avatar functions (emotion, blink, eye tracking with velocity, **annoyed wake-up with attitude lines**, face direction), weather-fx particle generation, speech reveal, **pop-out pill rendering + tap reactions**, quick access, panel rendering, chat with keyword-based offline agent, settings persistence, boot/init.

## 3. Completed Features

- [x] **Centered avatar "Printko"** — friendly robot-printer SVG with antenna, face screen, body, status light. Hands/arms removed in v10. (Implemented in: avatar SVG block)
- [x] **9 illustrated weather themes** — Trta, Jelka, Morje, Solnce, Noč, Mleko, Dež, Sneg, Meglja. Cycled via topbar sun button. (Implemented in: CSS theme blocks, ambient SVG, `THEMES`/`THEME_META`, `setTheme()`/`cycleTheme()`)
- [x] **Animated iconic background elements per theme**: Trta (butterfly), Jelka (3 birds), Morje (sailboat + 2 fish), Solnce (rotating sun), Noč (crescent moon + 18 twinkling stars + comet), Mleko (minimal), Dež (40 raindrops + clouds), Sneg (30 snowflakes + snowman), Meglja (4 drifting fog clouds). (Implemented in: ambient SVG + CSS keyframe animations)
- [x] **Emotion system with 31 named emotions** (v11 expanded from 23): organized into families:
  - **Calm/neutral**: neutral, content, sleepy
  - **Happy**: happy, joyful, grateful, proud, kiss
  - **Thinking/confused**: thinking, confused, dizzy
  - **Surprised**: surprised, shocked
  - **Sad**: sad, crying, worried, embarrassed
  - **Angry/annoyed (v11 attitude)**: grumpy, angry, **annoyed, exasperated, unimpressed, fedup**
  - **Sassy/attitude (v11 NEW)**: smug, **sarcastic, mischievous, dramatic, sideeye**
  - **System**: speaking, listening, sleeping
  - (Implemented in: `EMOTIONS` constant table, CSS visibility rules, `setEmotion()`)
- [x] **15 eye shape variants** + animated tear drop for sad/crying
- [x] **8 eyebrow shape variants** (neutral, raised, furrowed, sad, surprised, worried, smug, sleeping)
- [x] **17 mouth shape variants**
- [x] **Smart eye tracking** — pupils smoothly follow mouse when within 350px; extrapolates look direction from mouse velocity; 12% focus-lock chance when near; 5% glance chance when far. (Implemented in: `trackEyes()`, `lookAt()`, `focusEyesOn()`, `exploreEyes()`)
- [x] **Face direction switching** — every 5-9s Printko randomly faces forward/left/right (3D Y-rotation). Stops while sleeping/speaking. (Implemented in: `scheduleFaceDirection()`, `.facing-left`/`.facing-right` CSS)
- [x] **Sleep / wake cycle with ANNOYED wake-up (v11 changed)** — 45s idle → sleeps. Click/msg → wakes **annoyed** (not grumpy anymore) with attitude lines like "Uf. Kaj pa tokrat?" / "Ugh. What is it this time?" for 4s, then neutral. (Implemented in: `wakeUp()`, `IDLE_TIMEOUT`, `GRUMPY_DURATION`)
- [x] **Printko has ATTITUDE (v11 NEW)** — clicking the avatar no longer always gives happy greetings. Random mix of reactions: ~30% helpful (happy/content/grateful), ~25% sassy (smug/mischievous/proud), ~25% attitude (annoyed/sarcastic/unimpressed/sideeye), ~10% dramatic (dramatic/surprised), ~10% sleepy (sleepy/content). Each reaction has a matching speech line in SL and EN. (Implemented in: `avatarClick()`)
- [x] **Pop-out suggestion pills around Printko's head (v11 NEW)** — the 6 quick-question pills are no longer in a row below. They pop out around his head (top-left, top-right, mid-left, mid-right, bottom-left, bottom-right) using absolute positioning within `.avatar-wrap`. Each pill has a staggered pop-in animation + subtle idle floating. (Implemented in: `.suggestions-pop` CSS, `renderSuggestions()`, responsive overrides)
- [x] **Pill tap = Printko reaction (v11 NEW)** — tapping a pill no longer just sends the message. Printko reacts with a brief attitude quip ("Hm. Zanimivo vprašanje." / "Hm. Interesting question.") and an emotion (listening/thinking/mischievous/smug/content), then processes the actual answer after 650ms. (Implemented in: `askSuggestion()`)
- [x] **No blue tap highlight (v11 NEW)** — all suggestion pills have `-webkit-tap-highlight-color:transparent` and `outline:none`. The tap feedback is a custom `.tapped` animation: scale down → flash accent background → scale back. No browser-default blue. (Implemented in: `.sugg` CSS, `.sugg.tapped` animation, `ontouchstart` handler with `preventDefault()`)
- [x] **Body animation** — float, sway, breathing, shadow pulse. (Implemented in: `.body-group`, `.body-sway`, `.body-breath` CSS)
- [x] **Flat speech text below avatar in Caveat font** — Printko's replies use the Caveat handwriting font. (Implemented in: `.speech` CSS, `showSpeech()`)
- [x] **State descriptor in less intense color** — avatar-status uses `--tdim` at 80% opacity. (Implemented in: `.avatar-status` CSS)
- [x] **Fully responsive** — breakpoints at 900px (tablet), 640px (mobile), 380px (very small), landscape <500px, `prefers-reduced-motion`. Pop-out pills reposition closer to avatar on smaller screens. (Implemented in: `@media` blocks)
- [x] **Slovenian default + English toggle** — all UI strings, suggestions, status, agent replies, attitude lines, and wake-up quips are bilingual. (Implemented in: `I18N`, `setLang()`, `toggleLang()`)
- [x] **Quick-access pills** — 6 pills (Novice/Projekti/Zgodovina/Tiski/Črnilo/Finance) that open slide-in panels. (Implemented in: `renderQuickAccess()`, panel system)
- [x] **6 slide-in panels** — News, Projects, History, Ink, Finances, Prints. (Implemented in: panel render functions)
- [x] **Offline agent with context-aware replies** — keyword-based reply generator (SL + EN). (Implemented in: `generateReply()`)
- [x] **Settings modal** — theme grid, language tabs, OpenRouter API key + model, Notion token + DB ID, sync button, about rows. (Implemented in: `openSettings()`, `saveKeys()`, `loadKeysUI()`)
- [x] **Deprecated files archived** — v6, v7, v8, v9 files in `/archive/` with deprecation headers + README. (Implemented in: `archive/` folder)

## 4. In-Progress / Next Features

- [ ] **Real OpenRouter API call** — `callLiveAgent()` not yet implemented. Settings UI accepts key/model, but `sendMsg()` always uses offline `generateReply()`. Need fetch to `https://openrouter.ai/api/v1/chat/completions`, stream response, fall back to offline on error. (Status: Queued)
- [ ] **Real Notion sync** — `syncNotion()` only toasts. Need real Notion API queries + CORS proxy. (Status: Queued)
- [ ] **Auto weather detection** — geolocation + weather API to auto-pick theme. (Status: Queued)
- [ ] **Printko voice (TTS)** — Web Speech API. (Status: Queued)
- [ ] **Project / print detail views** — clicking a card currently just toasts. Should open detail drawer. (Status: Queued)
- [ ] **Add/edit forms for all entity types** — panels are read-only. (Status: Queued)
- [ ] **Chat history persistence** — `chatHistory` is in-memory only. Should persist to `localStorage`. (Status: Queued)
- [ ] **Emotion-reactive agent replies** — agent detects sad/negative query → Printko shows worried/sad during reply; detects gratitude → grateful. (Status: Queued)
- [ ] **More attitude lines** — expand the avatarClick reactions and wake-up lines with more variety. Currently 14 click reactions + 6 wake-up lines per language. Could grow to 30+ each. (Status: Queued)
- [ ] **Avatar customization** — alternative antenna styles, body patterns. (Status: Queued)

## 5. Architectural Decisions & Constraints

- **Single self-contained `index.html`** — no build step, no dependencies, no server. Do NOT split into multiple files or add a bundler.
- **CSS-variable-driven theming** — every theme defines the same CSS custom properties on `[data-theme="name"]`. Do NOT introduce runtime CSS-in-JS.
- **Emotion system = CSS class swap + EMOTIONS table** — SVG contains all eye/eyebrow/mouth variants; CSS visibility rules show only the trio matching `emotion-<name>`. The `EMOTIONS` JS table documents which (eyes, eyebrows, mouth) each emotion uses. Do NOT regenerate the SVG or use JS to toggle individual `display`.
- **31-emotion vocabulary is the canonical set (v11)** — organized into 8 families. Adding a new emotion requires: (1) adding to `EMOTIONS` table, (2) adding a CSS visibility rule `.avatar.emotion-<name> .piko .eyes-<X>, .eyebrows-<Y>, .mouth-<Z>{display:block}`. Do NOT add emotions that reuse the same combo as an existing one.
- **Wake-up reaction is "annoyed" (v11 changed from "grumpy")** — the user explicitly requested this change. The `wakeUp()` function sets `emotion:'annoyed'` and shows a random attitude line. The `GRUMPY_DURATION` constant is kept (per RULE 6) but now governs the annoyed phase duration. Do NOT change back to grumpy without explicit permission.
- **Avatar click = attitude mix (v11)** — `avatarClick()` picks from 14 reactions across helpful/sassy/attitude/dramatic/sleepy families. Do NOT revert to always-happy greetings — the attitude is the new personality.
- **Pop-out pills are absolutely positioned around the avatar head (v11)** — pills live inside `.avatar-wrap` (which is `position:relative` with `max-width:520px`). Each pill has `:nth-child(n)` positioning. Do NOT revert to the old row-below layout.
- **No blue tap highlight (v11)** — all interactive pills/buttons have `-webkit-tap-highlight-color:transparent`. Tap feedback is custom CSS animation only. Do NOT remove this.
- **Pill tap triggers Printko reaction before answering (v11)** — `askSuggestion()` sets a brief emotion + quip, then calls `sendMsg()` after 650ms. Do NOT skip the reaction.
- **Slovenian (SL) is the default language.** All new strings MUST be added to both `I18N.sl` and `I18N.en`. This includes all attitude lines, wake-up quips, and pill-tap reactions.
- **Light relaxed colors and minimalism** — avoid dark themes as defaults (Noč is the deliberate exception). Avoid cluttered UI.
- **Weather themes cycle via the topbar sun button** — do NOT remove the cycle button even if auto-weather is added.
- **No comic-book speech cloud** — plain text below avatar. Do NOT re-add a bordered bubble.
- **Speech text uses Caveat font** — Printko's voice. Lists/code inside speech use Nunito/mono.
- **State descriptor (avatar-status) uses `--tdim` at 80% opacity** — deliberately less intense.
- **Avatar has NO hands/arms** (removed v10) — do NOT re-add.
- **Mock data in `DB` constant** — all panels render from `DB`. Real Notion sync should populate/replace `DB`.
- **`localStorage` keys prefixed `es-`** — `es-theme`, `es-lang`, `es-or-key`, `es-notion-key`, `es-notion-db`, `es-model`. Do NOT rename.
- **Idle/sleep timing constants** — `IDLE_TIMEOUT=45000`, `GRUMPY_DURATION=4000`, `EYE_EXPLORE_INTERVAL=4000`, `EYE_FOCUS_DURATION=2500`. Do NOT change without user testing.
- **Eye tracking proximity threshold = 350px** — do NOT make it always-track or never-track-far.
- **Deprecated files in `/archive/`** — per RULE 6, never delete old versions. v10 was added to archive in v11.

## 6. Known Bugs / Technical Debt

- **Vision-model false negatives on emotions** — subtle emotions (content, sleepy, embarrassed, unimpressed, sideeye) may be reported as "neutral" by vision models. DOM-level tests confirm correct variants display. Verify via `getComputedStyle(.eyes-<name>).display === 'block'`.
- **Theme decoration visibility is intentionally subtle** — opacities 0.18–0.35 for minimalism. Bump opacity if reported "missing".
- **`chatHistory` is in-memory only** — lost on reload. See In-Progress #7.
- **No real API calls yet** — OpenRouter and Notion are UI-only stubs.
- **`generateReply()` ink breakdown is simplified** — returns flat 0.4ml per channel per print. Fix when real ink-tracking is added.
- **Face direction 3D rotation may not render on very old browsers** — falls back gracefully.
- **Pop-out pills can overlap on very narrow screens (<320px)** — they reposition tighter but may collide. Acceptable for now; could add a "collapse to row" fallback if reported.
- **Pill tap on touch devices fires both `ontouchstart` and `onclick`** — the `ontouchstart` handler calls `preventDefault()` to suppress the duplicate click. Verified working but worth watching.
- **The `GRUMPY_DURATION` constant name is now misleading** (v11) — it governs the annoyed wake-up phase, not grumpy. Kept the name per RULE 6 (never rename without asking). Could rename to `ANNOYED_DURATION` in a future version with explicit permission.
