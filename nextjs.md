# Modern Website Design — Master Handbook (Next.js)

> A long‑form, advanced **skill**: read it _before_ designing or building any website. It
> encodes a complete modern design system, deep motion engineering, a **category playbook**
> (Portfolio, Dashboard, …), a **pattern catalog** ("if asked to add X, do Y"), full
> copy‑paste components, and a large curated library directory. This is the **foundations**
> layer of the _nextjs-modern-design_ skill — see [SKILL.md](SKILL.md) and the companion
> references: [design-systems.md](references/design-systems.md) ·
> [components/](references/components/README.md) ·
> [animated-components.md](references/animated-components.md) ·
> [ux-rules.md](references/ux-rules.md).
>
> **Doctrine:** _calm layout, loud motion._ Restraint in color and structure; richness in
> motion and micro‑interaction. Modern only — no dated bevels/drop‑shadows‑on‑everything, no
> pure‑black dark mode, no stocky clip‑art, no carousels-as-hero. Always ship a
> `prefers-reduced-motion` fallback and hit WCAG AA.

**Table of contents** 0. [How an AI should use this skill](#0-how-an-ai-should-use-this-skill)

1. [Stack, setup & project structure](#1-stack-setup--project-structure)
2. [Design tokens & theming](#2-design-tokens--theming)
3. [Color, type, spacing & scales](#3-color-type-spacing--scales)
4. [Modern CSS techniques](#4-modern-css-techniques)
5. [Motion engineering (Motion / Framer)](#5-motion-engineering)
6. [Website categories playbook](#6-website-categories-playbook)
7. [Pattern catalog — “add X”](#7-pattern-catalog--add-x)
8. [Component animation reference](#8-component-animation-reference)
9. [Advanced animation recipes](#9-advanced-animation-recipes)
10. [Accessibility (deep)](#10-accessibility-deep)
11. [Performance (deep)](#11-performance-deep)
12. [Responsive & fluid design](#12-responsive--fluid-design)
13. [Images, media & icons](#13-images-media--icons)
14. [Forms & validation](#14-forms--validation)
15. [SEO, metadata & sharing](#15-seo-metadata--sharing)
16. [Page & view transitions](#16-page--view-transitions)
17. [Library directory](#17-library-directory)
18. [Checklists](#18-checklists)
19. [Do / Don’t & anti‑patterns](#19-do--dont--anti-patterns)
20. [Appendix: copy‑paste components](#20-appendix-copy-paste-components)

---

## 0. How an AI should use this skill

Follow this loop **every time**:

1. **Classify the site** → pick a category in §6 (Portfolio/Agency, Dashboard/Admin, SaaS
   Landing, E‑commerce, Blog/Docs, Local/Business, Event, Personal). Category decides layout
   density, component set, and _how much_ motion is appropriate.
2. **Infer intent, don’t interrogate.** Read the brief/chat carefully, choose the most useful
   interpretation, and build. Only ask when truly blocked. Re‑read requirements before coding.
3. **Establish the foundation** (§2–§4): tokens, light+dark theming, type scale, spacing,
   one signature easing.
4. **Compose from patterns** (§7). Every feature request maps to a reusable _item_:
   - "contact / chat / quick action button" → **Floating Action Button** (§7.13)
   - "menu" → **Nav + dropdown** (§7.1); "numbers/metrics" → **KPI/counters** (§7.10)
   - "table" → **Data table** (§7.16); "graphs" → **Charts** (§7.17)
5. **Layer motion** (§5, §8, §9): _ambient_ (background/float) → _scroll‑linked_ (reveal,
   parallax) → _interaction_ (hover, magnetic, spotlight). Never animate everything at once.
6. **Verify** (§10–§12, §18): AA contrast in both themes, keyboard + focus, responsive,
   reduced‑motion fallback, transform/opacity‑only animation, lazy media, build + lint clean.

**Intent cheatsheet** (map fuzzy requests → concrete work):

| User says                       | Likely means                    | Build                                      |
| ------------------------------- | ------------------------------- | ------------------------------------------ |
| "make it modern / stylish"      | strong type, whitespace, motion | §2–§5 foundation + §7 hero/cards/reveal    |
| "more alive / animated"         | continuous + scroll motion      | §8 particle bg, parallax, marquee, reveals |
| "too dark / hard to read"       | pure black, low contrast        | soft‑slate dark (§2), lift muted text, AA  |
| "add WhatsApp / chat / call"    | persistent contact              | **FAB** pattern (§7.13), channel `href`    |
| "dashboard / admin / analytics" | dense data UI                   | §6.2 + tables/charts, restrained motion    |
| "portfolio / agency"            | story + trust + 1 CTA           | §6.1 blueprint                             |
| "it hangs when scrolling"       | heavy sticky‑pin section        | remove tall `position:sticky` pins (§19)   |

---

## 1. Stack, setup & project structure

### 1.1 The baseline

| Concern       | Choice                          | Notes / link                                                               |
| ------------- | ------------------------------- | -------------------------------------------------------------------------- |
| Framework     | **Next.js** App Router (RSC)    | server components, routing, image opt — <https://nextjs.org/docs>          |
| Runtime       | **React 19**                    | `useSyncExternalStore`, transitions — <https://react.dev>                  |
| Styling       | **Tailwind CSS v4**             | token‑driven, `@theme` — <https://tailwindcss.com/docs>                    |
| Animation     | **Motion** (`motion`)           | springs/scroll/layout/variants — <https://motion.dev/docs>                 |
| Smooth scroll | **Lenis**                       | feeds Motion scroll hooks — <https://github.com/darkroomengineering/lenis> |
| Fonts         | `next/font`                     | self‑hosted, zero CLS                                                      |
| Icons         | **Lucide** (or hand‑rolled SVG) | <https://lucide.dev>                                                       |

```bash
npm i next react react-dom motion lenis
npm i -D tailwindcss @tailwindcss/postcss typescript @types/react @types/node eslint eslint-config-next
```

**When to deviate:** for a heavy _app_ (dashboard) you may swap a headless system
(Radix/shadcn) and add TanStack Table/Query; for timeline‑heavy creative work, add **GSAP**;
for 3D, add **react‑three‑fiber**. Motion + Tailwind is the default for marketing/portfolio.

### 1.2 Config files

```js
// postcss.config.mjs
export default { plugins: { "@tailwindcss/postcss": {} } };
```

```ts
// next.config.ts
import type { NextConfig } from "next";
const nextConfig: NextConfig = {
  images: {
    remotePatterns: [{ protocol: "https", hostname: "images.unsplash.com" }],
  },
};
export default nextConfig;
```

### 1.3 Recommended structure

```
app/
  layout.tsx            # fonts, theme script, providers, global chrome (header/footer/bg/cursor)
  globals.css           # tokens + theming + keyframes + base layer
  page.tsx              # routes…
  (marketing)/…         # optional route groups
  components/
    providers/          # smooth-scroll, theme, query
    chrome/             # site-header, site-footer, scroll-progress, cursor, background, fab
    motion/             # reveal, stagger, parallax, marquee, magnetic, spotlight, text-reveal, counter
    ui/                 # button, card, input, select, dialog, tabs, badge, toast
    sections/           # hero, features, pricing, faq, cta, stats
  lib/                  # utils, config constants (contact handles!), data
```

> Keep **one config constant** for contact handles/numbers (FAB, email, phone) so they’re
> trivial to change and easy to mark as placeholders.

### 1.4 Root layout (complete)

```tsx
// app/layout.tsx
import type { Metadata } from "next";
import { Space_Grotesk, Inter, JetBrains_Mono } from "next/font/google";
import "./globals.css";
import { SmoothScroll } from "./components/providers/smooth-scroll";
import { SiteBackground } from "./components/chrome/site-background";
import { Cursor } from "./components/chrome/cursor";
import { ScrollProgress } from "./components/chrome/scroll-progress";
import { SiteHeader } from "./components/chrome/site-header";
import { SiteFooter } from "./components/chrome/site-footer";

const display = Space_Grotesk({
  subsets: ["latin"],
  variable: "--font-display",
});
const body = Inter({ subsets: ["latin"], variable: "--font-body" });
const mono = JetBrains_Mono({ subsets: ["latin"], variable: "--font-mono" });

export const metadata: Metadata = {
  metadataBase: new URL("https://example.com"),
  title: { default: "Site — tagline", template: "%s — Site" },
  description: "…",
};

const themeScript = `(function(){try{var t=localStorage.getItem("theme");
var m=window.matchMedia("(prefers-color-scheme: dark)").matches;
document.documentElement.setAttribute("data-theme",t||(m?"dark":"light"));}catch(e){}})();`;

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html
      lang="en"
      data-theme="dark"
      suppressHydrationWarning
      className={`${display.variable} ${body.variable} ${mono.variable}`}
    >
      <head>
        <script dangerouslySetInnerHTML={{ __html: themeScript }} />
      </head>
      <body className="grain relative flex min-h-screen flex-col bg-bg text-ink antialiased">
        <SiteBackground />
        <Cursor />
        <SmoothScroll>
          <ScrollProgress />
          <SiteHeader />
          <main className="relative z-[2] flex-1">{children}</main>
          <SiteFooter />
        </SmoothScroll>
      </body>
    </html>
  );
}
```

---

## 2. Design tokens & theming

### 2.1 Why tokens

A **token** is a named design decision (`--accent`, `--surface`, `--space-6`). Tokens give you
theming, consistency, and a single place to tune. Use **two layers**: _primitive_ values
(`--blue-600: #1f5fbf`) and _semantic_ aliases (`--accent: var(--blue-600)`). Components only
ever reference **semantic** tokens.

### 2.2 Theming model (`data-theme`, no flash)

Set theme **before first paint** with an inline `<head>` script (see §1.4), flip a
`data-theme` attribute on `<html>`. The toggle flips the attribute; the icon is swapped purely
in CSS so there’s **no React state and no hydration mismatch**:

```tsx
// theme-toggle.tsx ("use client")
export function ThemeToggle() {
  function toggle() {
    const dark = document.documentElement.getAttribute("data-theme") === "dark";
    const next = dark ? "light" : "dark";
    document.documentElement.setAttribute("data-theme", next);
    try {
      localStorage.setItem("theme", next);
    } catch {}
  }
  return (
    <button
      onClick={toggle}
      aria-label="Toggle colour theme"
      className="relative h-10 w-10 overflow-hidden rounded-full border border-line"
    >
      <span className="theme-ico theme-ico--sun">{/* sun svg */}</span>
      <span className="theme-ico theme-ico--moon">{/* moon svg */}</span>
    </button>
  );
}
```

```css
.theme-ico {
  position: absolute;
  inset: 0;
  display: grid;
  place-items: center;
  transition:
    opacity 0.45s var(--ease-out-expo),
    transform 0.45s var(--ease-out-expo);
}
[data-theme="dark"] .theme-ico--sun {
  opacity: 0;
  transform: translateY(-140%) rotate(90deg);
}
[data-theme="light"] .theme-ico--moon {
  opacity: 0;
  transform: translateY(140%) rotate(-90deg);
}
```

### 2.3 Complete `globals.css` token set

```css
@import "tailwindcss";

/* ---------- fonts ---------- */
:root {
  --font-display: var(--font-display);
  --font-body: var(--font-body);
  --font-mono: var(--font-mono);
}

/* ---------- LIGHT (warm paper) ---------- */
:root,
[data-theme="light"] {
  color-scheme: light;
  --bg: #f4f3ef;
  --bg-2: #eceae4;
  --surface: #fbfaf8;
  --surface-2: #f3f1ec;
  --paper: #fff;
  --ink: #15161a;
  --ink-2: #3c3e46;
  --ink-3: #6b6e78;
  --ink-4: #9a9da6;
  --line: rgba(20, 22, 26, 0.1);
  --line-2: rgba(20, 22, 26, 0.16);
  --accent: #1f5fbf;
  --accent-2: #2f74d8;
  --on-accent: #fff;
  --gold: #a9761f;
  --success: #16794d;
  --warning: #9a6a12;
  --danger: #b3402f;
  --glow: rgba(31, 95, 191, 0.18);
  --shadow-sm:
    0 1px 2px rgba(20, 22, 26, 0.05), 0 2px 8px -4px rgba(20, 22, 26, 0.1);
  --shadow-md: 0 12px 40px -18px rgba(20, 22, 26, 0.22);
  --shadow-lg: 0 30px 70px -30px rgba(20, 22, 26, 0.34);
  --grain: 0.045;
}
/* ---------- DARK (soft slate — never #000) ---------- */
[data-theme="dark"] {
  color-scheme: dark;
  --bg: #161922;
  --bg-2: #1b1f29;
  --surface: #20242f;
  --surface-2: #282d3a;
  --paper: #232834;
  --ink: #eef1f6;
  --ink-2: #c5cad6;
  --ink-3: #979eb1;
  --ink-4: #6e7589;
  --line: rgba(228, 233, 244, 0.12);
  --line-2: rgba(228, 233, 244, 0.21);
  --accent: #74a6f2;
  --accent-2: #86b2f5;
  --on-accent: #0a1322;
  --gold: #e6c27d;
  --success: #5cc99a;
  --warning: #e3bd74;
  --danger: #f08a7a;
  --glow: rgba(116, 166, 242, 0.2);
  --shadow-md: 0 18px 48px -22px rgba(0, 0, 0, 0.55);
  --shadow-lg: 0 36px 90px -34px rgba(0, 0, 0, 0.7);
  --grain: 0.035;
}

/* ---------- bridge to Tailwind v4 utilities ---------- */
@theme inline {
  --color-bg: var(--bg);
  --color-bg-2: var(--bg-2);
  --color-surface: var(--surface);
  --color-surface-2: var(--surface-2);
  --color-paper: var(--paper);
  --color-ink: var(--ink);
  --color-ink-2: var(--ink-2);
  --color-ink-3: var(--ink-3);
  --color-ink-4: var(--ink-4);
  --color-line: var(--line);
  --color-line-2: var(--line-2);
  --color-accent: var(--accent);
  --color-accent-2: var(--accent-2);
  --color-on-accent: var(--on-accent);
  --color-gold: var(--gold);
  --color-success: var(--success);
  --color-warning: var(--warning);
  --color-danger: var(--danger);
  --font-sans: var(--font-body);
  --font-display: var(--font-display);
  --font-mono: var(--font-mono);
  --radius-card: 16px;
  --radius-xl2: 20px;
  --radius-xl3: 28px;
  --shadow-soft: var(--shadow-md);
  --shadow-elevated: var(--shadow-lg);
  --ease-out-expo: cubic-bezier(0.16, 0.84, 0.3, 1);
  --ease-in-out-soft: cubic-bezier(0.65, 0, 0.35, 1);
  --animate-marquee: marquee 40s linear infinite;
  --animate-aurora: aurora 18s ease-in-out infinite;
  --animate-float: float 8s ease-in-out infinite;
}

/* ---------- base layer (MUST be layered or it overrides utilities) ---------- */
@layer base {
  * {
    box-sizing: border-box;
  }
  html {
    -webkit-text-size-adjust: 100%;
    scrollbar-gutter: stable;
  }
  body {
    margin: 0;
    font-family: var(--font-body), system-ui, sans-serif;
    line-height: 1.6;
    -webkit-font-smoothing: antialiased;
    text-rendering: optimizeLegibility;
    background: var(--bg);
    color: var(--ink);
    transition:
      background-color 0.5s var(--ease-out-expo),
      color 0.5s var(--ease-out-expo);
  }
  h1,
  h2,
  h3,
  h4,
  .font-display {
    font-family: var(--font-display), sans-serif;
    letter-spacing: -0.03em;
    font-weight: 600;
  }
  a {
    color: inherit;
    text-decoration: none;
  }
  :focus-visible {
    outline: 2px solid var(--accent);
    outline-offset: 3px;
    border-radius: 4px;
  }
  ::selection {
    background: var(--accent);
    color: var(--on-accent);
  }
}
```

> ⚠️ **Tailwind v4 traps:** (1) base resets written _outside_ `@layer base` are unlayered and
> **beat all utilities** (a bare `a{color:inherit}` kills every `text-*` on links). (2) Don’t
> name a token `bg` — it collides with the `bg-` namespace; use `surface`/`canvas`/`paper`.
> (3) Verify your custom `--color-*` actually generate utilities by checking the rendered CSS.

### 2.4 Semantic naming conventions

- **Surfaces:** `bg` (page) → `bg-2` → `surface` (card) → `surface-2` (raised/menu) → `paper`.
- **Text:** `ink` (primary) → `ink-2` → `ink-3` (muted/lede) → `ink-4` (faint/labels).
- **Lines:** `line` (hairline) → `line-2` (stronger/hover).
- **Intent:** `accent`, `gold`, `success`, `warning`, `danger` + `on-accent` for text on accent.
- Menus/dropdowns must use a surface **lighter than the page** (`surface-2`) + a visible border
  so they don’t vanish in dark mode.

---

## 3. Color, type, spacing & scales

### 3.1 Building a palette (theory)

- **One accent + neutral ramp (8–10 steps) + one secondary** (warm counter‑accent). Tint
  neutrals slightly toward the brand hue for cohesion.
- **Accent is rare**: links, focus rings, the single primary CTA per view, active states.
- **Dark mode = soft slate**, not `#000` (halation/eye‑strain; worse for 40+ & OLED). Lift
  muted text one step in dark for readability.
- **Prefer OKLCH** for perceptually even ramps and easy lightness control:
  `--accent: oklch(0.62 0.15 255);`. Tools below generate consistent steps.
- **Contrast targets:** body text **AA 4.5:1**, large text/UI **3:1**. Test _both_ themes.

Tools: [Radix Colors](https://www.radix-ui.com/colors) ·
[Open Color](https://yeun.github.io/open-color/) ·
[Realtime Colors](https://www.realtimecolors.com) · [Coolors](https://coolors.co) ·
[Huemint](https://huemint.com) · [Leonardo](https://leonardocolor.io) ·
[OKLCH picker](https://oklch.com) ·
[Contrast checker](https://webaim.org/resources/contrastchecker/)

### 3.2 Typography

Pair a **distinct display** + **neutral body** + **mono** for labels. Optionally an editorial
**serif italic** for a single accent word (use sparingly — many audiences read thin italics as
"weak"; when in doubt, use solid bold accent color instead).

```ts
import { Space_Grotesk, Inter, JetBrains_Mono } from "next/font/google";
export const display = Space_Grotesk({
  subsets: ["latin"],
  variable: "--font-display",
  weight: ["400", "500", "600", "700"],
});
export const body = Inter({ subsets: ["latin"], variable: "--font-body" });
export const mono = JetBrains_Mono({
  subsets: ["latin"],
  variable: "--font-mono",
  weight: ["400", "500"],
});
```

**Fluid type scale** with `clamp(min, preferred, max)`:

| Role    | Tailwind / CSS                                                 | Approx  |
| ------- | -------------------------------------------------------------- | ------- |
| Hero    | `text-[clamp(2.6rem,6.4vw,4.6rem)]`                            | 42→74px |
| H2      | `text-[clamp(1.9rem,4vw,3rem)]`                                | 30→48px |
| H3      | `text-[1.3rem]`                                                | 21px    |
| Lede    | `text-[1.05–1.1rem] text-ink-3`                                | 17–18px |
| Body    | `text-[0.95–1rem]`                                             | 15–16px |
| Eyebrow | `font-mono text-[.72rem] uppercase tracking-[.2em] text-ink-3` | 11.5px  |

Rules: tighten display tracking (`-.02–.03em`); body line‑height 1.5–1.65; measure 60–75ch for
prose; never justify; use `text-balance` on headings, `text-pretty` on paragraphs.

Sources: [Google Fonts](https://fonts.google.com) · [Fontshare](https://www.fontshare.com) ·
[Fontjoy pairings](https://fontjoy.com).

### 3.3 Spacing, radius, shadow, z‑index scales

- **Spacing:** 4px base (`0,1,2,3,4,6,8,12,16,24,32`). Section padding `py-24 → py-32`; card
  padding `p-6 → p-10`; element gaps `gap-3 → gap-5`.
- **Radius:** inputs/buttons `rounded-full` or `rounded-xl`; cards `16–28px`; pills `999px`.
- **Shadow:** long, soft, low‑opacity (`0 18px 48px -22px rgba(0,0,0,.55)`); avoid harsh
  near‑black drop shadows; in dark mode shadows are subtle — separation comes from surface steps.
- **z‑index scale (document it):** background `-10` · grain `1` · content `2` · sticky bits `30`
  · header `50` · scroll progress `70` · cursor `100` · FAB `40` · toasts `90` · modal `100+`.

---

## 4. Modern CSS techniques

Use these instead of JS where possible — they’re faster and resilient.

- **Fluid sizing:** `clamp()`, `min()`, `max()` for type, spacing, and widths.
- **Container queries:** style a component by _its own_ width, not the viewport —
  `@container (min-width:480px){…}` with a parent `container-type:inline-size`. Perfect for
  cards reused in different columns.
- **`:has()`** (parent/relational selector): `.card:has(img){…}`, form states
  `.field:has(:invalid){…}`, theme‑less toggles.
- **`:is()` / `:where()`**: group selectors; `:where()` has **0 specificity** (great for resets).
- **`color-mix()`**: derive tints/translucency from tokens —
  `background:color-mix(in srgb,var(--surface) 80%,transparent)` (glass).
- **Scroll‑driven animations (native):** `animation-timeline:scroll()` / `view()` for
  reveal/parallax with **zero JS** (progressive‑enhance; check support).
- **`@property`**: register custom props so gradients/angles can animate —
  `@property --angle{syntax:"<angle>";inherits:false;initial-value:0deg}`.
- **`mask` / `mix-blend-mode`**: spotlight borders, gradient text fades, blended cursors.
- **`backdrop-filter`**: glass headers/menus (use sparingly over animated canvases — costly).
- **Logical properties:** `padding-inline`, `margin-block`, `inset` for RTL/i18n‑safe layout.
- **`aspect-ratio`**: stable media boxes (`aspect-video`, `aspect-[4/3]`) — no CLS.
- **`accent-color`**: theme native checkboxes/radios/range cheaply.
- **`subgrid`**: align nested grids to a parent track (card rows with equal sections).
- **`text-wrap:balance|pretty`**: better headline/paragraph wrapping.

Example glass + grain + grid:

```css
.glass {
  background: color-mix(in srgb, var(--bg) 68%, transparent);
  backdrop-filter: blur(16px) saturate(160%);
  -webkit-backdrop-filter: blur(16px) saturate(160%);
}
.bg-grid-fade {
  background-image:
    linear-gradient(var(--line) 1px, transparent 1px),
    linear-gradient(90deg, var(--line) 1px, transparent 1px);
  background-size: 64px 64px;
  mask-image: radial-gradient(ellipse at center, #000, transparent 75%);
}
.grain::after {
  content: "";
  position: fixed;
  inset: -50%;
  z-index: 1;
  pointer-events: none;
  opacity: var(--grain);
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.85' numOctaves='3'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
}
```

Useful keyframes:

```css
@keyframes marquee {
  to {
    transform: translateX(-50%);
  }
}
@keyframes float {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-16px);
  }
}
@keyframes aurora {
  0%,
  100% {
    transform: translate3d(0, 0, 0) scale(1);
  }
  33% {
    transform: translate3d(4%, -3%, 0) scale(1.1);
  }
  66% {
    transform: translate3d(-3%, 2%, 0) scale(0.95);
  }
}
@keyframes pulseRing {
  0% {
    transform: scale(0.8);
    opacity: 0.55;
  }
  100% {
    transform: scale(2.4);
    opacity: 0;
  }
}
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.001ms !important;
    transition-duration: 0.001ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## 5. Motion engineering

### 5.1 Mental model

Motion exposes **MotionValues** (numbers that update outside React render) and **components**
(`motion.div`) that read them. Prefer driving animation with MotionValues + springs/scroll over
React state to avoid re‑renders. Reserve React state for discrete UI (open/closed, active tab).

### 5.2 Hooks reference

| Hook / API                                     | Purpose                        | Example                                    |
| ---------------------------------------------- | ------------------------------ | ------------------------------------------ |
| `motion.<tag>`                                 | animatable element             | `<motion.div animate={{x:10}}/>`           |
| `initial/animate/exit`                         | enter/leave                    | with `AnimatePresence`                     |
| `whileHover/whileTap/whileFocus/whileInView`   | gesture/viewport states        | hover lift, reveal                         |
| `variants` + `staggerChildren`/`delayChildren` | orchestration                  | staggered grids/lists                      |
| `transition`                                   | spring/tween/keyframes         | `{type:"spring",stiffness:300,damping:30}` |
| `useMotionValue` / `useMotionValueEvent`       | imperative value + listener    | cursor pos, scroll dir                     |
| `useSpring`                                    | smooth follow of a value       | trailing cursor, progress                  |
| `useScroll`                                    | scroll progress (page/element) | reveals, parallax, progress bar            |
| `useTransform`                                 | map ranges (+clamp)            | `[0,1]→[0,360]`                            |
| `useVelocity`                                  | speed of a value               | scroll‑reactive marquee                    |
| `useAnimationFrame`                            | per‑frame loop                 | marquee, canvas sync                       |
| `useInView`                                    | boolean in‑view (ref)          | counters, lazy effects                     |
| `layout` / `layoutId`                          | auto + shared‑element layout   | tab indicator, list reorder                |
| `AnimatePresence`                              | exit anims, swaps              | modals, tabs, route/page                   |
| `MotionConfig`                                 | global config                  | `reducedMotion="user"`                     |
| `useReducedMotion`                             | respect setting                | static fallbacks                           |

### 5.3 Springs vs tweens

- **Spring** = physical, interruptible, natural for anything that follows input (cursor, drag,
  layout). Tune `stiffness` (snappiness), `damping` (settle), `mass` (weight). Higher
  stiffness + higher damping = tight & quick; lower = floaty.
- **Tween** = fixed `duration` + `ease`; use for choreographed reveals where timing matters.
- **Signature easing:** `cubic-bezier(0.16,0.84,0.3,1)` (fast‑out, soft land). Pick one, reuse it.
- **Durations:** micro 150–300ms · reveals 600–900ms · ambient loops 6–40s · **stagger** 40–90ms.

### 5.4 Orchestration (variants + stagger)

```tsx
const container = {
  hidden: {},
  visible: { transition: { staggerChildren: 0.09, delayChildren: 0.05 } },
};
const item = {
  hidden: { opacity: 0, y: 22, filter: "blur(6px)" },
  visible: {
    opacity: 1,
    y: 0,
    filter: "blur(0px)",
    transition: { duration: 0.7, ease: [0.16, 0.84, 0.3, 1] },
  },
};
<motion.ul
  variants={container}
  initial="hidden"
  whileInView="visible"
  viewport={{ once: true }}
>
  {items.map((i) => (
    <motion.li key={i} variants={item} />
  ))}
</motion.ul>;
```

### 5.5 Scroll engineering

```tsx
// element parallax
const ref = useRef(null);
const { scrollYProgress } = useScroll({
  target: ref,
  offset: ["start end", "end start"],
});
const y = useSpring(useTransform(scrollYProgress, [0, 1], [80, -80]), {
  stiffness: 120,
  damping: 30,
});
// scroll direction (hide-on-scroll header)
const { scrollY } = useScroll();
const last = useRef(0);
useMotionValueEvent(scrollY, "change", (v) => {
  setHidden(v > last.current && v > 260);
  last.current = v;
});
```

> With Lenis, `useScroll` keeps working because Lenis drives native scroll. Don’t also set CSS
> `scroll-behavior:smooth` (Lenis handles it).

### 5.6 Layout & shared‑element

`layout` animates an element when its size/position changes; `layoutId` morphs between two
different elements (e.g., a moving active‑tab/nav underline). Wrap conditionally rendered
trees in `AnimatePresence` for exit animations; use `mode="wait"`/`"popLayout"` as needed.

### 5.7 Global reduced motion (do once)

```tsx
// smooth-scroll.tsx
import { MotionConfig, useReducedMotion } from "motion/react";
import { ReactLenis } from "lenis/react";
export function SmoothScroll({ children }: { children: React.ReactNode }) {
  const reduce = useReducedMotion();
  return (
    <MotionConfig reducedMotion="user">
      <ReactLenis
        root
        options={{ lerp: reduce ? 1 : 0.09, smoothWheel: !reduce }}
      >
        {children}
      </ReactLenis>
    </MotionConfig>
  );
}
```

> ⚠️ **Never hide content behind a transform‑only reveal.** Under reduced motion Motion won’t
> run the transform and the element stays where `initial` put it (e.g. `y:"115%"` → invisible).
> Provide a static fallback (`useReducedMotion()` → render plain) for anything that _contains_
> content (headlines!). Decorative‑only motion can simply stop.

---

## 6. Website categories playbook

Pick one. Each lists **goal, blueprint, components, motion level, libraries**.

### 6.1 Portfolio / Agency / Marketing _(this project’s type)_

- **Goal:** story + trust → one primary action (contact/quote).
- **Blueprint:** hide‑on‑scroll nav → animated hero (word reveal + signature visual) → trust
  strip / market marquee → "what we do" pillars → work/case highlights w/ imagery → why‑us
  bento → stats counters → process → testimonial/quote → CTA band → rich footer. Add a
  **FAB** for quick contact.
- **Motion:** _high_ — ambient bg, parallax, reveals, magnetic CTAs, spotlight cards, cursor.
- **Libs:** Motion, Lenis, [Aceternity](https://ui.aceternity.com), [Magic UI](https://magicui.design),
  [React Bits](https://reactbits.dev), Lucide, Unsplash imagery.

### 6.2 Dashboard / Admin / Analytics

- **Goal:** dense info, fast scanning, task completion. **Restrained, functional motion only.**
- **Blueprint:** collapsible **sidebar/rail** → top bar (search, ⌘K, theme, avatar) → **KPI/stat
  card** row → **charts** grid → **data table** (sort/filter/paginate/select) → detail
  drawers/modals. Bento grid + resizable panels; sticky table headers; skeleton loaders.
- **Welcome motion:** count‑up KPIs, chart draw‑in, row enter/exit (`AnimatePresence`+`layout`),
  drawer slide, sidebar collapse, `layoutId` tab indicator, toasts. Keep ≤300ms. **No
  decorative parallax/particles/cursor.**

| Need                          | Pick                                                                                                                                                                                                                                                  | Link                                                                                 |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Charts (copy‑paste, Recharts) | **shadcn Charts**                                                                                                                                                                                                                                     | <https://ui.shadcn.com/charts> · [docs](https://ui.shadcn.com/docs/components/chart) |
| Charts (React)                | **Recharts**                                                                                                                                                                                                                                          | <https://recharts.org>                                                               |
| KPI cards / chart kit         | **Tremor**                                                                                                                                                                                                                                            | <https://tremor.so>                                                                  |
| Rich charts                   | **Nivo** <https://nivo.rocks> · **visx** <https://airbnb.io/visx> · **ECharts** <https://echarts.apache.org> · **ApexCharts** <https://apexcharts.com> · **Chart.js** <https://www.chartjs.org> · **Observable Plot** <https://observablehq.com/plot> |                                                                                      |
| Headless table                | **TanStack Table**                                                                                                                                                                                                                                    | <https://tanstack.com/table>                                                         |
| Ready data table              | **shadcn Data Table**                                                                                                                                                                                                                                 | <https://ui.shadcn.com/docs/components/data-table>                                   |
| Enterprise grid               | **AG Grid** <https://www.ag-grid.com> · **MUI X Data Grid** <https://mui.com/x/react-data-grid> · **Material React Table** <https://www.material-react-table.com> · **Glide Data Grid** <https://grid.glideapps.com>                                  |                                                                                      |
| Virtualize rows               | **TanStack Virtual**                                                                                                                                                                                                                                  | <https://tanstack.com/virtual>                                                       |
| Data fetching                 | **TanStack Query**                                                                                                                                                                                                                                    | <https://tanstack.com/query>                                                         |
| Resizable panels              | **react-resizable-panels**                                                                                                                                                                                                                            | <https://github.com/bvaughn/react-resizable-panels>                                  |
| Drag & drop / kanban          | **dnd-kit**                                                                                                                                                                                                                                           | <https://dndkit.com>                                                                 |
| Command palette ⌘K            | **cmdk**                                                                                                                                                                                                                                              | <https://cmdk.paco.me>                                                               |
| Date range                    | **react-day-picker**                                                                                                                                                                                                                                  | <https://daypicker.dev>                                                              |
| Admin framework               | **Refine** <https://refine.dev> · **React Admin** <https://marmelab.com/react-admin>                                                                                                                                                                  |                                                                                      |
| Component systems             | **shadcn/ui** <https://ui.shadcn.com> · **Mantine** <https://mantine.dev> · **MUI** <https://mui.com> · **Ant Design** <https://ant.design> · **Chakra** <https://chakra-ui.com>                                                                      |                                                                                      |
| Tailwind admin blocks         | **Tailwind Plus** <https://tailwindcss.com/plus> · **Preline** <https://preline.co> · **Flowbite** <https://flowbite.com> · **Tremor blocks**                                                                                                         |                                                                                      |

### 6.3 SaaS landing / product page

- **Blueprint:** nav → hero w/ product shot/loop → social‑proof logos → feature splits
  (alternating) → interactive/bento features → pricing table → testimonials → FAQ (accordion) →
  CTA → footer. **Motion:** medium — scroll reveals, product loop (Lottie/Rive), hover.
- **Libs:** [Vaul](https://vaul.emilkowal.ski), [Sonner](https://sonner.emilkowal.ski),
  Aceternity/Magic UI hero effects, Recharts mini‑charts.

### 6.4 E‑commerce / storefront

- **Blueprint:** announcement bar → nav w/ mega‑menu + cart → hero/collection → product grid
  (filters, quick‑view) → PDP (gallery, variants, sticky add‑to‑cart) → **cart drawer** →
  checkout. **Motion:** subtle — hover lift, add‑to‑cart fly, drawer slide.
- **Libs:** [Embla](https://www.embla-carousel.com) galleries, Vaul cart drawer, Sonner toasts,
  skeletons. Platforms: Shopify Hydrogen, Medusa.

### 6.5 Blog / Editorial / Docs

- **Blueprint:** reading column ~65ch, strong type scale, TOC, anchored headings, code blocks
  (Shiki), MDX. Reading‑progress bar; search ([cmdk]/Algolia DocSearch). **Motion:** minimal —
  reveal, progress, view‑transition between articles.

### 6.6 Local business / restaurant / services

- **Blueprint:** hero w/ strong photo + clear value + **call/book FAB** → services/menu →
  gallery → reviews → map + hours → contact form. **Motion:** light. Emphasize **NAP**
  (name/address/phone), `LocalBusiness` JSON‑LD, click‑to‑call.

### 6.7 Event / launch / one‑pager

- **Blueprint:** countdown hero → agenda → speakers/lineup → tickets/CTA → venue → FAQ.
  **Motion:** medium‑high; countdown, sticky ticket CTA.

### 6.8 Personal / résumé

- **Blueprint:** intro hero → selected work → about → experience timeline → contact. **Motion:**
  tasteful reveals; keep fast and accessible.

---

## 7. Pattern catalog — “add X”

Map any request to a reusable item. Implement the pattern, not a one‑off.

### 7.1 Nav + dropdown

Fixed bar; **hide on scroll‑down / show on scroll‑up**; glass on scroll; **animated active
underline** via shared `layoutId`; hover **dropdowns** (`AnimatePresence`) where the parent
label _still links_ to its default page; full‑screen mobile overlay with staggered links.
Dropdown panel uses `surface-2` + `border-line-2` + elevated shadow (visible on dark).

### 7.2 Hero

Fluid headline (**word‑mask reveal**), sub‑copy, 1–2 CTAs (primary **magnetic**), and a
**signature visual** (animated SVG, canvas particle field, 3D tilt, Lottie/Rive). Optional
scroll cue. Keep one accent.

### 7.3 Section reveal & stagger

`whileInView` fade+rise(+de‑blur) once; groups via parent `variants` + `staggerChildren`.

### 7.4 Cards (feature / bento / pricing)

`SpotlightCard` (cursor‑follow border glow) + hover lift + optional image header + glass.
Compose a **bento** (mixed col/row spans) for rhythm. Pricing: highlight one plan, monthly/annual
toggle (`layoutId` switch).

### 7.5 Marquee / logo ticker

Pure‑CSS infinite track (duplicate content, translateX −50%), or **velocity marquee** (speed
reacts to scroll via `useVelocity`). Pause on hover; mask edges.

### 7.6 Tabs / segmented control

`AnimatePresence` panel swap + `layoutId` moving indicator. Roving‑tabindex for a11y.

### 7.7 Accordion / FAQ

Native `<details>`/`<summary>` (free a11y) or Radix Accordion; animate height with `grid-rows`
trick or Motion. Plus icon rotates.

### 7.8 Forms (basic)

Validate on submit; inline errors; success state swapped with `AnimatePresence mode="wait"`.
Long option lists → **searchable combobox** (§7.9). See §14 for validation depth.

### 7.9 Searchable select / combobox

Open a panel, **filter options live** as you type, store the value in a hidden input for native
form submit, close on outside click; or use [Radix](https://www.radix-ui.com)/
[React Aria](https://react-spectrum.adobe.com/react-aria) for full keyboard/ARIA.

### 7.10 Stats / KPI counters

Count‑up with eased `requestAnimationFrame` when in view; pair with label + trend chip.

### 7.11 Overlays — modal / drawer / sheet / popover / tooltip

Use accessible primitives: [Radix Dialog/Popover/Tooltip](https://www.radix-ui.com),
[Vaul](https://vaul.emilkowal.ski) drawer. Modal: scale+fade; sheet: slide; **trap focus**,
restore focus on close, close on `Esc`/overlay, lock body scroll (or `lenis.stop()`).

### 7.12 Feedback — toasts / skeletons / progress

[Sonner](https://sonner.emilkowal.ski) toasts; **skeletons** for loading; inline spinners for
short waits; optimistic UI for actions.

### 7.13 Floating Action Button (contact / chat / quick action)

**Use whenever a request asks for a persistent contact or quick action** (chat apps, "message
us", "book now", "call", "back to top"). Fixed bottom‑right pill/circle linking to a channel,
with a ping ring + hover scale. **Reliable, reusable method — reuse it.**

```tsx
export function Fab({
  href,
  label,
  children,
}: {
  href: string;
  label: string;
  children: React.ReactNode;
}) {
  return (
    <a
      href={href}
      target="_blank"
      rel="noopener noreferrer"
      aria-label={label}
      className="group fixed bottom-5 right-5 z-40 inline-flex items-center gap-2.5 rounded-full
                 py-3.5 pl-4 pr-5 font-medium text-white shadow-lg transition-transform
                 duration-300 hover:scale-105"
    >
      <span className="relative grid place-items-center">
        <span className="absolute h-full w-full animate-ping rounded-full bg-white/40" />
        <span className="relative">{children /* icon */}</span>
      </span>
      <span className="hidden sm:inline">{label}</span>
    </a>
  );
}
```

**Channel `href` formats:** chat → `https://wa.me/<intl-number>?text=<encoded>` (WhatsApp),
`https://m.me/<page>` (Messenger), `https://t.me/<user>` (Telegram); email → `mailto:`;
phone → `tel:+…`. Keep the handle/number in **one config constant**; mark placeholders.

### 7.14 Command palette (⌘K)

[cmdk](https://cmdk.paco.me) for quick nav/actions — great on dashboards & docs.

### 7.15 Carousel / gallery

[Embla](https://www.embla-carousel.com) (lightweight, accessible). Avoid auto‑rotating heroes.

### 7.16 Data table

**TanStack Table** (headless engine) + your markup, or **shadcn Data Table**. Add
sort/filter/pagination/selection; virtualize big sets (TanStack Virtual); URL‑driven state.

### 7.17 Charts

**shadcn Charts**/**Recharts** or **Tremor**; animate draw‑in; theme via CSS vars; always
provide accessible summaries/tables for key data.

### 7.18 Cursor & ambient (portfolio/landing only)

Blended trailing cursor (`mix-blend-difference`, fine‑pointer only), particle‑network canvas,
aurora blobs, scroll progress. **Skip on dashboards.**

### 7.19 Pagination / load‑more / infinite scroll

Prefer pagination/load‑more for control + a11y; infinite scroll only for feeds (keep a footer
reachable). Animate new items with `AnimatePresence`+`layout`.

### 7.20 Empty / error / 404 states

Design them: friendly copy, an illustration/icon, and a clear next action. Don’t leave blanks.

---

## 8. Component animation reference

Concrete implementations behind §7 — copy and adapt.

- **Particle‑network background** _(portfolio)_ — canvas; ~90 drifting dots connecting to
  neighbours + the cursor; theme‑aware colours (read `--accent`/`--ink-3`); pauses on hidden
  tab; **static under reduced motion**; DPR clamped to 2; particle cap by area; O(n²) link
  check kept small; pointer pulls particles slightly.
- **Blended cursor** — ring `useSpring(x,{stiffness:380,damping:34})` + tight dot
  `useSpring(x,{stiffness:1100,damping:50})`, container `mix-blend-difference`; grows on hover
  of `a,button,[data-cursor]`; mounts **only on fine pointers** via `useSyncExternalStore`
  (no setState‑in‑effect).
- **Scroll progress** — `useScroll().scrollYProgress → useSpring → style={{scaleX}}` on a fixed
  `origin-left` bar.
- **Reveal** — `initial{opacity:0,y:26,filter:"blur(8px)"} → whileInView{…}`, `viewport.once`;
  reduced‑motion renders children plain.
- **Stagger / StaggerItem** — parent `variants` `staggerChildren:.09`; child `variants`.
- **Word reveal (headline)** — words wrapped in `overflow-hidden` spans; each inner
  `y:"115%"→0`; **orchestrated from one parent** (per‑word `whileInView` is flaky); **static
  fallback** under reduced motion so headlines never hide.
- **Parallax** — per‑element `useScroll({target,offset:["start end","end start"]}) →
useTransform → useSpring → y`.
- **Velocity marquee** — `useVelocity(scrollY)` + `useAnimationFrame` + `wrap` to advance an x
  MotionValue; direction flips with scroll sign.
- **FloatY** — `animate={{y:[-a,a,-a]}}` `repeat:Infinity`, `easeInOut`.
- **Magnetic** — on `mousemove`, set x/y to cursor delta × strength through `useSpring`; reset
  on leave.
- **Spotlight card** — write `--mx/--my` on `mousemove`; a masked `radial-gradient` paints a
  border glow + an inner highlight follows the pointer.
- **Counter** — eased `requestAnimationFrame` from 0→value on `useInView`; respects reduced motion.
- **Theme toggle** — sun/moon cross‑fade purely via `[data-theme]` CSS (no React state).
- **Header** — `useMotionValueEvent(scrollY)` → hide/scrolled booleans; `layoutId` underline;
  `AnimatePresence` dropdowns; glass when scrolled.
- **Hero visual** — SVG paths draw with `pathLength` (Motion), particles travel via SVG
  `animateMotion`, dashed rings rotate, whole group **tilts in 3D** (`rotateX/rotateY` springs)
  toward the cursor.
- **Searchable select** — open panel; live `filter()` on query; value in hidden input; outside‑
  click close (listener in effect, setState in callback — lint‑safe).
- **List filter** — `AnimatePresence`+`layout` so rows animate as filters/search change.
- **Forms** — `AnimatePresence mode="wait"` swap to a success card after validation passes.

---

## 9. Advanced animation recipes

| Pattern                    | Technique                                              | Category          |
| -------------------------- | ------------------------------------------------------ | ----------------- |
| Scroll reveal              | `whileInView` + `viewport.once`                        | all               |
| Staggered grid/list        | parent `variants` + `staggerChildren`                  | all               |
| Text mask reveal           | per‑word clip, parent‑orchestrated                     | portfolio/landing |
| Line‑by‑line reveal        | wrap lines, clip + `y` stagger                         | editorial         |
| Parallax (multi‑layer)     | several `useTransform` speeds per layer                | portfolio         |
| Velocity marquee           | `useVelocity` + `useAnimationFrame`                    | portfolio/landing |
| Magnetic button            | cursor delta → `useSpring`                             | portfolio/landing |
| Spotlight border           | `--mx/--my` + masked gradient                          | all               |
| 3D tilt card               | pointer → `rotateX/rotateY` springs + perspective      | portfolio         |
| Count‑up                   | eased rAF on in‑view                                   | all (KPIs)        |
| Shared element (tab/nav)   | `layoutId`                                             | all               |
| List reorder               | `layout` + `AnimatePresence`                           | all               |
| Modal/sheet                | scale+fade / slide + focus trap                        | all               |
| Chart draw‑in              | lib animation / `pathLength`                           | dashboard         |
| Sticky scroll storytelling | a _short_ sticky + scroll progress (use sparingly)     | landing           |
| Page transition            | `AnimatePresence` template **or** View Transitions API | all               |
| Cursor‑follow gradient     | track pointer → CSS var → radial‑gradient              | portfolio         |
| Gradient border anim       | `@property --angle` + `conic-gradient`                 | all               |
| Number/odometer roll       | translateY of stacked digits                           | dashboard         |
| Image reveal (clip)        | `clip-path` inset 100%→0 on view                       | portfolio         |

> **Avoid:** tall `position:sticky;height:200vh` pinned sections — they make the page feel like
> it “hangs” while scrolling. If you need a pinned beat, keep it short and obviously progressing.

---

## 10. Accessibility (deep)

- **Semantics first:** real landmarks (`header/nav/main/footer`), one `<h1>`/page, ordered
  headings, lists for lists, `<button>` for actions, `<a>` for navigation.
- **Keyboard:** everything operable; logical tab order; visible **`:focus-visible`** ring;
  `Esc` closes overlays; arrow keys for menus/tabs/listboxes (roving tabindex).
- **ARIA only when needed:** name interactive icons (`aria-label`); `aria-expanded`,
  `aria-current="page"`, `role="dialog"`+`aria-modal`; live regions (`aria-live`) for async
  status/toasts. Prefer native elements over ARIA reinvention.
- **Color & contrast:** AA 4.5:1 body / 3:1 large+UI; never rely on color alone (add icon/label);
  test both themes.
- **Motion:** honor `prefers-reduced-motion`; never trap users in motion; no >3 flashes/sec.
- **Forms:** `<label>` for every field; describe errors with `aria-describedby`; group with
  `<fieldset>/<legend>`; don’t disable submit silently.
- **Media:** meaningful `alt` (empty `alt=""` for decorative); captions/transcripts for AV;
  pause/controls for autoplay.
- **Targets:** ≥24–44px touch targets; spacing between them.
- Tools: [axe DevTools](https://www.deque.com/axe/devtools/) ·
  [WAVE](https://wave.webaim.org) · [Lighthouse](https://developer.chrome.com/docs/lighthouse) ·
  [WCAG](https://www.w3.org/WAI/standards-guidelines/wcag/) ·
  [APG patterns](https://www.w3.org/WAI/ARIA/apg/patterns/).

---

## 11. Performance (deep)

- **Animate only `transform` & `opacity`** (compositor‑friendly). Avoid animating
  width/height/top/left/box‑shadow/filter on large/often elements; prefer `transform:scale`,
  pseudo‑element shadows, or `will-change` _sparingly_.
- **Canvas/particles:** cap count by area, clamp `devicePixelRatio` to 2, pause on
  `visibilitychange`/off‑screen, throttle pointer, keep per‑frame work O(n) where possible.
- **`backdrop-filter`** is expensive over animated content — limit count/area.
- **Images:** `next/image` with explicit `sizes`/`fill`, modern formats (AVIF/WebP),
  `priority` only for LCP, lazy by default; set `images.remotePatterns`; verify remotes load.
- **Fonts:** `next/font` (self‑host, `display:swap`), subset, limit weights; avoid FOIT.
- **JS:** Server Components by default; `"use client"` only where needed; dynamic‑import heavy
  client libs (charts/editors/3D) with `next/dynamic`; code‑split routes.
- **Core Web Vitals:** LCP < 2.5s (optimize hero image/text, preconnect), CLS < 0.1 (reserve
  media with `aspect-ratio`/`width`/`height`), INP < 200ms (avoid long tasks; debounce; keep
  animations off the main thread).
- **Lenis:** fine for marketing; for app/dashboard scroll‑heavy grids, prefer native scroll +
  virtualization.
- Measure: [PageSpeed](https://pagespeed.web.dev) · [WebPageTest](https://www.webpagetest.org) ·
  [web.dev/vitals](https://web.dev/articles/vitals).

---

## 12. Responsive & fluid design

- **Mobile‑first**; layout with **CSS Grid** + **Flexbox**; prefer **container queries** for
  reusable components.
- **Fluid everything** via `clamp()` (type, spacing, gaps) so you need fewer breakpoints.
- **Tailwind breakpoints:** `sm 640 · md 768 · lg 1024 · xl 1280 · 2xl 1536`. Design the
  _content_ breakpoints, not device names.
- **Touch:** ≥44px targets, no hover‑only affordances (provide tap equivalents), bigger spacing.
- **Test:** 360px (small phone), 768 (tablet), 1280 (laptop), 1440+ (desktop). Check landscape
  and dynamic toolbars (use `dvh`/`svh`/`lvh` for full‑height heroes, not `vh`).

---

## 13. Images, media & icons

- **`next/image`** for raster: `fill` + `sizes` inside an `aspect-[…]` box; `priority` for LCP.
- **Art direction:** `<picture>`/source sets for very different crops; gradient overlay for text
  legibility on photos (`bg-linear-to-t from-black/45`).
- **SVG** for logos/icons/diagrams (crisp, animatable, themeable via `currentColor`/CSS vars).
- **Icons:** [Lucide](https://lucide.dev) / [Tabler](https://tabler.io/icons) /
  [Phosphor](https://phosphoricons.com) / [Iconify](https://icon-sets.iconify.design) — size 16–24,
  match stroke to text weight, give standalone icons `aria-label`.
- **Motion graphics:** [Lottie](https://lottiefiles.com) (JSON, lightweight) /
  [Rive](https://rive.app) (interactive state machines) for product loops/illustrations.
- **Stock:** [Unsplash](https://unsplash.com) / [Pexels](https://pexels.com) — pick consistent,
  on‑brand imagery; avoid obvious clip‑art. Verify each remote URL actually loads.

---

## 14. Forms & validation

- **Structure:** `<label>` + input pairs, logical grouping, clear required markers, helpful
  placeholders (not as labels), autocomplete tokens, correct `type`/`inputmode`.
- **Validation:** validate on submit (and on blur for long forms); inline, specific messages;
  describe errors via `aria-describedby`; never block silently. For schema validation use
  **Zod** (<https://zod.dev>); manage complex forms with **React Hook Form**
  (<https://react-hook-form.com>) or **TanStack Form** (<https://tanstack.com/form>); or native
  Server Actions + `useActionState`.
- **States:** idle → focus → filled → error → submitting (disable + spinner) → success (swap
  with `AnimatePresence`).
- **Long lists** (countries, job types): **searchable combobox**; add an "Any/None" option at top
  when relevant.
- **Spam:** honeypot field + rate limit; avoid intrusive CAPTCHAs when possible.

```tsx
function onSubmit(e) {
  e.preventDefault();
  const next = {};
  if (!name.trim()) next.name = "Enter your name.";
  if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email))
    next.email = "Valid email please.";
  setErrors(next);
  if (Object.keys(next).length === 0) {
    setSent(true);
  }
}
```

---

## 15. SEO, metadata & sharing

- **Per‑route `metadata`** (title template, description, keywords, canonical).
- **Open Graph + Twitter** cards; generate **OG images** with `next/og` (`ImageResponse`).
- **Structured data (JSON‑LD):** `Organization`, `LocalBusiness`, `BreadcrumbList`, `FAQPage`,
  `Product`, `JobPosting`, `Article` as relevant.
- **Sitemap & robots:** `app/sitemap.ts`, `app/robots.ts`.
- **Semantics + perf are SEO:** fast Core Web Vitals, real headings, descriptive links, alt text.
- Refs: [Next metadata](https://nextjs.org/docs/app/api-reference/functions/generate-metadata) ·
  [schema.org](https://schema.org) · [OG protocol](https://ogp.me).

---

## 16. Page & view transitions

- **Native View Transitions API** for element/route morphs (progressive‑enhance):
  <https://developer.mozilla.org/docs/Web/API/View_Transitions_API>. Next’s App Router supports
  view transitions patterns; tag shared elements with `view-transition-name`.
- **Motion route transitions:** `AnimatePresence` in a `template.tsx`, keyed by pathname; keep
  exits short (≤300ms) so navigation feels instant. Persisted chrome (header/footer) lives in
  `layout.tsx` so it doesn’t re‑mount.
- Respect reduced motion (cross‑fade or none).

---

## 17. Library directory

**Animation:** Motion <https://motion.dev> · GSAP <https://gsap.com> · anime.js
<https://animejs.com> · AutoAnimate <https://auto-animate.formkit.com> · Theatre.js
<https://www.theatrejs.com>

**Scroll:** Lenis <https://github.com/darkroomengineering/lenis> · ScrollTrigger
<https://gsap.com/docs/v3/Plugins/ScrollTrigger> · View Transitions API
<https://developer.mozilla.org/docs/Web/API/View_Transitions_API>

**Component kits / headless:** shadcn/ui <https://ui.shadcn.com> · Radix
<https://www.radix-ui.com> · React Aria <https://react-spectrum.adobe.com/react-aria> ·
Base UI <https://base-ui.com> · Headless UI <https://headlessui.com> · Ark UI
<https://ark-ui.com>

**Animated component galleries:** Aceternity <https://ui.aceternity.com> · Magic UI
<https://magicui.design> · React Bits <https://reactbits.dev> · Hover.dev
<https://www.hover.dev> · Cult UI <https://www.cult-ui.com> · Uiverse <https://uiverse.io>

**Design systems / kits:** Mantine <https://mantine.dev> · MUI <https://mui.com> · Ant Design
<https://ant.design> · Chakra <https://chakra-ui.com> · Tailwind Plus
<https://tailwindcss.com/plus> · Preline <https://preline.co> · Flowbite
<https://flowbite.com> · Untitled UI <https://www.untitledui.com> · Park UI
<https://park-ui.com>

**Dashboards / data:** TanStack Table <https://tanstack.com/table> · Query
<https://tanstack.com/query> · Virtual <https://tanstack.com/virtual> · Form
<https://tanstack.com/form> · AG Grid <https://www.ag-grid.com> · MUI X
<https://mui.com/x/react-data-grid> · Tremor <https://tremor.so> · shadcn Charts
<https://ui.shadcn.com/charts> · Recharts <https://recharts.org> · Nivo <https://nivo.rocks> ·
visx <https://airbnb.io/visx> · ECharts <https://echarts.apache.org> · ApexCharts
<https://apexcharts.com> · Chart.js <https://www.chartjs.org> · Observable Plot
<https://observablehq.com/plot> · Refine <https://refine.dev> · React Admin
<https://marmelab.com/react-admin>

**Overlays / utilities:** Vaul <https://vaul.emilkowal.ski> · Sonner
<https://sonner.emilkowal.ski> · cmdk <https://cmdk.paco.me> · Embla
<https://www.embla-carousel.com> · dnd-kit <https://dndkit.com> · react-resizable-panels
<https://github.com/bvaughn/react-resizable-panels> · react-day-picker <https://daypicker.dev> ·
Tippy/Floating UI <https://floating-ui.com>

**3D / canvas / particles:** three.js <https://threejs.org> · React Three Fiber
<https://docs.pmnd.rs/react-three-fiber> · drei <https://github.com/pmndrs/drei> · tsParticles
<https://particles.js.org> · cobe <https://github.com/shuding/cobe> · OGL
<https://github.com/oframe/ogl> · Spline <https://spline.design>

**Text / vector motion:** SplitType <https://github.com/lukePeavey/SplitType> · Splitting.js
<https://splitting.js.org> · Lottie <https://lottiefiles.com> · Rive <https://rive.app>

**Forms / validation:** Zod <https://zod.dev> · React Hook Form <https://react-hook-form.com> ·
TanStack Form <https://tanstack.com/form>

**Icons & media:** Lucide <https://lucide.dev> · Tabler <https://tabler.io/icons> · Phosphor
<https://phosphoricons.com> · Iconify <https://icon-sets.iconify.design> · unDraw
<https://undraw.co> · Unsplash <https://unsplash.com> · Pexels <https://pexels.com>

**Color & fonts:** Radix Colors <https://www.radix-ui.com/colors> · Open Color
<https://yeun.github.io/open-color/> · Realtime Colors <https://www.realtimecolors.com> ·
Coolors <https://coolors.co> · OKLCH <https://oklch.com> · Open Props
<https://open-props.style> · Google Fonts <https://fonts.google.com> · Fontshare
<https://www.fontshare.com>

**Inspiration:** Awwwards <https://www.awwwards.com> · Godly <https://godly.website> ·
Land‑book <https://land-book.com> · Refero <https://refero.design> · Mobbin
<https://mobbin.com> · SiteInspire <https://www.siteinspire.com> · Cosmos
<https://www.cosmos.so> · Httpster <https://httpster.net>

**Learning:** Josh W. Comeau <https://www.joshwcomeau.com> · web.dev <https://web.dev> · MDN
<https://developer.mozilla.org> · Motion examples <https://motion.dev/examples> · Codrops
<https://tympanus.net/codrops> · Smashing <https://www.smashingmagazine.com>

---

## 18. Checklists

**Design**

- [ ] One accent + neutral ramp (tinted) + one secondary; AA in both themes
- [ ] Soft‑slate dark (no `#000`); muted text lifted in dark
- [ ] Display + body + mono; fluid `clamp()` headings; `text-balance`
- [ ] 4px spacing scale; generous section padding; documented radius/shadow/z scales
- [ ] Bento/asymmetric layout; one primary CTA per view

**Motion**

- [ ] One signature easing; layered ambient/scroll/interaction
- [ ] `prefers-reduced-motion` fallbacks; content never hidden behind transform‑only reveals
- [ ] Only `transform`/`opacity` animated on hot paths
- [ ] No tall sticky‑pin "hang" sections

**Build**

- [ ] Server Components default; `"use client"` only where needed; dynamic‑import heavy libs
- [ ] `next/image` (+`sizes`, remotePatterns) verified loading; fonts via `next/font`
- [ ] Per‑route `metadata`, OG image, JSON‑LD, sitemap/robots
- [ ] Keyboard + focus‑visible + ARIA; semantic landmarks; labelled forms
- [ ] Core Web Vitals green; build + lint clean

---

## 19. Do / Don’t & anti‑patterns

**Do**

- ✅ Base resets in `@layer base`; tokens for everything; semantic aliases only in components.
- ✅ Soft‑dark theme; dropdowns on `surface-2` + visible border so they’re seen in dark.
- ✅ Orchestrate text reveals from a parent; ship reduced‑motion fallbacks.
- ✅ Theme via CSS‑driven attribute (no hydration mismatch). Contact handles in one config.
- ✅ Verify remote images load; reserve media boxes to avoid CLS.
- ✅ Match motion _amount_ to category (high for portfolio, restrained for dashboard).

**Don’t / anti‑patterns**

- ❌ Pure‑black dark mode; naming a color token `bg`; unlayered global resets.
- ❌ Hiding content behind transform‑only reveals (invisible under reduced motion).
- ❌ Tall `position:sticky` pinned scroll sections that feel like the page "hangs."
- ❌ Decorative parallax/cursor/particles on a dashboard.
- ❌ Auto‑rotating hero carousels; modal on load; infinite scroll without a reachable footer.
- ❌ Animating layout props (width/top) on scroll; over‑using `backdrop-blur` over canvases.
- ❌ Icon‑only buttons without labels; color‑only status; placeholder‑as‑label.
- ❌ Five typefaces, ten accent colors, gradients on everything — restraint reads as premium.

---

## 20. Appendix: copy‑paste components

### 20.1 Reveal + Stagger

```tsx
"use client";
import { motion, useReducedMotion, type Variants } from "motion/react";
const EASE = [0.16, 0.84, 0.3, 1] as const,
  VP = { once: true, margin: "0px 0px -12% 0px" } as const;
export function Reveal({
  children,
  delay = 0,
  className = "",
}: {
  children: React.ReactNode;
  delay?: number;
  className?: string;
}) {
  const r = useReducedMotion();
  if (r) return <div className={className}>{children}</div>;
  return (
    <motion.div
      className={className}
      initial={{ opacity: 0, y: 26, filter: "blur(8px)" }}
      whileInView={{ opacity: 1, y: 0, filter: "blur(0px)" }}
      viewport={VP}
      transition={{ duration: 0.8, ease: EASE, delay }}
    >
      {children}
    </motion.div>
  );
}
const c: Variants = {
  hidden: {},
  visible: { transition: { staggerChildren: 0.09 } },
};
const i: Variants = {
  hidden: { opacity: 0, y: 22, filter: "blur(6px)" },
  visible: {
    opacity: 1,
    y: 0,
    filter: "blur(0px)",
    transition: { duration: 0.7, ease: EASE },
  },
};
export function Stagger({
  children,
  className = "",
}: {
  children: React.ReactNode;
  className?: string;
}) {
  const r = useReducedMotion();
  if (r) return <div className={className}>{children}</div>;
  return (
    <motion.div
      className={className}
      variants={c}
      initial="hidden"
      whileInView="visible"
      viewport={VP}
    >
      {children}
    </motion.div>
  );
}
export function StaggerItem({
  children,
  className = "",
}: {
  children: React.ReactNode;
  className?: string;
}) {
  const r = useReducedMotion();
  if (r) return <div className={className}>{children}</div>;
  return (
    <motion.div className={className} variants={i}>
      {children}
    </motion.div>
  );
}
```

### 20.2 Headline word reveal (with reduced‑motion fallback)

```tsx
"use client";
import { motion, useReducedMotion, type Variants } from "motion/react";
type Seg = { text: string; accent?: boolean };
const container: Variants = {
  hidden: {},
  visible: (s: number) => ({ transition: { staggerChildren: s } }),
};
const word: Variants = {
  hidden: { y: "115%" },
  visible: { y: 0, transition: { duration: 0.8, ease: [0.16, 0.84, 0.3, 1] } },
};
export function WordsReveal({
  segments,
  className = "",
  stagger = 0.045,
}: {
  segments: Seg[];
  className?: string;
  stagger?: number;
}) {
  const reduce = useReducedMotion();
  const words: { w: string; accent?: boolean }[] = [];
  segments.forEach((s) =>
    s.text
      .trim()
      .split(/\s+/)
      .forEach((w) => words.push({ w, accent: s.accent })),
  );
  if (reduce)
    return (
      <span className={className}>
        {segments.map((s, i) => (
          <span key={i} className={s.accent ? "text-accent" : ""}>
            {s.text}
            {i < segments.length - 1 ? " " : ""}
          </span>
        ))}
      </span>
    );
  return (
    <motion.span
      className={className}
      variants={container}
      custom={stagger}
      initial="hidden"
      whileInView="visible"
      viewport={{ once: true, amount: 0.2 }}
    >
      {words.map((it, i) => (
        <span key={i} className="inline-block overflow-hidden align-bottom">
          <motion.span
            variants={word}
            className={`inline-block ${it.accent ? "text-accent" : ""}`}
          >
            {it.w}
            {i < words.length - 1 ? "\u00A0" : ""}
          </motion.span>
        </span>
      ))}
    </motion.span>
  );
}
```

### 20.3 Magnetic wrapper

```tsx
"use client";
import { motion, useMotionValue, useSpring } from "motion/react";
import { useRef } from "react";
export function Magnetic({
  children,
  strength = 0.4,
  className = "",
}: {
  children: React.ReactNode;
  strength?: number;
  className?: string;
}) {
  const ref = useRef<HTMLDivElement>(null);
  const x = useMotionValue(0),
    y = useMotionValue(0);
  const sx = useSpring(x, { stiffness: 180, damping: 14, mass: 0.2 }),
    sy = useSpring(y, { stiffness: 180, damping: 14, mass: 0.2 });
  return (
    <motion.div
      ref={ref}
      style={{ x: sx, y: sy }}
      className={`inline-flex ${className}`}
      onMouseMove={(e) => {
        const r = ref.current!.getBoundingClientRect();
        x.set((e.clientX - (r.left + r.width / 2)) * strength);
        y.set((e.clientY - (r.top + r.height / 2)) * strength);
      }}
      onMouseLeave={() => {
        x.set(0);
        y.set(0);
      }}
    >
      {children}
    </motion.div>
  );
}
```

### 20.4 Spotlight card

```tsx
"use client";
import { useRef } from "react";
export function SpotlightCard({
  children,
  className = "",
}: {
  children: React.ReactNode;
  className?: string;
}) {
  const ref = useRef<HTMLDivElement>(null);
  return (
    <div
      ref={ref}
      onMouseMove={(e) => {
        const el = ref.current!,
          r = el.getBoundingClientRect();
        el.style.setProperty("--mx", `${e.clientX - r.left}px`);
        el.style.setProperty("--my", `${e.clientY - r.top}px`);
      }}
      className={`spotlight group relative overflow-hidden ${className}`}
    >
      <div
        className="pointer-events-none absolute -inset-px opacity-0 transition-opacity duration-500 group-hover:opacity-100"
        style={{
          background:
            "radial-gradient(420px circle at var(--mx,50%) var(--my,50%),color-mix(in srgb,var(--accent) 12%,transparent),transparent 60%)",
        }}
      />
      <div className="relative">{children}</div>
    </div>
  );
}
```

```css
.spotlight::before {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1px;
  background: radial-gradient(
    200px circle at var(--mx, 50%) var(--my, 0),
    var(--accent),
    transparent 65%
  );
  -webkit-mask:
    linear-gradient(#000 0 0) content-box,
    linear-gradient(#000 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  opacity: 0;
  transition: opacity 0.4s var(--ease-out-expo);
}
.spotlight:hover::before {
  opacity: 0.8;
}
```

### 20.5 Count‑up

```tsx
"use client";
import { useEffect, useRef, useState } from "react";
export function Counter({
  value,
  suffix = "",
  duration = 1600,
}: {
  value: number;
  suffix?: string;
  duration?: number;
}) {
  const ref = useRef<HTMLSpanElement>(null);
  const [n, setN] = useState(0);
  const done = useRef(false);
  useEffect(() => {
    const el = ref.current;
    if (!el) return;
    const reduce = matchMedia("(prefers-reduced-motion: reduce)").matches;
    const io = new IntersectionObserver(
      (es) =>
        es.forEach((e) => {
          if (e.isIntersecting && !done.current) {
            done.current = true;
            if (reduce) {
              setN(value);
              return;
            }
            const t0 = performance.now();
            const tick = (t: number) => {
              const k = Math.min((t - t0) / duration, 1);
              const e2 = 1 - Math.pow(2, -10 * k);
              setN(value * e2);
              if (k < 1) requestAnimationFrame(tick);
            };
            requestAnimationFrame(tick);
          }
        }),
      { threshold: 0.4 },
    );
    io.observe(el);
    return () => io.disconnect();
  }, [value, duration]);
  return (
    <span ref={ref}>
      {Math.round(n)}
      {suffix}
    </span>
  );
}
```

### 20.6 Searchable select (combobox)

```tsx
"use client";
import { useEffect, useMemo, useRef, useState } from "react";
export function SearchableSelect({
  options,
  value,
  onChange,
  name,
  placeholder = "Select…",
}: {
  options: string[];
  value: string;
  onChange: (v: string) => void;
  name: string;
  placeholder?: string;
}) {
  const [open, setOpen] = useState(false),
    [q, setQ] = useState("");
  const ref = useRef<HTMLDivElement>(null);
  const list = useMemo(() => {
    const s = q.trim().toLowerCase();
    return s ? options.filter((o) => o.toLowerCase().includes(s)) : options;
  }, [options, q]);
  useEffect(() => {
    const h = (e: MouseEvent) => {
      if (ref.current && !ref.current.contains(e.target as Node)) {
        setOpen(false);
        setQ("");
      }
    };
    document.addEventListener("mousedown", h);
    return () => document.removeEventListener("mousedown", h);
  }, []);
  return (
    <div ref={ref} className="relative">
      <button
        type="button"
        onClick={() => setOpen((o) => !o)}
        aria-haspopup="listbox"
        aria-expanded={open}
        className="flex w-full items-center justify-between rounded-xl border border-line bg-surface px-4 py-3.5 text-left"
      >
        <span className={value ? "text-ink" : "text-ink-4"}>
          {value || placeholder}
        </span>
      </button>
      {open && (
        <div className="absolute z-30 mt-2 w-full rounded-xl border border-line-2 bg-surface-2 p-2 shadow-elevated">
          <input
            autoFocus
            value={q}
            onChange={(e) => setQ(e.target.value)}
            placeholder="Type to search…"
            className="w-full rounded-lg border border-line bg-surface px-3 py-2.5"
          />
          <div className="mt-2 max-h-56 overflow-y-auto">
            {list.map((o) => (
              <button
                key={o}
                type="button"
                onClick={() => {
                  onChange(o);
                  setOpen(false);
                  setQ("");
                }}
                className={`block w-full rounded-lg px-3 py-2.5 text-left hover:bg-surface-2 ${o === value ? "text-accent" : "text-ink-2"}`}
              >
                {o}
              </button>
            ))}
            {list.length === 0 && (
              <p className="px-3 py-3 text-ink-4">No matches.</p>
            )}
          </div>
        </div>
      )}
      <input type="hidden" name={name} value={value} />
    </div>
  );
}
```

### 20.7 Floating action button — see §7.13.

---

_Baseline: Next.js 16 · React 19 · Tailwind CSS v4 · Motion v12 · Lenis v1. Treat this as a
skill — classify the site, lay the foundation, compose from patterns, layer motion to the
right amount for the category, and verify (a11y, performance, reduced motion)._
