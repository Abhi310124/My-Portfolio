# Master Prompt — Animated Portfolio for Ponnamudi Abhinav Sai Pavan Kalyan

> Derived from three sources: (1) design forensics of https://fayyaz.me/, (2) `Abhinav-Resume (1).pdf`,
> (3) the live GitHub API for `github.com/Abhi310124` (13 public repos, read 2026-08-31).
> This file is the input specification. `index.html` is the output built from it.

---

## 1. Reference design system (extracted from fayyaz.me)

### 1.1 Typography — exactly three families, each with one job

| Family | Role | Usage rules |
|---|---|---|
| **Space Grotesk** (300–700) | Everything structural | Body, nav, cards, buttons. Big display type at `font-weight:700`, `letter-spacing:-0.035em`, `line-height:0.9–0.95` |
| **Fraunces** (ital, opsz 9..144, wght 300/600/900) | Emotional accent | **Italic only, never roman.** Used at `font-weight:300` for the light-serif contrast against heavy sans. Applies to: taglines, the `<em>` in every section heading, pull-quotes, and the first letter of each hero name line |
| **DM Mono** (ital 0;1) | Data | Dates, periods, IDs, metrics. Never prose |

Single Google Fonts request, `display=swap`:
```
Space+Grotesk:wght@300;400;500;600;700
Fraunces:ital,opsz,wght@1,9..144,300;1,9..144,600;1,9..144,900
DM+Mono:ital@0;1
```

**The signature move:** a heavy sans headline where one clause is swapped to light italic Fraunces in the
accent red — `I build things that <em>hold up at scale.</em>` Every section heading follows this
two-line pattern: line 1 neutral sans, line 2 accent italic serif.

### 1.2 Type scale
```
hero name      clamp(3.8rem, 10vw, 10.5rem)   700  ls -0.035em  lh 0.9
contact title  clamp(3rem, 8vw, 9rem)         700  ls -0.04em   lh 0.95
section head   clamp(2.4rem, 5vw, 5.2rem)     700  ls -0.035em  lh 0.95
about head     clamp(2.2rem, 4.5vw, 4rem)     700  ls -0.03em   lh 1.05
pull-quote     clamp(1.4rem, 3vw, 2.4rem)     300  Fraunces italic
tagline        clamp(1.05rem, 2.2vw, 1.55rem) 300  Fraunces italic
card title     1.25–1.35rem                   600  ls -0.02em
body           0.87–0.97rem                   400  lh 1.6–1.8   color muted
eyebrow/label  0.68–0.72rem                   600  ls 0.12–0.16em  UPPERCASE
mono meta      0.70–0.76rem                   DM Mono
```

### 1.3 Colour — warm cream / near-black, one red, one gold
```css
:root{                        /* light */
  --bg:#F4F0E8;  --surface:#EDE9DF;  --card:#E8E4DA;
  --text:#141110; --muted:#7A7470;
  --accent:#D42820; --accent2:#B8935A;
  --accent-soft:rgba(212,40,32,0.07);
  --border:rgba(20,17,16,0.1);
}
[data-theme="dark"]{
  --bg:#0D0D0B;  --surface:#161614;  --card:#1C1C19;
  --text:#EEE9E0; --muted:#857E74;
  --accent:#E63B2E; --accent2:#CBA06A;
  --accent-soft:rgba(230,59,46,0.1);
  --border:rgba(238,233,224,0.08);
}
```
Rules: `border-radius:2px` on cards (near-square, editorial). Alternating section
backgrounds `--bg` / `--surface`. Section padding `8rem 4rem` desktop, `5rem 1.5rem` mobile.
Gold `--accent2` is reserved for secondary/credential affordances only.

### 1.4 Motion — one easing curve for the whole site
```css
--trans: 0.6s cubic-bezier(0.16,1,0.3,1);   /* expo-out */
```

Seven named animations:

| Name | Spec | Purpose |
|---|---|---|
| `charIn` | `translateY(110%)→0`, `0.7s`, stagger `0.055s`/char | Hero name, per-character, inside `overflow:hidden` masks |
| `fadeup` | `translateY(12–16px)→0` + opacity, `0.8s`, delays `0.2 / 1.3 / 1.55 / 1.8 / 2.1s` | Hero furniture, choreographed *after* the name lands |
| `bfloat` | `translate/scale` 3-stop, `22s` & `28s reverse` | Big background blobs |
| `bfloat2` | `translate` 2-stop, `19s` | Third blob, different rhythm |
| `ticker` | `translateX(0 → -50%)`, `10s linear` | Skills marquee (content duplicated exactly 2×) |
| `sscroll` | `scaleY` with `transform-origin` flip at 48/49% | Scroll-hint line that draws down then retracts |
| `.rev → .vis` | `translateY(36px)→0`, `0.75s`, `.rev-d1..d4` = `0.1s` steps | IntersectionObserver scroll reveal, `threshold:0.1`, unobserve after fire |

