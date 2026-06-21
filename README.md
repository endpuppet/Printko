# Printko

A custom assistant web app for the **eufy E1 UV printer**, centered around an animated character avatar named **Printko**. The app tracks prints, finances, projects, and inventory, and lets you chat with the assistant. It is designed to connect to a Notion database for live data sync.

## What makes it special

- **Printko, the character avatar** — a friendly robot-printer with antenna, eyes that explore the room, and a body that breathes, sways, and floats. He has multiple emotions (happy, sad, surprised, thinking, sleeping, grumpy) and reacts to your mouse and taps.
- **Sleep / wake cycle** — after 45 seconds of inactivity, Printko falls asleep (Zzz floating up, dim status light). Click him or send a message and he wakes up **grumpy** for 4 seconds before returning to neutral.
- **9 illustrated weather themes** — Trta (vine), Jelka (pine), Morje (sea), Solnce (sun), Noč (night), Mleko (milk), Dež (rain), Sneg (snow), Meglja (fog). Each has subtle animated decorations (swaying trees, twinkling stars + comets, falling rain, drifting snow, moving waves). Cycle them with the topbar sun button.
- **Slovenian-first, English toggle** — every string, suggestion, and agent reply is bilingual. SL is the default.
- **Light, relaxed, minimalist** — no clutter, no dark mode by default, no comic-book speech cloud. Plain text sits below the avatar.
- **Quick-access pills** for Novice, Projekti, Zgodovina, Tiski, Črnilo, and Finance — each opens a slide-in panel.
- **Offline AI agent** — context-aware keyword-based replies about your ink usage, projects, finances, and maintenance schedule. Bring your own OpenRouter key (UI wired, API call is a future task).
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
├── index.html           # the entire app (HTML + CSS + JS inline, ~2350 lines)
├── AI_CODING_RULES.md   # 6 rules every AI contributor must follow
├── PROJECT_STATUS.md    # single source of truth for project state
└── README.md            # this file
```

## For AI contributors

**Read these files before doing anything:**

1. `AI_CODING_RULES.md` — the 6 non-negotiable rules (read-before-write, surgical edits, maintain PROJECT_STATUS, modularize, ask before big changes, never delete without asking).
2. `PROJECT_STATUS.md` — what exists, what's done, what's queued, and the architectural decisions you must not break.

## Tech

- Single self-contained HTML file — no build step, no npm install, no framework
- Vanilla JS, vanilla CSS, inline SVG
- `localStorage` for all persistence (theme, language, API keys, idle timing)
- Google Fonts (Nunito, Fraunces, Caveat, IBM Plex Mono) loaded async with print-media swap

## License

Private project. All rights reserved.
