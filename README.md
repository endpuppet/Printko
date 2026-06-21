# Printko

A custom assistant web app for the **eufy E1 UV printer**, centered around an animated character avatar named **Printko** — now shaped like an actual printer.

## What makes it special

- **Printer-shaped avatar** — Printko has a paper feed slot, control panel face screen, output tray with paper coming out, status LEDs, and "eufy E1 UV" branding. He breathes, sways, floats, and turns to face left/right.
- **31 emotions** in 8 families (calm, happy, thinking, surprised, sad, angry/annoyed, sassy, system) — composed from 15 eye shapes × 8 eyebrow shapes × 17 mouth shapes. Curved-arch blink matches the eye's circular shape.
- **FAB button input** — press to expand a 3-line text input, **hold to talk** (vibrates + speech recognition via Web Speech API). Printko grows larger when listening, shrinks + moves up when report space is needed.
- **Report icons** — 6 meaningful icons (ink, finance, projects, prints, history, news) replace the old quick-access pills.
- **Layered scene backgrounds** — each of the 9 weather themes has a **sky that circles slowly clockwise**, a horizon line, and a ground/sea layer. More animated elements: 5 birds, 3 butterflies, 8 falling leaves, 2 fish, sailboat, snowman, 40 twinkling stars, occasional comet, drifting clouds.
- **All weather conditions** — sunny (Trta, Solnce), forest (Jelka), sea (Morje), night (Noč), minimal (Mleko), rain (Dež), snow (Sneg), fog (Meglja).
- **Cozy personality** — Printko calls you by name (default: Lara). Configure name, tone (cozy/formal/playful), avatar shape (round/square/hex/blob), and preset sentences in Settings. "Generate 3× variations" expands the preset bank.
- **Attitude** — Printko has 8 attitude emotions (annoyed, exasperated, unimpressed, fedup, sarcastic, mischievous, dramatic, sideeye). He wakes up annoyed when disturbed from sleep.
- **Smart eye tracking** — pupils follow your mouse when near, look in the direction of motion, focus-lock on clicks/taps.
- **Pop-out suggestion pills** around his head — tap for a reaction + answer (no blue highlight).
- **Slovenian-first, English toggle** — every string, preset, and reply is bilingual.
- **Light, relaxed, minimalist** — no clutter, Caveat handwriting font for Printko's voice.
- **Fully responsive** — desktop, tablet, mobile, landscape, reduced-motion.

## Run it

Open `index.html` in any modern browser. No build step, no server, no dependencies.

**Note:** Speech recognition requires HTTPS or localhost. For local testing, serve via a simple HTTP server:
```bash
python3 -m http.server 3000
# then open http://localhost:3000
```

```bash
git clone https://github.com/endpuppet/Printko.git
cd Printko
open index.html      # macOS
```

## Project structure

```
Printko/
├── index.html           # the entire app (HTML + CSS + JS inline, ~3950 lines)
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
    ├── printko-v11.html           # v11
    └── printko-v12.html           # v12
```

## For AI contributors

**Read these files before doing anything:**

1. `AI_CODING_RULES.md` — the 6 non-negotiable rules.
2. `PROJECT_STATUS.md` — architectural decisions you must not break (e.g. "printer-shaped avatar", "FAB is primary input", "layered scene backgrounds", "curved-arch blink", "dynamic sizing via has-report/is-listening", "31-emotion vocabulary", "personality system canonical", "wake-up is annoyed", "no blue tap highlight").

## Tech

- Single self-contained HTML file — no build step, no framework
- Vanilla JS, vanilla CSS, inline SVG
- `localStorage` for all persistence
- Web Speech API for speech recognition
- Google Fonts (Nunito, Fraunces, Caveat, IBM Plex Mono)

## License

Private project. All rights reserved.
