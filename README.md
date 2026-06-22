# Printko

A custom assistant web app for the **eufy E1 UV printer**, centered around an animated character avatar named **Printko** — shaped like an actual printer with **3 RGB antennas** that pulse to signal answer type.

## What makes it special (v15)

- **3 RGB antennas** — left (red, short), center (green, medium), right (blue, long). Each pulses in its color. `signalAnswer(type)` makes the matching antenna pulse bright (green = success, red = error, blue = info) for 3s while the others dim. Top paper feed slot REMOVED — antennas sprout directly from the chassis.
- **3 independent background axes** — **Location** (forest / beach / ocean / mountains) × **Weather** (clear / cloudy / rain / snow / fog / storm) × **Time-of-Day** (dawn / day / dusk / night, auto-advancing every 10 min). All color transitions animate smoothly (`transition: background 2s ease, fill 2s ease`).
- **Thought-bubble suggestion icons** — 6 emoji icons (📊 💰 📋 🔮 📈 📦) float around Printko's head when you tap him, like thought bubbles. Hidden by default, auto-hide after 8s.
- **Redesigned FAB layout** — custom FAB SVG icon (waveform + bubble + 3 dots). Press = 6 report icons slide out in an arc + chat input appears at center-bottom. Structured chat panel sits between input and avatar (scrollable, headings/lists/bold/code in Caveat font). All UI max ¼ viewport width.
- **Printer-shaped avatar** — control panel face screen, output tray with paper, status LEDs, "eufy E1 UV" branding. Breathes, sways, floats higher above ground, turns to face left/right.
- **31 emotions** in 8 families — composed from 15 eye shapes × 8 eyebrow shapes × 17 mouth shapes. Curved-arch blink matches the eye's circular shape.
- **Smart eye tracking** — pupils follow your mouse when near, look in the direction of motion, focus-lock on clicks/taps.
- **Cozy personality** — Printko calls you by name (default: Lara). Configure name, tone (cozy/formal/playful), avatar shape (round/square/hex/blob), and preset sentences in Settings. "Generate 3× variations" expands the preset bank.
- **Attitude** — 8 attitude emotions (annoyed, exasperated, unimpressed, fedup, sarcastic, mischievous, dramatic, sideeye). Wakes up annoyed when disturbed from sleep.
- **Real OpenRouter agent** — bring your own API key (Claude / GPT-4o / Gemini / Mistral / DeepSeek). Falls back to offline keyword agent.
- **Real Notion sync** — query your projects database via Notion API.
- **Auto-weather** — geolocation + Open-Meteo API sets the `curWeather` axis automatically.
- **TTS** — Printko speaks his replies via Web Speech API.
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
├── index.html           # the entire app (HTML + CSS + JS inline, ~5460 lines) — v15
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
    ├── printko-v12.html           # v12
    ├── printko-v13.html           # v13
    └── printko-v14.html           # v14 (archived with deprecation header)
```

## For AI contributors

**Read these files before doing anything:**

1. `AI_CODING_RULES.md` — the 6 non-negotiable rules.
2. `PROJECT_STATUS.md` — architectural decisions you must not break (e.g. "3 independent background axes (Location × Weather × Time-of-Day)", "3 RGB antennas with signalAnswer()", "thought-bubble suggestion icons hidden by default", "custom FAB SVG icon", "all UI max 25vw", "status label directly below avatar", "structured chat-output panel", "topbar cycles locations", "auto-weather sets curWeather axis", "time auto-advances every 10 min", "31-emotion vocabulary", "personality system canonical", "wake-up is annoyed", "no blue tap highlight", plus all v10-v14 constraints retained).

## Tech

- Single self-contained HTML file — no build step, no framework
- Vanilla JS, vanilla CSS, inline SVG
- `localStorage` for all persistence (keys prefixed `es-`)
- Web Speech API for speech recognition + TTS
- Open-Meteo API for auto-weather (no key needed)
- Optional: OpenRouter API for live AI agent, Notion API for sync (user-supplied keys)
- Google Fonts (Nunito, Fraunces, Caveat, IBM Plex Mono)

## License

Private project. All rights reserved.
