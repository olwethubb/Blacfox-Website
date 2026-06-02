# Blacfox — Website Frontend

> **Status:** Design / dev environment — site is not live.  
> **Next step:** Lovable will use this codebase as the visual foundation before adding auth, CMS, and other platform facilities.

---

## Quick start

```bash
# Clone the repo
git clone https://github.com/amilio65-spec/fox_editorial.git
cd fox_editorial

# No build step — open any .html file directly in a browser
# Recommended: use a local server to avoid CORS issues with SVGs
npx serve .        # or
python -m http.server 8080
```

> **Before every working session, pull first:**
> ```bash
> git pull origin main
> ```

---

## Project overview

Pure HTML + CSS + vanilla JS marketing website for **Blacfox** — a B2B tech growth consultancy based in Cape Town. No frameworks, no bundlers, no dependencies beyond Google Fonts and Chart.js (loaded from CDN).

The design language is built around:
- An **orange grid** background (`rgba(233,92,37,0.18)` at 64 × 64 px, `background-attachment: fixed`)
- **Liquid glass** cards (`backdrop-filter: blur + saturate + brightness` on semi-transparent panels)
- **Floating orange orbs** as ambient depth layers
- **Montserrat** (900, 800, 700, 600, 500, 400) via Google Fonts

---

## File structure

```
fox_editorial/
│
├── index.html            # Home — hero, proof, agency model, PMF preview, FAQ
├── method.html           # Method — PMF eight-step radial diagram, 4Ps, Pos vs Msg
├── for-partners.html     # For Partners — co-op mistakes infographic, case study
├── for-isvs.html         # For ISVs — three problems, channel enablement FAQ
├── services.html         # Services — three service cards + PMF timing rings
├── work.html             # Work — Adept ICT case study with ig9 stats grid
├── about.html            # About — story, credibility stats, team, Africa narrative
├── contact.html          # Contact — direct info + discovery call form
├── seo-checklist.html    # Internal — SEO checklist (ig10) + content roadmap (ig11)
│
├── styles.css            # Shared design system — single source of truth
├── fox-icon.svg          # Blacfox fox silhouette icon (nav + favicon)
│
└── Infographics/
    └── blacfox-infographics.html   # Source file for ig4–ig11 infographic blocks
```

---

## Design system — `styles.css`

### CSS variables (`:root`)

| Token | Value | Usage |
|---|---|---|
| `--dark` | `#000000` | Body background base |
| `--mid` | `#1a1a1a` | Section-mid overlays |
| `--card` | `#1a1a1a` | Card base (overridden by glass) |
| `--light` | `#e0e0e0` | Primary text colour |
| `--orange` | `#e95c25` | Brand accent — all interactive/highlight use |
| `--muted` | `#888888` | Secondary / body text |
| `--border` | `rgba(255,255,255,0.08)` | Subtle dividers |
| `--site-px` | `60px → 32px → 20px` | Horizontal page padding (responsive via `@media` on `:root`) |
| `--site-max` | `1200px` | Content column max-width |

### Typography

- **Font:** Montserrat (Google Fonts) — weights 300–900 + italic 800
- **Hero title:** `clamp(40px, 5.5vw, 80px)` weight 900
- **Section heading:** `clamp(26px, 3.2vw, 44px)` weight 800
- **CTA heading:** `clamp(28px, 4vw, 54px)` weight 900
- **Body / section-p:** `16px` weight 400, `color: var(--muted)`

### Liquid glass recipe

All cards use the same base glass formula (defined in `styles.css` at the `/* LIQUID GLASS */` block):

```css
background: linear-gradient(135deg,
  rgba(255,255,255,0.10) 0%,
  rgba(255,255,255,0.03) 45%,
  rgba(255,255,255,0.07) 100%
);
backdrop-filter: blur(20px) saturate(1.9) brightness(1.14);
border: 1px solid rgba(255,255,255,0.15);
box-shadow:
  0 4px 32px rgba(0,0,0,0.28),
  inset 0 1.5px 0 rgba(255,255,255,0.22),
  inset 0 -1px 0 rgba(0,0,0,0.08);
```

Orange-tinted glass (Blacfox-branded surfaces, CTA, output cards):

```css
background: linear-gradient(135deg,
  rgba(233,92,37,0.52) 0%,
  rgba(233,92,37,0.24) 48%,
  rgba(233,92,37,0.44) 100%
);
backdrop-filter: blur(22px) saturate(2.3) brightness(1.32);
border: 1px solid rgba(255,160,80,0.45);
```

