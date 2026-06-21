# Printko

A custom assistant web app for the **eufy E1 UV printer**, centered around an animated character avatar named **Printko**. The app tracks prints, finances, projects, and inventory, and lets you chat with the assistant. It is designed to connect to a Notion database for live data sync.

## What makes it special

- **Printko, the character avatar** — a friendly robot-printer with antenna, expressive eyebrows, eyes that track your mouse, and **31 named emotions**. He has **attitude**: he explores the room with his eyes, occasionally turns to face left/right, sleeps when idle (45s), and wakes up **annoyed** ("Uf. Kaj pa tokrat?") for 4 seconds before returning to neutral.
- **Emotion system with attitude** — 31 emotions organized into 8 families: calm (neutral, content, sleepy), happy (happy, joyful, grateful, proud, kiss), thinking (thinking, confused, dizzy), surprised (surprised, shocked), sad (sad, crying, worried, embarrassed), **angry/annoyed (grumpy, angry, annoyed, exasperated, unimpressed, fedup)**, **sassy (smug, sarcastic, mischievous, dramatic, sideeye)**, and system (speaking, listening, sleeping). Each composed from 15 eye shapes × 8 eyebrow shapes × 17 mouth shapes.
- **Avatar click = attitude mix** — clicking Printko no longer always gives happy greetings. Random reactions across helpful (~30%), sassy (~25%), attitude (~25%), dramatic (~10%), and sleepy (~10%) — each with a matching speech line in SL and EN.
- **Pop-out suggestion pills around his head** — the 6 quick-question pills pop out around Printko's head (top-left, top-right, mid-left, mid-right, bottom-left, bottom-right) with staggered pop-in + idle floating animations. Tapping a pill makes Printko react with an attitude quip before answering. **No blue tap highlight** — custom CSS animation feedback only.
- **Smart eye tracking** — pupils smoothly follow your mouse when within 350px, look in the direction of motion, occasionally focus-lock on you.
- **9 illustrated weather themes** — Trta (vine + butterfly), Jelka (pine + birds), Morje (sea + fish + sailboat), Solnce (sun), Noč (night + moon + stars + comet), Mleko (milk), Dež (rain), Sneg (snow + snowman), Meglja (fog). Each has subtle animated decorations.
- **Slovenian-first, English toggle** — every string, suggestion, agent reply, attitude line, and wake-up quip is bilingual. SL is the default.
- **Light, relaxed, minimalist** — no clutter, no dark mode by default, no comic-book speech cloud. Printko's replies appear in a handwriting (Caveat) font directly below him; state descriptors ("Pripravljen", "Printko spi…", "Uf, kaj pa zdaj?") use a softer, less intense color.
- **Quick-access pills** for Novice, Projekti, Zgodovina, Tiski, Črnilo, and Finance — each opens a slide-in panel.
- **Fully responsive** — works on desktop, tablet, mobile (including landscape), and very small phones. Respects `prefers-reduced-motion`. Pop-out pills reposition closer to avatar on smaller screens.
- **Offline AI agent** — context-aware keyword-based replies about your ink usage, projects, finances, and maintenance. Bring your own OpenRouter key (UI wired, API call is a future task).
- **Notion sync UI** — enter your integration token and database IDs in Settings (UI wired, real sync is a future task).

## Run it

Open `index.html` in any modern browser. No build step, no server, no dependencies.

```bash
git clone https://github.com/endpuppet/Printko.git
cd Printko
open index.html      # macOS
# or just double-click index.html
```

## Project structure

```
Printko/
├── index.html           # the entire app (HTML + CSS + JS inline, ~2930 lines)
├── AI_CODING_RULES.md   # 6 rules every AI contributor must follow
├── PROJECT_STATUS.md    # single source of truth for project state
├── README.md            # this file
├── .gitignore
└── archive/             # DEPRECATED older versions — DO NOT USE
    ├── README.md
    ├── eufy-dashboard-v6-1.html   # v6
    ├── eufy-dashboard-v7.html     # v7
    ├── eufy-studio.html           # v8
    ├── printko-v9.html            # v9
    └── printko-v10.html           # v10
```

## For AI contributors

**Read these files before doing anything:**

1. `AI_CODING_RULES.md` — the 6 non-negotiable rules (read-before-write, surgical edits, maintain PROJECT_STATUS, modularize, ask before big changes, never delete without asking).
2. `PROJECT_STATUS.md` — what exists, what's done, what's queued, and the architectural decisions you must not break (e.g. "no comic-book speech cloud", "speech uses Caveat font", "avatar has no hands", "31-emotion vocabulary is canonical", "wake-up is annoyed not grumpy", "pills pop out around head", "no blue tap highlight").

## Tech

- Single self-contained HTML file — no build step, no npm install, no framework
- Vanilla JS, vanilla CSS, inline SVG
- `localStorage` for all persistence (theme, language, API keys)
- Google Fonts (Nunito, Fraunces, Caveat, IBM Plex Mono) loaded async with print-media swap

## License

Private project. All rights reserved.
