# PROJECT STATUS TRACKER

**CRITICAL INSTRUCTION FOR AI:** This is the single source of truth for this project. You MUST read this file before starting any work. You MUST update this file immediately after completing any task.

## 1. Project Overview

**Printko** is a custom assistant web app for the eufy E1 UV printer, centered around an animated character avatar named Printko. The app tracks prints, finances, projects, and inventory, and lets the user chat with the assistant. It is designed to connect to a Notion database for live data sync. The UI is Slovenian-first (with English toggle), uses light relaxed colors and minimalism, and ships with 9 weather-themed animated backgrounds — each with iconic animated elements (butterflies, birds, fish, sailboats, moon, snowman, etc.). The avatar has deep character — eyebrows, eyes, and mouths combine into 31 named emotions including a sassy/annoyed attitude family, the eyes smoothly track the mouse when near and look in the direction of motion, he occasionally turns to face left/right (face stays centered), sleeps when idle for 45s, and wakes up **annoyed** with attitude for 4s. Suggestion pills pop out around his head; tapping them makes him react before answering (no blue highlight). Printko is **cozy, not official** — he calls the user by name (default: Lara), and his greetings/wake-ups/quips/reactions come from a configurable preset bank with 3 tones (cozy/formal/playful) in SL + EN, plus a "Generate 3× variations" button that expands the base presets using a local template combinator. The avatar shape is also configurable (round/square/hex/blob).

Current phase: **v12.0 — personality system + avatar shapes + tap-to-look fix + face centering fix shipped.** Notion sync is stubbed (UI wired, sample data shown, real API call not yet implemented). OpenRouter agent is offline-only with a context-aware local fallback.

## 2. File Map (The Architecture)

```
Printko/
├── index.html           # the entire app (HTML + CSS + JS inline, ~3420 lines) — CURRENT VERSION
├── AI_CODING_RULES.md   # 6 rules every AI contributor must follow
├── PROJECT_STATUS.md    # this file — single source of truth
├── README.md            # repo readme
├── .gitignore
└── archive/             # DEPRECATED older versions — DO NOT USE
    ├── README.md         # explains what's deprecated and why
    ├── eufy-dashboard-v6-1.html   # v6
    ├── eufy-dashboard-v7.html     # v7
    ├── eufy-studio.html           # v8
    ├── printko-v9.html            # v9
    ├── printko-v10.html           # v10
    └── printko-v11.html           # v11
```

### Inside `index.html`
- **CSS section** (~lines 10–1215): 9 illustrated themes, eyebrow/eye/mouth emotion visibility rules for 31 emotions, weather animation keyframes, extra ambient animations (butterfly, birds, fish, sailboat, moon, snowman), pop-out suggestion pill positioning, **avatar shape variants (round/square/hex/blob)**, **preset list styling**, panel/modal styles, full responsive breakpoints.
- **HTML body** (~lines 1215–1960): ambient background SVG decorations, weather-fx particle layer, topbar, main stage (avatar with `data-shape` + pop-out pills + speech + chat bar + quick-access pills), 6 slide-in panels, settings modal **with Printko personality subsection (name, tone, shape, presets, generate button)**.
- **JS section** (~lines 1960–3420): I18N strings (SL/EN), theme metadata, mock DB, state, utils, toast, theme management, language toggle, **PERSONALITY system (name, tone, shape, PRESETS bank, variation generator, save/load/show/add/delete presets)**, EMOTIONS table (31 emotions), avatar functions (emotion, blink, eye tracking with velocity, **annoyed wake-up using personality preset**, face direction — **face stays centered, no translateX**, **global click-to-look listener**), weather-fx, speech reveal, pop-out pill rendering + tap reactions, quick access, panel rendering, chat, settings persistence, boot/init.

## 3. Completed Features