> **Important:** `overflow: clip` (not `overflow: hidden`) must be used on any container that holds glass cards. `overflow: hidden` creates a stacking context that blocks `backdrop-filter` from seeing the body background behind it.

### Named component classes

| Class | Description |
|---|---|
| `.dark-card` | Standard neutral glass card |
| `.glass-card` | Utility class with `!important` — for inline-styled elements |
| `.faq-item` | Accordion item (glass + JS toggle) |
| `.step-item` | Method step row (two-col grid) |
| `.service-card` | Services page card |
| `.case-study-card` | Case study container |
| `.compare-infographic` | Agency comparison two-col grid |
| `.cmp-col.bad / .good` | Comparison column variants |
| `.pmf-radial` | Home page PMF three-col spoke layout |
| `.pvm-wrap` | Positioning vs Messaging two-col layout |
| `.pmf-grid` | Method page eight-step grid |
| `.four-ps-wrap` | 4Ps quadrant grid |
| `.mistakes-grid` | Co-op mistakes card grid |
| `.section-tag` | Orange eyebrow label above headings |
| `.section-h` | Section heading (all pages) |
| `.section-p` | Section body paragraph |
| `.btn-primary` | Orange glass button |
| `.btn-ghost` | Neutral glass button |
| `.btn-white` | Light glass button (on orange CTA) |

### Scroll reveal

Every page uses an `IntersectionObserver` targeting `.reveal`, `.reveal-left`, `.reveal-right`, `.reveal-scale`, and `.stagger`. Elements re-animate every time they enter the viewport (observer does **not** call `unobserve`).

```js
const ro = new IntersectionObserver(entries => {
  entries.forEach(e => {
    if (e.isIntersecting) e.target.classList.add('visible');
    else e.target.classList.remove('visible');
  });
}, { threshold: 0.12 });
```

---

## Navigation

Fixed bottom → top pill navigation. Two separate pills:

1. **Logo pill** (`.nav-logo.nav-pill-glass`) — fox icon, links to `index.html`
2. **Links pill** (`.nav-links-pill.nav-pill-glass`) — orange-tinted glass, 8 page links

Active state set by JS matching `window.location.pathname` to `data-page` attributes.

---

## Infographics (Lovable integration note)

The `Infographics/blacfox-infographics.html` file is the **source** for eight data-driven visual blocks (ig4–ig11). These have been extracted and placed into the relevant pages with full style harmonisation. When Lovable ingests this repo:

- **ig4** (PMF radial) → `method.html`
- **ig5** (4Ps quadrant) → `method.html`
- **ig6** (Pos vs Msg) → `method.html`
- **ig7** (Agency comparison) → `index.html`
- **ig8** (Co-op mistakes) → `for-partners.html`
- **ig9** (Case study stats) → `work.html`
- **ig10 + ig11** (SEO checklist + content roadmap) → `seo-checklist.html`

All infographic CSS lives in per-page `<style>` blocks in each file's `<head>`, **after** the `<link rel="stylesheet" href="styles.css"/>` tag. This is intentional — page-level styles override shared styles where needed.

---

## Development workflow

```bash
# Always pull before starting work
git pull origin main

# Make changes, then:
git add .
git commit -m "feat: describe what changed"
git push origin main
```

### Branching (recommended for multiple devs)

```bash
git checkout -b feature/your-feature-name
# … make changes …
git push origin feature/your-feature-name
# Open a PR on GitHub for review before merging to main
```

---

## Pages at a glance

| File | Title tag | Primary keyword |
|---|---|---|
| `index.html` | B2B Tech Marketing Agency for Microsoft & SAP Partners | B2B tech marketing agency South Africa |
| `method.html` | Positioning & Messaging Framework for B2B Tech Companies | positioning and messaging framework B2B |
| `for-partners.html` | Microsoft & SAP Partner Marketing Agency | Microsoft partner marketing agency |
| `for-isvs.html` | ISV Marketing Agency | ISV marketing agency |
| `services.html` | B2B Tech Marketing Services | B2B demand generation agency |
| `work.html` | B2B Tech Marketing Case Studies | B2B tech marketing case studies |
| `about.html` | About Blacfox — B2B Tech Growth Consultancy | B2B tech growth consultancy South Africa |
| `contact.html` | Book a Discovery Call | contact Blacfox |
| `seo-checklist.html` | SEO Implementation Checklist *(internal)* | — |

---

## Contact details in the site

```
Email:    info@blacfox.com
Phone:    +27 21 493 9327
LinkedIn: /company/blacfox-enterprises
Location: Cape Town, South Africa
```

---

*Blacfox Website — Design system v1 · June 2026*
