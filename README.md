# Printko

A custom assistant web app for the **eufy E1 UV printer**, centered around an animated character avatar named **Printko**. The app tracks prints, finances, projects, and inventory, and lets you chat with the assistant. It is designed to connect to a Notion database for live data sync.

## What makes it special

- **Printko, the character avatar** — a friendly robot-printer with antenna, expressive eyebrows, eyes that track your mouse and **look in the direction of any tap/click**, and **31 named emotions**. He has **attitude**: he sleeps when idle (45s), wakes up **annoyed** for 4s, and occasionally turns to face left/right (face stays centered).
- **Cozy personality, not official** — Printko calls you by name (default: **Lara**). Configure your name, tone (cozy/formal/playful), and avatar shape (round/square/hex/blob) in the new **Printko · Osebnost** settings subsection.
- **Configurable preset bank** — Printko's greetings, wake-up lines, quips, and reactions come from a preset bank with 3 tones × 2 languages. Add your own custom presets, or hit **"Generate 3× variations"** to expand the base presets using a local template combinator (~45 variations).
- **Emotion system with attitude** — 31 emotions in 8 families: calm, happy, thinking, surprised, sad, angry/annoyed (annoyed, exasperated, unimpressed, fedup), sassy (smug, sarcastic, mischievous, dramatic, sideeye), and system. Each composed from 15 eye shapes × 8 eyebrow shapes × 17 mouth shapes.
- **Pop-out suggestion pills around his head** — 6 quick-question pills float around Printko's head. Tapping makes him react with a quip before answering. **No blue tap highlight** — custom CSS animation feedback only.
- **9 illustrated weather themes** — Trta (vine + butterfly), Jelka (pine + birds), Morje (sea + fish + sailboat), Solnce (sun), Noč (night + moon + stars + comet), Mleko (milk), Dež (rain), Sneg (snow + snowman), Meglja (fog).
- **Slovenian-first, English toggle** — every string, suggestion, agent reply, attitude line, wake-up quip, and personality preset is bilingual. SL is the default.
- **Light, relaxed, minimalist** — no clutter, no dark mode by default, no comic-book speech cloud. Printko's replies appear in a handwriting (Caveat) font directly below him.
- **Quick-access pills** for Novice, Projekti, Zgodovina, Tiski, Črnilo, and Finance — each opens a slide-in panel.
- **Fully responsive** — desktop, tablet, mobile (including landscape), very small phones. Respects `prefers-reduced-motion`.
- **Offline AI agent** — context-aware keyword-based replies. Bring your own OpenRouter key (UI wired, API call is a future task).
- **Notion sync UI** — enter your integration token and database IDs (UI wired, real sync is a future task).

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
├── index.html           # the entire app (HTML + CSS + JS inline, ~3420 lines)
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
    ├── printko-v10.html           # v10
    └── printko-v11.html           # v11
```

## For AI contributors

**Read these files before doing anything:**

1. `AI_CODING_RULES.md` — the 6 non-negotiable rules (read-before-write, surgical edits, maintain PROJECT_STATUS, modularize, ask before big changes, never delete without asking).
2. `PROJECT_STATUS.md` — what exists, what's done, what's queued, and the architectural decisions you must not break (e.g. "no comic-book speech cloud", "speech uses Caveat font", "avatar has no hands", "31-emotion vocabulary is canonical", "wake-up is annoyed not grumpy", "pills pop out around head", "no blue tap highlight", **"personality system is canonical (v12)"**, **"avatar shape via data-shape (v12)"**, **"global click-to-look (v12)"**, **"face stays centered, no translateX (v12)"**).

## Tech

- Single self-contained HTML file — no build step, no npm install, no framework
- Vanilla JS, vanilla CSS, inline SVG
- `localStorage` for all persistence (theme, language, API keys, **personality**)
- Google Fonts (Nunito, Fraunces, Caveat, IBM Plex Mono) loaded async with print-media swap

## License

Private project. All rights reserved.