- [x] **Centered avatar "Printko"** — friendly robot-printer SVG. Hands/arms removed in v10. **v12: shape is configurable** (round/square/hex/blob) via `data-shape` attribute on `#avatar` + CSS `rx`/`ry` overrides on the body rect. (Implemented in: avatar SVG, `.avatar[data-shape="..."]` CSS, `setShape()`, shape-card grid in settings)
- [x] **9 illustrated weather themes** with animated iconic elements per theme (butterfly, birds, fish, sailboat, moon, snowman, etc.). (Implemented in: CSS theme blocks, ambient SVG, `THEMES`/`THEME_META`)
- [x] **Emotion system with 31 named emotions** organized into 8 families (calm, happy, thinking, surprised, sad, angry/annoyed, sassy, system). (Implemented in: `EMOTIONS` table, CSS visibility rules, `setEmotion()`)
- [x] **15 eye shapes + 8 eyebrow shapes + 17 mouth shapes** — composed into 31 emotions.
- [x] **Smart eye tracking** — pupils smoothly follow mouse when within 350px; extrapolates look direction from mouse velocity; 12% focus-lock chance when near; 5% glance chance when far. (Implemented in: `trackEyes()`, `lookAt()`, `focusEyesOn()`, `exploreEyes()`)
- [x] **Global click-to-look (v12 NEW FIX)** — any click anywhere on the page (except on the avatar itself or interactive controls) now makes Printko look at that point for 2.5× the normal focus duration. This fixes the "can't get him to look in direction of tap" issue. (Implemented in: global `click` listener in `boot()`)
- [x] **Face direction switching with face centered (v12 FIXED)** — every 5-9s Printko randomly faces forward/left/right via 3D Y-rotation. **v12 fix: removed the `translateX` shifts** that were making the face drift off-center; now only the rotation happens, face stays centered. (Implemented in: `.facing-left`/`.facing-right` CSS — `rotateY(±15deg)` only, no translateX)
- [x] **Sleep / wake cycle with ANNOYED wake-up** — 45s idle → sleeps. Click/msg → wakes **annoyed** with attitude line for 4s, then neutral. **v12: wake-up line now comes from personality presets** (cozy/formal/playful + user's name). (Implemented in: `wakeUp()`, `pickPreset('wakeups')`)
- [x] **Printko has ATTITUDE on click** — `avatarClick()` picks from 13 emotion families. **v12: greeting line now comes from personality presets** instead of hardcoded lines. (Implemented in: `avatarClick()`, `pickPreset('greetings')`)
- [x] **PERSONALITY SYSTEM (v12 NEW)** — a new "Printko · Osebnost" subsection in Settings with:
  - **User name input** (default: "Lara") — Printko calls the user by name
  - **Tone selector** (cozy / formal / playful) — changes the entire preset bank
  - **Avatar shape selector** (round / square / hex / blob) — live-updates the avatar body shape
  - **Preset sentence bank** with 4 categories: greetings, wakeups, quips, reactions
  - **Add custom preset** — user can type their own sentences (with `{name}` placeholder)
  - **Delete preset** — custom and generated presets can be deleted
  - **"Generate 3× variations" button** — expands base presets using a local template combinator (prefix + suffix modifiers in SL + EN). Produces ~45 variations across all 4 categories.
  - **Stats display** — shows total/base/custom/generated counts per category
  - (Implemented in: `personality` state object, `PRESETS` constant, `VARIATION_MODIFIERS`, `fillPreset()`, `getActivePresets()`, `pickPreset()`, `generateVariations()`, `savePersonality()`, `loadPersonality()`, `setShape()`, `showPresets()`, `addPreset()`, `deletePreset()`)
- [x] **Cozy tone (v12 NEW)** — default tone is "cozy" (was hardcoded formal/official). Printko now says "Pozdravljena, Lara!" instead of "Pozdravljeni!". All greetings/wake-ups/quips/reactions use the cozy tone by default, with formal and playful as alternatives. (Implemented in: `PRESETS.sl.cozy`, `PRESETS.en.cozy`, default `personality.tone='cozy'`)
- [x] **User name in all speech (v12 NEW)** — `{name}` placeholder in all presets is filled at runtime with the user's name. Default "Lara". Stored in `localStorage['es-personality']`. (Implemented in: `fillPreset()`)
- [x] **Pop-out suggestion pills around Printko's head** — 6 pills positioned absolutely (top-left, top-right, mid-left, mid-right, bottom-left, bottom-right) with staggered pop-in + idle floating. Tapping triggers a brief attitude quip (**v12: from personality presets**) before answering. No blue tap highlight. (Implemented in: `.suggestions-pop` CSS, `askSuggestion()`, `pickPreset('quips')`)
- [x] **Body animation** — float, sway, breathing, shadow pulse. (Implemented in: `.body-group`, `.body-sway`, `.body-breath` CSS)
- [x] **Flat speech text below avatar in Caveat font** — Printko's replies use the Caveat handwriting font. (Implemented in: `.speech` CSS, `showSpeech()`)
- [x] **State descriptor in less intense color** — avatar-status uses `--tdim` at 80% opacity. (Implemented in: `.avatar-status` CSS)
- [x] **Fully responsive** — breakpoints at 900px (tablet), 640px (mobile), 380px (very small), landscape <500px, `prefers-reduced-motion`. (Implemented in: `@media` blocks)
- [x] **Slovenian default + English toggle** — all UI strings, suggestions, status, agent replies, attitude lines, wake-up quips, personality presets, and variation modifiers are bilingual. (Implemented in: `I18N`, `PRESETS.sl`/`PRESETS.en`, `VARIATION_MODIFIERS.sl`/`VARIATION_MODIFIERS.en`)
- [x] **Quick-access pills** — 6 pills (Novice/Projekti/Zgodovina/Tiski/Črnilo/Finance) that open slide-in panels. (Implemented in: `renderQuickAccess()`, panel system)
- [x] **6 slide-in panels** — News, Projects, History, Ink, Finances, Prints. (Implemented in: panel render functions)
- [x] **Offline agent with context-aware replies** — keyword-based reply generator (SL + EN). (Implemented in: `generateReply()`)
- [x] **Settings modal** — theme grid, language tabs, **Printko personality subsection (v12)**, OpenRouter API key + model, Notion token + DB ID, sync button, about rows. (Implemented in: `openSettings()`, `saveKeys()`, `savePersonality()`, `loadPersonality()`, `loadKeysUI()`)
- [x] **Deprecated files archived** — v6 through v11 files in `/archive/` with deprecation headers + README. (Implemented in: `archive/` folder)

## 4. In-Progress / Next Features

- [ ] **Real OpenRouter API call** — `callLiveAgent()` not yet implemented. Settings UI accepts key/model, but `sendMsg()` always uses offline `generateReply()`. (Status: Queued)
- [ ] **Real Notion sync** — `syncNotion()` only toasts. Need real Notion API queries + CORS proxy. (Status: Queued)
- [ ] **AI-powered variation generation (v12 stretch)** — currently the "Generate 3× variations" button uses a local template combinator. When OpenRouter key is set, it should call the LLM to produce higher-quality, semantically varied sentences based on the base presets. (Status: Queued)
- [ ] **Auto weather detection** — geolocation + weather API to auto-pick theme. (Status: Queued)
- [ ] **Printko voice (TTS)** — Web Speech API. (Status: Queued)
- [ ] **Project / print detail views** — clicking a card currently just toasts. Should open detail drawer. (Status: Queued)
- [ ] **Add/edit forms for all entity types** — panels are read-only. (Status: Queued)
- [ ] **Chat history persistence** — `chatHistory` is in-memory only. Should persist to `localStorage`. (Status: Queued)
- [ ] **Emotion-reactive agent replies** — agent detects sad/negative query → Printko shows worried/sad during reply. (Status: Queued)
- [ ] **More avatar shapes** — add more shape options (e.g., "star", "heart", "diamond"). Currently 4 (round/square/hex/blob). (Status: Queued)
- [ ] **Personality import/export** — let users export their preset bank as JSON and import on another device. (Status: Queued)

## 5. Architectural Decisions & Constraints

- **Single self-contained `index.html`** — no build step, no dependencies, no server. Do NOT split into multiple files or add a bundler.
- **CSS-variable-driven theming** — every theme defines the same CSS custom properties on `[data-theme="name"]`. Do NOT introduce runtime CSS-in-JS.
- **Emotion system = CSS class swap + EMOTIONS table** — SVG contains all eye/eyebrow/mouth variants; CSS visibility rules show only the trio matching `emotion-<name>`. Do NOT regenerate the SVG or use JS to toggle individual `display`.
- **31-emotion vocabulary is the canonical set** — organized into 8 families. Adding a new emotion requires: (1) adding to `EMOTIONS` table, (2) adding a CSS visibility rule. Do NOT add emotions that reuse the same combo as an existing one.
- **PERSONALITY system is the canonical way to configure Printko's voice (v12 NEW)** — `personality` state object holds `name`, `tone`, `shape`, `customPresets`, `generated`. All persisted to `localStorage['es-personality']`. Do NOT hardcode greetings/wake-ups/quips/reactions in `avatarClick()`, `wakeUp()`, or `askSuggestion()` — always use `pickPreset(category)`. The only hardcoded speech remaining is the welcome message and the offline agent's keyword-based replies (which are data-driven, not personality-driven).
- **PRESETS bank structure (v12 NEW)** — `PRESETS[lang][tone][category]` returns an array of template strings. Each template uses `{name}` placeholder. Categories are: `greetings`, `wakeups`, `quips`, `reactions`. Tones are: `cozy`, `formal`, `playful`. Do NOT add a 4th tone or 5th category without expanding all 3 languages × all tones × all categories.
- **Variation generator uses local template combinator (v12)** — `generateVariations()` combines each base preset with random prefix + suffix from `VARIATION_MODIFIERS[lang]` to produce 3 variations per base preset. Do NOT replace this with a pure random word salad — the modifiers are curated to produce natural-sounding variations. When real AI is added (In-Progress #3), it should augment, not replace, this local generator.
- **Avatar shape via `data-shape` attribute (v12 NEW)** — `#avatar` has `data-shape="round|square|hex|blob"`. CSS rules override the body rect's `rx`/`ry` accordingly. Do NOT use JS to change the SVG attributes directly — use `setShape(shape)` which updates `personality.shape` + the DOM attribute + persists to localStorage.
- **Wake-up reaction is "annoyed" (v11, kept in v12)** — the user explicitly requested this. `wakeUp()` sets `emotion:'annoyed'` and uses `pickPreset('wakeups')` for the line. Do NOT change back to grumpy.
- **Avatar click uses personality greeting (v12)** — `avatarClick()` picks a random emotion + uses `pickPreset('greetings')` for the line. Do NOT revert to hardcoded reactions.
- **Pop-out pills are absolutely positioned around the avatar head (v11, kept in v12)** — pills live inside `.avatar-wrap`. Do NOT revert to the old row-below layout.
- **No blue tap highlight (v11, kept in v12)** — all interactive pills/buttons have `-webkit-tap-highlight-color:transparent`. Tap feedback is custom CSS animation only.
- **Global click-to-look (v12 NEW)** — any click on empty space (not on avatar or controls) makes Printko look at that point. Do NOT remove this listener — it's the fix for the "can't get him to look at tap" bug.
- **Face stays centered when facing L/R (v12 FIX)** — `.facing-left`/`.facing-right` use `rotateY(±15deg)` only, NO `translateX`. Do NOT re-add the translateX shift.
- **Slovenian (SL) is the default language.** All new strings MUST be added to both `I18N.sl` and `I18N.en`, AND to `PRESETS.sl` and `PRESETS.en` if they're personality lines.
- **Light relaxed colors and minimalism** — avoid dark themes as defaults (Noč is the deliberate exception).
- **Weather themes cycle via the topbar sun button** — do NOT remove.
- **No comic-book speech cloud** — plain text below avatar.
- **Speech text uses Caveat font** — Printko's voice.
- **State descriptor (avatar-status) uses `--tdim` at 80% opacity** — deliberately less intense.
- **Avatar has NO hands/arms** (removed v10) — do NOT re-add.
- **Mock data in `DB` constant** — all panels render from `DB`.
- **`localStorage` keys prefixed `es-`** — `es-theme`, `es-lang`, `es-or-key`, `es-notion-key`, `es-notion-db`, `es-model`, **`es-personality` (v12 NEW)**. Do NOT rename.
- **Idle/sleep timing constants** — `IDLE_TIMEOUT=45000`, `GRUMPY_DURATION=4000`, `EYE_EXPLORE_INTERVAL=4000`, `EYE_FOCUS_DURATION=2500`.
- **Eye tracking proximity threshold = 350px**.
- **Deprecated files in `/archive/`** — per RULE 6, never delete old versions. v11 was added to archive in v12.

## 6. Known Bugs / Technical Debt

- **Vision-model false negatives on emotions** — subtle emotions may be reported as "neutral" by vision models. Verify via `getComputedStyle(.eyes-<name>).display === 'block'`.
- **Theme decoration visibility is intentionally subtle** — opacities 0.18–0.35 for minimalism.
- **`chatHistory` is in-memory only** — lost on reload.
- **No real API calls yet** — OpenRouter and Notion are UI-only stubs.
- **`generateReply()` ink breakdown is simplified** — returns flat 0.4ml per channel per print.
- **Face direction 3D rotation may not render on very old browsers** — falls back gracefully.
- **Pop-out pills can overlap on very narrow screens (<320px)** — they reposition tighter but may collide.
- **Variation generator is local-only (v12)** — produces ~45 variations using prefix+suffix combinator. Quality is decent but not as good as LLM-generated. See In-Progress #3.
- **The `GRUMPY_DURATION` constant name is now misleading** (v11) — it governs the annoyed wake-up phase, not grumpy. Kept per RULE 6.
- **Custom presets don't validate `{name}` placeholder (v12)** — users can type anything; if they don't include `{name}`, the sentence just won't have a name. Acceptable but could add a hint.
- **Shape change doesn't re-animate the body (v12)** — switching shape updates `rx`/`ry` instantly. Could add a smooth transition, but SVG attribute transitions are limited. Acceptable for now.
