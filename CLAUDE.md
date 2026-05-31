# CLAUDE.md — VibeQuota Site

Context file for Claude Code. Read this before touching anything.

---

## What this repo is

Static marketing site for **VibeQuota** — an AI usage monitor for developers and power users working across Claude, Claude Code, OpenAI Codex, and other AI coding tools. The site lives at [vibequota.com](https://vibequota.com).

This is not the application. It is the public-facing presence: coming soon page today, expanding to marketing, support, privacy, and terms as the product ships.

---

## Owner

**PassingLane Solutions LLC**
Built by and for developers who use multiple AI tools daily and need visibility into limits, usage, and availability.

---

## Infrastructure

| Layer | Service |
|---|---|
| Domain registrar | Namecheap |
| DNS + CDN | Cloudflare |
| Hosting | Cloudflare Pages |
| Source | GitHub → `jrccsi/vibequota-site` |
| Deploy | Auto on push to `main` |

Pushes to `main` deploy automatically. No build step — this is a static site. What you commit is what ships.

---

## Repo structure

```
vibequota-site/
├── index.html          # The entire site (single page, coming soon)
├── CLAUDE.md           # This file
├── README.md           # Short project description
├── AGENTS.md           # Codex CLI agent config
├── RTK.md              # rtk CLI usage (token-optimized shell proxy)
├── .gitignore          # Excludes .env, secrets, settings.local.json
└── .claude/
    └── settings.local.json   # Local only — never committed
```

---

## Shell commands

This repo uses `rtk` as a token-optimized CLI proxy. Prefix shell commands accordingly:

```bash
rtk git status
rtk git add index.html
rtk git commit -m "message"
rtk git push
```

See `RTK.md` for full usage and meta commands.

---

## Design system

The site is a single self-contained `index.html`. No framework, no build tool, no dependencies beyond Google Fonts.

**Fonts**
- Hero wordmark: `Space Mono` (700) — loaded from Google Fonts
- Body + UI: `JetBrains Mono` (300, 400, 500, 600) — loaded from Google Fonts

**Color palette**
- Background: `#08080b`
- Amber accent: `#f59e0b` — primary brand color, used for logo, highlights, warnings
- Text primary: `#f0eee8`
- Text muted: `#5a5868`, `#3a3a48`, `#28282e` (progressively dimmer)
- Green (healthy): `#34d399`
- Amber (warning): `#f59e0b`
- Red (critical): `#f87171`

**Visual rules**
- Dark background only. No light mode.
- Noise texture overlay via inline SVG data URI — do not remove.
- Amber radial glow at top — do not remove.
- All entrance animations use `.a1`–`.a5` staggered `rise` keyframes.
- Widget card has a top-edge amber gradient highlight (`::before` pseudo) — preserve it.

---

## Widget logic

The central widget is the product demo. It shows:
- **Claude** — session % and weekly % with a live countdown to next reset
- **OpenAI Codex** — session % and weekly % with a live countdown to next reset

Each service section is a `.svc-section` block with two `.svc-row` rows (Session, Weekly) and a `.svc-reset` countdown below.

The warning bar (`.w-warn`) fires when a session is burning fast. Currently hardcoded to Claude session.

Countdowns tick live via `setInterval` in JS. Claude reset starts at 1h 19m, Codex at 5h 55m. These are illustrative — not real API data.

Bar fill colors follow usage level:
- `f-green` / `p-green` → under ~50%
- `f-amber` / `p-amber` → 50–85%
- `f-red` / `p-red` → 85%+

---

## What not to do

- Do not add a framework. This stays vanilla HTML/CSS/JS.
- Do not add a build step or `package.json`.
- Do not commit `.claude/settings.local.json` — it is gitignored for a reason.
- Do not commit `.env` or any file containing secrets or API keys.
- Do not add email capture without a confirmed backend (Formspree or Buttondown — ask first).
- Do not change the font on the hero wordmark without explicit direction. Space Mono was a deliberate choice.
- Do not proxy Cloudflare DNS records until GitHub/Cloudflare Pages cert is issued. Already resolved — leave DNS as-is.

---

## Deployment checklist

Before pushing:
1. Validate HTML opens cleanly in a browser locally
2. Check the widget bars animate on load
3. Check the reset countdowns tick
4. Confirm the warning bar is visible
5. Push to `main` — Cloudflare Pages deploys automatically

After pushing:
```bash
curl -I https://vibequota.com
```
Expect `HTTP/2 200`.