Interaction layer:
- **Custom cursor** — 5px dot tracking `mousemove` directly; 38px ring lerping at `0.11` in a rAF loop; ring grows to 64px + `--accent-soft` fill over interactive elements
- **Magnetic buttons** — pointer offset × `0.18`, released with a `0.4s` expo-out transition
- **Scroll progress** — 2px top bar, `width` transition `0.08s linear`
- **Nav** — `.scrolled` past 40px → `rgba(bg,0.88)` + `backdrop-filter:blur(18px)`
- **Hero parallax** — blobs at `0.28 / 0.16 / 0.22` × scrollY
- **Card hovers** — projects `translateY(-7px)` + underline `scaleX(0→1)` from left; experience cards `border-left` colour-in + `::before` tint fade
- **Ghost numerals** — `::before{content:attr(data-y)}` at `8rem/opacity:0.03`; giant monogram at `32vw/opacity:0.025` behind contact

### 1.5 Deliberate improvements over the reference
The reference has real accessibility gaps. Fix, don't copy:
1. `cursor:none` is applied globally — breaks touch and assistive input. Gate it behind `@media (hover:hover) and (pointer:fine)`.
2. No `prefers-reduced-motion` handling. Add a full block that neutralises transforms and reveals all content.
3. `.nav-links{display:none}` under 900px with no replacement — build a real animated mobile drawer.
4. Theme resets to dark on every load. Persist to `localStorage`, seed from `prefers-color-scheme`.
5. Modal has no Escape key, no focus trap, no `aria-modal`. Add all three.
6. No active-section nav indication. Add IntersectionObserver-driven `.active`.
7. Hero name is built by JS with `aria-label` — keep the label, and also degrade gracefully if JS fails.

---

## 2. Content model (verified facts only)

### 2.1 Identity
```
Full name   Ponnamudi Abhinav Sai Pavan Kalyan
Hero lines  "ABHINAV" / "KALYAN"          (first char of each in Fraunces italic accent)
Monogram    AK                             (32vw ghost behind contact)
Role        Application Developer — Adobe Experience Manager, IBM India
Location    Tenali, Andhra Pradesh, India
Email       abhinavsai27@gmail.com
Phone       +91-9866269986
LinkedIn    https://www.linkedin.com/in/abhinav3101/
GitHub      https://github.com/Abhi310124
LeetCode    https://leetcode.com/u/Abhinav_3101/
GitHub bio  "Coding Enthusiast!"  · 13 public repos · joined Nov 2023
Tagline     Fraunces italic, ≤560px: backend systems + reach into the physical world
```

### 2.2 Ticker (duplicate 2× for seamless loop)
`AEM` · `Sling Models` · `JCR` · `OSGi` · `HTL` · `Node.js` · `Express` · `MongoDB` · `Redis` ·
`REST APIs` · `JWT · OAuth2` · `WebSockets` · `Docker` · `TypeScript` · `Python` · `Java` · `C++` ·
`DSA` · `YOLOv8` · `OpenCV` · `Raspberry Pi` · `STM32` · `Embedded C`

### 2.3 Stat strip — animated count-up on reveal
| Value | Label |
|---|---|
| 8.32 | GPA / 10.0 — B.E. CSE |
| 1 | Published patent |
| 93% | YOLOv8 detection accuracy |
| 13 | Public repositories |

### 2.4 Experience — one card, IBM
**IBM India** · `Nov 2025 → Present` · Application Developer – Adobe Experience Manager (AEM)
- Developing and customizing enterprise content management solutions using Adobe Experience Manager
- Built and enhanced AEM components, templates, and workflows for scalable digital experiences
- Worked with core AEM architecture including Sling Models, JCR, and OSGi services

### 2.5 Featured projects — 6 cards, strongest first
1. **Smart Device Management Platform** — *Backend Systems · Aug 2025* — Node.js, Express, MongoDB,
   Redis, WebSocket/SSE, Joi, Jest+Supertest, Docker Compose. JWT access (15m) + refresh (7d) with
   rotation and blacklist. Redis caching TTL 15–30m with invalidation on write. Handles 1000+
   concurrent requests; cached device listing responds <100ms. Hourly cron deactivates devices idle
   >24h. Async CSV/JSON export with job IDs. Rate limiting, security headers, health + metrics endpoints.
