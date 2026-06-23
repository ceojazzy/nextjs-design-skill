# Design Systems — Styles, Palettes & Type

Pick a **style**, a **palette**, and a **font pairing**, then wire them into the token system in
[../nextjs.md §2](../nextjs.md). One style per site — never mix randomly. Everything here is
expressed for **Next.js + Tailwind v4 + Motion**.

**Contents**

- [How to choose](#how-to-choose)
- [Style catalog (30+)](#style-catalog)
- [Color palettes by product type](#color-palettes-by-product-type)
- [Font pairings](#font-pairings)
- [Wiring a chosen system into tokens](#wiring-a-chosen-system-into-tokens)

---

## How to choose

1. **Product type → palette.** Find the closest row in [color palettes](#color-palettes-by-product-type).
2. **Personality → style.** Match the brief's adjectives to a row in the [style catalog](#style-catalog).
3. **Voice → fonts.** Pick a [font pairing](#font-pairings) whose mood matches.
4. **Sanity check:** does the style support the category (e.g. don't use neon brutalism for a bank)?
   Confirm the style's `Light ✓ / Dark ✓` and accessibility notes.

> **Default safe pick for most modern marketing sites:** _Minimalism + Soft-Slate Dark_,
> palette **SaaS / Portfolio**, fonts **Space Grotesk + Inter**. Add motion for life.

---

## Style catalog

Each style lists: **mood**, **when to use / avoid**, **signature effects**, and a **token seed**
(drop into `@theme` / CSS variables). Radii/shadows/easing are starting points — tune per brand.

### 1. Minimalism / Swiss

- **Mood:** clean, spacious, functional, high-contrast, grid-based.
- **Use for:** SaaS, dashboards, docs, professional tools, portfolios. **Avoid:** when you need playful warmth.
- **Effects:** subtle 200–250ms hovers, sharp hierarchy, generous whitespace, few or no shadows.
- **Tokens:** `--radius: 0–8px · --shadow: none/sm · accent: single color · type: large weight contrast`.

### 2. Soft-Slate Dark (recommended default dark)

- **Mood:** calm, premium, easy on the eyes — **not** pure black (great for 30–60 audiences).
- **Use for:** any modern site's dark theme. **Avoid:** OLED power-saving pitches (use Dark OLED).
- **Effects:** desaturated tonal surfaces, soft elevation, low-glow accents.
- **Tokens:** `--bg:#161922 · --surface:#20242f · --surface-2:#282d3a · --ink:#eef1f6 · --ink-3:#979eb1 · --accent:#74a6f2 · --line:#2c3140`.

### 3. Dark Mode (OLED)

- **Mood:** deep black, vivid accents, power-efficient.
- **Use for:** media/coding/entertainment apps on OLED. **Avoid:** print-first, outdoor-bright.
- **Effects:** minimal glow (`text-shadow:0 0 10px accent` sparingly), visible focus, high readability.
- **Tokens:** `--bg:#000 · --surface:#121212 · --ink:#e6e6e6 · accent: neon (#39FF14 / #0080FF / #BF00FF)`.

### 4. Glassmorphism

- **Mood:** frosted, layered, depth over a vibrant/photographic background.
- **Use for:** modern SaaS, fintech dashboards, overlays, navbars. **Avoid:** low-contrast text, perf-limited.
- **Effects:** `backdrop-filter: blur(12–20px)`, `bg: rgba(255,255,255,.08–.16)`, 1px light border.
- **Tokens:** `--glass-bg: rgba(255,255,255,.10) · --glass-border: rgba(255,255,255,.18) · --blur:16px`.
- **Tailwind:** `bg-white/10 backdrop-blur-xl border border-white/15` — keep text ≥4.5:1.

### 5. Neumorphism

- **Mood:** soft embossed/debossed monochrome UI.
- **Use for:** wellness, meditation, minimal toggles. **Avoid:** data-heavy, high-contrast needs (low contrast risk).
- **Effects:** dual shadow `-6px -6px 16px light, 6px 6px 16px dark`, 12–16px radius.
- **Tokens:** `--bg:#e8ecf2 · --shadow-light:#ffffff · --shadow-dark:#c2c9d6 · --radius:16px`.

### 6. Claymorphism

- **Mood:** chunky, playful, toy-like, bubbly.
- **Use for:** education, kids, creative tools, onboarding. **Avoid:** finance, legal, serious/medical.
- **Effects:** inner+outer soft shadows, thick 3–4px borders, 16–24px radius, bouncy easing `cubic-bezier(.34,1.56,.64,1)`.
- **Tokens:** `--radius:20px · pastel surfaces · --shadow-out:8px 8px 16px rgba(0,0,0,.12), inset -2px -2px 6px rgba(255,255,255,.7)`.

### 7. Brutalism / Neo-brutalism

- **Mood:** raw, stark, high-contrast, hard edges, visible borders.
- **Use for:** portfolios, editorial, counter-culture, dev tools. **Avoid:** corporate, conservative, a11y-critical.
- **Effects:** 0px radius, hard `4px 4px 0 #000` offset shadow, thick borders, instant or snappy transitions, bold type.
- **Tokens:** `--radius:0 · --border:3px solid #000 · --shadow:6px 6px 0 #000 · accents: pure primaries`.

### 8. Bento Grid

- **Mood:** modular dashboard-of-cards; mixed tile sizes, content-dense yet airy.
- **Use for:** feature sections, product overviews, dashboards, "why us". **Avoid:** linear storytelling.
- **Effects:** CSS grid spans, rounded cards, hover lift/spotlight, staggered reveal.
- **Tokens:** `grid auto-rows + col/row-span · --radius:20–28px · gap: 16–24px`.

### 9. Aurora / Mesh Gradient

- **Mood:** luminous flowing gradients (northern-lights), atmospheric hero backdrops.
- **Use for:** SaaS heroes, creative, music, premium. **Avoid:** data dashboards, distraction-sensitive content.
- **Effects:** large blurred conic/radial blobs animating 8–14s, blend modes, behind glass cards.
- **Tokens:** `gradient stops: blue→purple→pink→teal · animation 12s · saturate(1.1)`.

### 10. Retro-Futuristic / Neon (Synthwave / Cyberpunk)

- **Mood:** 80s sci-fi, neon glow, dark canvas, grids, scanlines.
- **Use for:** gaming, music, web3, dev/AI brands, hackathons. **Avoid:** banking, healthcare, conservative.
- **Effects:** neon `text-shadow`/`box-shadow` glow, gradient borders, grid floor, glitch, monospace accents.
- **Tokens:** `--bg:#0a0a12 · neon: #00e5ff / #ff2bd6 / #b14bff / #39FF14 · glow: 0 0 12px/24px accent`.
- See [animated-components.md](animated-components.md) for neon headers, borders, and cards.

### 11. Liquid Glass

- **Mood:** flowing translucent glass, iridescent, morphing.
- **Use for:** premium SaaS, high-end e-commerce, creative. **Avoid:** perf-limited, data-heavy.
- **Effects:** SVG/CSS morph 400–600ms, dynamic blur + saturate, subtle chromatic shift.

### 12. Flat / Modern Flat

- **Mood:** 2D, bold solid colors, no shadows/gradients, icon-forward.
- **Use for:** web/mobile apps, MVPs, dashboards. **Avoid:** luxury/immersive.
- **Tokens:** `--shadow:none · 4–6 solid colors · --radius:2–6px · 150–200ms hovers`.

### 13. Material (Modern)

- **Mood:** elevation system, state layers, tonal surfaces.
- **Use for:** app-like products, admin, Android-adjacent. **Effects:** ripple/state layer, elevation 1–5, 100–300ms.

### 14. Editorial / Magazine

- **Mood:** type-led, asymmetric grids, large serif headlines, generous leading.
- **Use for:** blogs, news, long-form, fashion. **Effects:** drop caps, pull quotes, column grids, restrained motion.

### 15. Luxury / Premium

- **Mood:** dark + gold/metallic accent, refined serif display, lots of negative space.
- **Use for:** luxury e-commerce, hospitality, high-end services. **Tokens:** `--bg:#0c0a09 · accent:#A16207 (gold) · serif display`.

### 16. Corporate / Trust

- **Mood:** navy + blue, professional, credibility markers.
- **Use for:** B2B, finance, legal, enterprise. **Tokens:** `primary:#0F172A · cta:#0369A1 · neutral surfaces`.

### 17. Playful / Vibrant Block

- **Mood:** energetic, big type (32px+), bold color blocks, large gaps.
- **Use for:** startups, youth, entertainment. **Avoid:** conservative/finance/healthcare.

### 18. Gradient-Mesh SaaS (AI-native)

- **Mood:** indigo/violet gradients, glow accents, "AI" sheen — use _sparingly_ and tastefully.
- **Use for:** AI/dev tools, modern SaaS. **Caution:** the "AI purple/pink gradient" is a cliché; differentiate with type & layout.

### 19. Monochrome + Single Accent

- **Mood:** grayscale system with one decisive accent color.
- **Use for:** portfolios, agencies, premium minimal. **Tokens:** `zinc/stone scale + accent:#2563EB or brand`.

### 20. Soft UI Evolution

- **Mood:** evolved neumorphism with **accessible** contrast and clearer elevation.
- **Use for:** modern enterprise, wellness, SaaS. **Tokens:** `softer multi-layer shadow + AA contrast + 8–12px radius`.

### 21. Motion-Driven / Interactive

- **Mood:** animation-heavy storytelling — scroll reveals, parallax, page transitions.
- **Use for:** portfolios, product launches, agencies. **Pair with** [animated-components.md](animated-components.md). Respect reduced-motion.

### 22. Skeuomorphic-lite (Tactile)

- **Mood:** subtle real-world texture/grain, gentle depth — modern restraint, not 2010 leather.
- **Use for:** premium products, music, photography. **Effects:** fine grain overlay, soft inner highlight.

### 23. Outline / Line-art

- **Mood:** thin strokes, line icons, wireframe-chic, lots of air.
- **Use for:** dev tools, technical brands, modern minimal. **Tokens:** `1–1.5px borders · stroke icons · low fills`.

### 24. Duotone

- **Mood:** two-color image treatment + matching UI.
- **Use for:** music, events, bold brand campaigns. **Effects:** `mix-blend` duotone over photos.

### 25. Grid / Blueprint

- **Mood:** visible grid lines, coordinates, technical aesthetic.
- **Use for:** engineering, data, architecture brands. **Effects:** faint grid background, mono labels, crosshair accents.

### 26. Warm Paper / Editorial Light

- **Mood:** off-white warm paper background, ink text, cozy and premium.
- **Use for:** writing, studios, boutiques. **Tokens:** `--bg:#f4f3ef · --ink:#1a1a1a · serif or grotesk display`.

### 27. High-Contrast Accessible

- **Mood:** WCAG AAA-first, symbol+color, large text, visible focus.
- **Use for:** government, healthcare, education, public. **Tokens:** `7:1 contrast · 16px+ · 3–4px focus ring`.

### 28. Frosted Dark (Glass on Slate)

- **Mood:** glass cards over soft-slate dark + aurora — modern SaaS staple.
- **Use for:** dashboards, premium SaaS dark mode. **Effects:** glass §4 over Soft-Slate §2 with subtle aurora §9.

### 29. Kinetic Typography

- **Mood:** type _is_ the design — animated, oversized, variable-weight headlines.
- **Use for:** agencies, launches, hero moments. **Pair with** WordsReveal / variable-font animation.

### 30. Spatial / 3D-lite

- **Mood:** perspective tilt, parallax depth, faux-3D cards (no heavy WebGL).
- **Use for:** product showcases, premium. **Effects:** `transform: perspective() rotateX/Y`, layered parallax. Add `react-three-fiber` only if true 3D is required.

---

## Color palettes by product type

WCAG-tuned light-mode palettes (foreground/background pairs meet contrast). Map each to a token in
[../nextjs.md §2](../nextjs.md). Provide a matching **soft-slate dark** variant (desaturate +
lighten accents ~10–15%, darken surfaces).

| Product type         | Primary   | Accent / CTA | Background | Foreground | Muted text | Border    | Notes                     |
| -------------------- | --------- | ------------ | ---------- | ---------- | ---------- | --------- | ------------------------- |
| SaaS (general)       | `#2563EB` | `#EA580C`    | `#F8FAFC`  | `#1E293B`  | `#64748B`  | `#E2E8F0` | Trust blue + orange CTA   |
| Micro-SaaS           | `#6366F1` | `#059669`    | `#F5F3FF`  | `#1E1B4B`  | `#64748B`  | `#E0E7FF` | Indigo + emerald          |
| E-commerce           | `#059669` | `#EA580C`    | `#ECFDF5`  | `#064E3B`  | `#64748B`  | `#A7F3D0` | Success green + urgency   |
| E-commerce luxury    | `#1C1917` | `#A16207`    | `#FAFAF9`  | `#0C0A09`  | `#64748B`  | `#D6D3D1` | Premium dark + gold       |
| B2B service          | `#0F172A` | `#0369A1`    | `#F8FAFC`  | `#020617`  | `#64748B`  | `#E2E8F0` | Professional navy         |
| Financial dashboard  | `#0F172A` | `#22C55E`    | `#020617`  | `#F8FAFC`  | `#94A3B8`  | `#334155` | Dark + green positives    |
| Analytics dashboard  | `#1E40AF` | `#D97706`    | `#F8FAFC`  | `#1E3A8A`  | `#64748B`  | `#DBEAFE` | Blue data + amber         |
| Healthcare           | `#0891B2` | `#059669`    | `#ECFEFF`  | `#164E63`  | `#64748B`  | `#A5F3FC` | Calm cyan + health green  |
| Education            | `#4F46E5` | `#EA580C`    | `#EEF2FF`  | `#1E1B4B`  | `#64748B`  | `#C7D2FE` | Playful indigo            |
| Creative agency      | `#EC4899` | `#0891B2`    | `#FDF2F8`  | `#831843`  | `#64748B`  | `#FBCFE8` | Bold pink + cyan          |
| Portfolio / personal | `#18181B` | `#2563EB`    | `#FAFAFA`  | `#09090B`  | `#64748B`  | `#E4E4E7` | Monochrome + blue         |
| Fintech / crypto     | `#7C3AED` | `#22D3EE`    | `#0B1020`  | `#F8FAFC`  | `#94A3B8`  | `#26304a` | Violet + cyan, dark-first |
| AI / dev tool        | `#6D28D9` | `#22D3EE`    | `#0A0A12`  | `#EDEDF5`  | `#9aa0b5`  | `#23263a` | Use gradient sparingly    |
| Hospitality / spa    | `#E8B4B8` | `#D4AF37`    | `#FFF5F5`  | `#2D3436`  | `#7a6e6e`  | `#efe2e2` | Soft pink + gold, calm    |
| Local business       | `#0D9488` | `#F59E0B`    | `#F8FAFC`  | `#0F172A`  | `#64748B`  | `#E2E8F0` | Approachable teal         |

**Semantic states (universal):** success `#22C55E` · warning `#F59E0B` · danger `#EF4444` ·
info `#3B82F6`. Always pair functional color with an icon/text (never color alone).

---

## Font pairings

Load with `next/font/google`. Set a **display** (headings) + **body** (text); optionally **mono**.

| Pairing             | Heading            | Body              | Mood                     | Best for                         |
| ------------------- | ------------------ | ----------------- | ------------------------ | -------------------------------- |
| Tech Startup ★      | Space Grotesk      | Inter / DM Sans   | modern, bold, futuristic | SaaS, dev tools, AI, startups    |
| Minimal Swiss       | Inter              | Inter             | neutral, functional      | dashboards, docs, design systems |
| Geometric Modern    | Outfit             | Work Sans         | contemporary, versatile  | portfolios, agencies, landing    |
| Modern Professional | Poppins            | Open Sans         | clean, friendly          | corporate, business, SaaS        |
| Classic Elegant     | Playfair Display   | Inter             | luxury, editorial        | fashion, spa, premium e-comm     |
| Wellness Calm       | Lora               | Raleway           | calm, organic            | health, wellness, meditation     |
| Bold Statement      | Bebas Neue         | Source Sans 3     | dramatic headlines       | agencies, events, sports         |
| Developer Mono      | JetBrains Mono     | IBM Plex Sans     | technical, precise       | dev tools, docs, code            |
| Editorial Classic   | Cormorant Garamond | Libre Baskerville | literary, refined        | publishing, blogs, news          |
| Playful Creative    | Fredoka            | Nunito            | fun, warm                | kids, education, gaming          |
| Retro Vintage       | Abril Fatface      | Merriweather      | nostalgic, bold          | breweries, restaurants, posters  |
| Display Grotesk     | Clash Display\*    | Satoshi\*         | striking, premium        | luxury, fashion (\*Fontshare)    |

★ = skill default. Headline-only display faces (Bebas, Abril, Clash) must **not** be used for body.

```ts
// app/fonts.ts — example (Tech Startup pairing)
import { Space_Grotesk, Inter, JetBrains_Mono } from "next/font/google";
export const display = Space_Grotesk({
  subsets: ["latin"],
  variable: "--font-display",
  display: "swap",
});
export const body = Inter({
  subsets: ["latin"],
  variable: "--font-body",
  display: "swap",
});
export const mono = JetBrains_Mono({
  subsets: ["latin"],
  variable: "--font-mono",
  display: "swap",
});
```

```tsx
// app/layout.tsx
import { display, body, mono } from "./fonts";
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html
      lang="en"
      className={`${display.variable} ${body.variable} ${mono.variable}`}
    >
      <body>{children}</body>
    </html>
  );
}
```

---

## Wiring a chosen system into tokens

Once you've chosen **style + palette + fonts**, encode them once in `globals.css` so every
component inherits them (full token system in [../nextjs.md §2](../nextjs.md)):

```css
@import "tailwindcss";

:root {
  /* palette → semantic tokens (light) */
  --bg: #f8fafc;
  --surface: #ffffff;
  --surface-2: #f1f5f9;
  --ink: #1e293b;
  --ink-2: #475569;
  --ink-3: #64748b;
  --line: #e2e8f0;
  --line-2: #cbd5e1;
  --accent: #2563eb;
  --accent-ink: #ffffff;
  --radius: 16px;
  --ease: cubic-bezier(0.22, 1, 0.36, 1);
}
.dark {
  /* soft-slate dark variant */
  --bg: #161922;
  --surface: #20242f;
  --surface-2: #282d3a;
  --ink: #eef1f6;
  --ink-2: #c4cad6;
  --ink-3: #979eb1;
  --line: #2c3140;
  --line-2: #3a4150;
  --accent: #74a6f2;
  --accent-ink: #0b0d12;
}

@theme inline {
  --color-bg: var(--bg);
  --color-surface: var(--surface);
  --color-surface-2: var(--surface-2);
  --color-ink: var(--ink);
  --color-ink-2: var(--ink-2);
  --color-ink-3: var(--ink-3);
  --color-line: var(--line);
  --color-line-2: var(--line-2);
  --color-accent: var(--accent);
  --color-accent-ink: var(--accent-ink);
  --font-display: var(--font-display);
  --font-body: var(--font-body);
  --font-mono: var(--font-mono);
}
```

Now components use `bg-surface`, `text-ink`, `text-ink-3`, `border-line`, `text-accent`,
`font-display`, etc., and **theme-switch automatically**. To switch styles later, you change the
token block — not the components.
