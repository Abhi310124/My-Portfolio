# Portfolio — Ponnamudi Abhinav Sai Pavan Kalyan

A single-file animated portfolio. No build step, no framework, no dependencies to install.

```
abhinav-portfolio/
├── index.html         ← the entire site (HTML + CSS + JS)
├── MASTER_PROMPT.md   ← the design + content spec this was built from
└── README.md
```

## Run it

Just open `index.html`. Or serve it locally:

```bash
python -m http.server 8000
# → http://localhost:8000
```

## Two things to add

1. **Your photo** — drop a portrait next to `index.html` named `abhinav.jpg`.
   Until then the About section shows a placeholder frame. Portrait crop, ~900×1200 or larger.
2. **Your résumé** — save the PDF as `resume.pdf` in this folder, then add a nav button:
   ```html
   <a href="resume.pdf" class="btn-pill solid" download>&darr; Resume</a>
   ```
   Put it just after `<nav id="nav">`, before the `.nav-links` div.

## Deploying

Any static host works, since it's one file.

- **GitHub Pages** — push to a repo, then Settings → Pages → deploy from `main` / root.
  For `abhi310124.github.io`, name the repo exactly that and it serves at the root domain.
- **Netlify / Vercel** — drag the folder onto the dashboard. No build command, no output directory.

## Design system

Ported from the reference portfolio's typographic and motion system — full analysis in
[MASTER_PROMPT.md](MASTER_PROMPT.md).

| | |
|---|---|
| **Sans** | Space Grotesk — everything structural |
| **Serif** | Fraunces, *italic only*, weight 300 — accents, taglines, the `<em>` in every heading |
| **Mono** | DM Mono — dates, patent numbers, metrics |
| **Easing** | `cubic-bezier(0.16,1,0.3,1)` — one curve for the whole site |
| **Light** | `#F4F0E8` cream / `#141110` ink / `#D42820` red / `#B8935A` gold |
| **Dark** | `#0D0D0B` / `#EEE9E0` / `#E63B2E` / `#CBA06A` |

The signature move: a heavy sans headline where one clause flips to light italic serif in red —
*"I build things that **hold up at scale.**"*

### Animations

Per-character hero reveal · choreographed hero fade-ups · three drifting parallax blobs ·
scrolling skills marquee · IntersectionObserver scroll reveals with stagger · animated stat
count-up · custom lerped cursor ring · magnetic buttons · scroll progress bar · blurred sticky
nav · active-section nav underline · project card lift with sweeping underline · clip-path
mobile drawer.

### Accessibility

Deliberately stricter than the reference: full `prefers-reduced-motion` support, custom cursor
gated to fine pointers only, a real mobile drawer instead of hidden nav links, theme persisted
to `localStorage` and seeded from `prefers-color-scheme`, keyboard-dismissable drawer, visible
focus rings, and the hero name present in markup so it renders even if JS fails.

## Editing content

Everything lives in `index.html` in reading order — hero → ticker → about → stats → experience →
projects → education → recognition → contact.

- **Skills** — `<span class="stag">` items in the About section
- **Ticker** — the `TERMS` array in the script block
- **Stats** — `data-to`, `data-dec`, `data-suffix` on each `.snum`
- **A project** — copy an `<article class="pcard">` block; bump `data-n` and the `rev-d*` delay class
- **Section heading** — the `<em>` inside `.sh` is the italic serif clause