2. **Smart Waste Management Platform** — *Full-Stack Monorepo · Oct 2025* — TypeScript across all three
   tiers. Express + MongoDB API with role-based access for Resident / Collector / Supervisor;
   React Native + Expo offline-first mobile app with auto-sync and a 60-second pickup confirmation
   window; React + Vite + Tailwind + Recharts supervisor KPI dashboard. Postman collection and
   `.http` test suite committed.
3. **Scalable Video Hosting Backend** — *RESTful API · Aug 2025* — Node.js, Express, MongoDB, Mongoose.
   Video uploads, comments, likes, subscriptions. JWT access + refresh tokens, Bcrypt hashing,
   MVC layering (controllers / models / routes / middlewares / services / utils), Mongoose schema validation.
4. **AI-Based Crop Protection System** — *Published Patent · May 2025* — Raspberry Pi 5 + YOLOv8 at 93%
   accuracy detecting birds, monkeys and wild boars in real time; species-specific deterrent audio;
   MCP3208 ADC water-level and rain sensing; Blynk IoT dashboard with Twilio SMS/WhatsApp alerts.
   **Patent Application No. 202541034995 (2025).** Badge this card with `--accent2`.
5. **STM32 Bare-Metal Firmware** — *Embedded Systems · Aug 2026* — STM32F407VGTX register-level work in
   Assembly and C: custom `startup_stm32f407vgtx.s`, hand-written FLASH and RAM linker scripts,
   memory-layout study, `syscalls`/`sysmem` retargeting, GPIO, interrupts, timers, comms protocols.
6. **OS & Concurrency Lab** — *Systems Programming · Jan–Mar 2026* — C++/C implementations of Dining
   Philosophers, Producer–Consumer, Reader–Writer, semaphores, race conditions, zombie and orphan
   processes, single vs multi-core threading, and FCFS / SJF non-preemptive / Round Robin schedulers.
   Includes LeetCode concurrency solutions (Print in Order, Fizz Buzz Multithreaded).

### 2.6 More repositories — compact secondary list
`Resume Parser` (Flask + HTML/CSS, Jul 2025) · `Forest Fire Prediction` (Python ML, Jul 2024) ·
`Blood Vessel Segmentation for Retinal Disease` (MATLAB, May 2025) ·
`Predicting Ionospheric Anomalies` (Jupyter, 2024) ·
`NodeJS Projects` (servers, fs, custom logging) · `JS Projects` (quiz, expense tracker, weather app)

### 2.7 Education
**Sathyabama Institute of Science and Technology**, Chennai — B.E. Computer Science and Engineering,
`2021 – 2025`, GPA **8.32 / 10.0**. Ghost numeral `data-y="2025"`.
Second card — *Areas of Focus* (each item backed by a real repository): Data Structures & Algorithms ·
Operating Systems & Concurrency · Backend Systems Design · Machine Learning & Computer Vision ·
Embedded Systems.

### 2.8 Recognition — 3 cards
- 📜 **Published Patent** · `--accent2` rank label — AI-Based Crop Protection System with Animal
  Detection and Water Management. Mono: `App. No. 202541034995 · 2025`
- 🎯 **93% Detection Accuracy** — YOLOv8 real-time multi-species detection on Raspberry Pi 5
- 🎓 **GPA 8.32 / 10.0** — B.E. Computer Science and Engineering, Sathyabama Institute

### 2.9 Contact
Heading `Let's build <em>something solid.</em>` · sub: open to backend, AEM and platform engineering work.
Pill links: email (click-to-copy with confirmation), phone, LinkedIn ↗, GitHub ↗, LeetCode ↗.
Footer: `© 2026 Ponnamudi Abhinav Sai Pavan Kalyan · Tenali, Andhra Pradesh` + `↑ Back to top`.

---

## 3. Build constraints
- Single self-contained `index.html`. No build step, no framework, no npm. Vanilla CSS + JS.
- Section order: hero → ticker → about → stats → experience → projects → education → recognition → contact → footer.
- Nav: logo `A<b>K</b>` · About / Work / Projects / Education / Recognition / Contact · theme toggle · mobile drawer.
- All animation on `transform`/`opacity` only. `will-change` on the cursor and blobs alone.
- Every scroll listener `{passive:true}`; parallax and progress share one rAF-throttled handler.
- Never state a fact absent from §2. No invented employers, dates, metrics or testimonials.
