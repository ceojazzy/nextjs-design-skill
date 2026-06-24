# Modern Website Design — Foundations (Next.js)

> The **foundations** layer of the _nextjs-design_ skill. Covers stack, tokens,
> theming, color/type/spacing scales, modern CSS, and motion engineering. For everything else
> see the companion files:
>
> - [SKILL.md](SKILL.md) — entry point, design philosophy, design loop
> - [design-systems.md](references/design-systems.md) — styles, palettes, font pairings
> - [references/categories.md](references/categories.md) — website category playbooks
> - [references/patterns.md](references/patterns.md) — "add X" pattern catalog
> - [references/components/](references/components/README.md) — component library
> - [references/animated-components.md](references/animated-components.md) — animated/effect components
> - [references/ux-rules.md](references/ux-rules.md) — accessibility, performance, checklists
> - [references/backend.md](references/backend.md) — server actions, APIs, validation, email, auth
> - [references/page-patterns.md](references/page-patterns.md) — multi-page generation
>
> **Doctrine:** _calm layout, loud motion._ Restraint in color and structure; richness in
> motion and micro-interaction. Modern only — no dated bevels/drop-shadows-on-everything, no
> pure-black dark mode, no carousels-as-hero. Always ship a
> `prefers-reduced-motion` fallback and hit WCAG AA.

---

## 1. Stack, setup & project structure

### 1.1 The baseline

| Concern       | Choice                          | Notes / link                                                               |
| ------------- | ------------------------------- | -------------------------------------------------------------------------- |
| Framework     | **Next.js** App Router (RSC)    | server components, routing, image opt — <https://nextjs.org/docs>          |
| Runtime       | **React 19**                    | `useSyncExternalStore`, transitions — <https://react.dev>                  |
| Styling       | **Tailwind CSS v4**             | token-driven, `@theme` — <https://tailwindcss.com/docs>                    |
| Animation     | **Motion** (`motion`)           | springs/scroll/layout/variants — <https://motion.dev/docs>                 |
| Smooth scroll | **Lenis**                       | feeds Motion scroll hooks — <https://github.com/darkroomengineering/lenis> |
| Fonts         | `next/font`                     | self-hosted, zero CLS                                                      |
| Icons         | **Lucide** (or hand-rolled SVG) | <https://lucide.dev>                                                       |

```bash
npm i next react react-dom motion lenis
npm i -D tailwindcss @tailwindcss/postcss typescript @types/react @types/node eslint eslint-config-next
```

**When to deviate:** for a heavy _app_ (dashboard) you may swap a headless system
(Radix/shadcn) and add TanStack Table/Query; for timeline-heavy creative work, add **GSAP**;
for 3D, add **react-three-fiber**. Motion + Tailwind is the default for marketing/portfolio.

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

> Keep **one config constant** for contact handles/numbers (FAB, email, phone) so they're
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
in CSS so there's **no React state and no hydration mismatch**:

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
> **beat all utilities** (a bare `a{color:inherit}` kills every `text-*` on links). (2) Don't
> name a token `bg` — it collides with the `bg-` namespace; use `surface`/`canvas`/`paper`.
> (3) Verify your custom `--color-*` actually generate utilities by checking the rendered CSS.

### 2.4 Semantic naming conventions

- **Surfaces:** `bg` (page) → `bg-2` → `surface` (card) → `surface-2` (raised/menu) → `paper`.
- **Text:** `ink` (primary) → `ink-2` → `ink-3` (muted/lede) → `ink-4` (faint/labels).
- **Lines:** `line` (hairline) → `line-2` (stronger/hover).
- **Intent:** `accent`, `gold`, `success`, `warning`, `danger` + `on-accent` for text on accent.
- Menus/dropdowns must use a surface **lighter than the page** (`surface-2`) + a visible border
  so they don't vanish in dark mode.

---

## 3. Color, type, spacing & scales

### 3.1 Building a palette (theory)

- **One accent + neutral ramp (8–10 steps) + one secondary** (warm counter-accent). Tint
  neutrals slightly toward the brand hue for cohesion.
- **Accent is rare**: links, focus rings, the single primary CTA per view, active states.
- **Dark mode = soft slate**, not `#000` (halation/eye-strain; worse for 40+ & OLED). Lift
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

Rules: tighten display tracking (`-.02–.03em`); body line-height 1.5–1.65; measure 60–75ch for
prose; never justify; use `text-balance` on headings, `text-pretty` on paragraphs.

Sources: [Google Fonts](https://fonts.google.com) · [Fontshare](https://www.fontshare.com) ·
[Fontjoy pairings](https://fontjoy.com).

### 3.3 Spacing, radius, shadow, z-index scales

- **Spacing:** 4px base (`0,1,2,3,4,6,8,12,16,24,32`). Section padding `py-24 → py-32`; card
  padding `p-6 → p-10`; element gaps `gap-3 → gap-5`.
- **Radius:** inputs/buttons `rounded-full` or `rounded-xl`; cards `16–28px`; pills `999px`.
- **Shadow:** long, soft, low-opacity (`0 18px 48px -22px rgba(0,0,0,.55)`); avoid harsh
  near-black drop shadows; in dark mode shadows are subtle — separation comes from surface steps.
- **z-index scale (document it):** background `-10` · grain `1` · content `2` · sticky bits `30`
  · header `50` · scroll progress `70` · cursor `100` · FAB `40` · toasts `90` · modal `100+`.

---

## 4. Modern CSS techniques

Use these instead of JS where possible — they're faster and resilient.

- **Fluid sizing:** `clamp()`, `min()`, `max()` for type, spacing, and widths.
- **Container queries:** style a component by _its own_ width, not the viewport —
  `@container (min-width:480px){…}` with a parent `container-type:inline-size`. Perfect for
  cards reused in different columns.
- **`:has()`** (parent/relational selector): `.card:has(img){…}`, form states
  `.field:has(:invalid){…}`, theme-less toggles.
- **`:is()` / `:where()`**: group selectors; `:where()` has **0 specificity** (great for resets).
- **`color-mix()`**: derive tints/translucency from tokens —
  `background:color-mix(in srgb,var(--surface) 80%,transparent)` (glass).
- **Scroll-driven animations (native):** `animation-timeline:scroll()` / `view()` for
  reveal/parallax with **zero JS** (progressive-enhance; check support).
- **`@property`**: register custom props so gradients/angles can animate —
  `@property --angle{syntax:"<angle>";inherits:false;initial-value:0deg}`.
- **`mask` / `mix-blend-mode`**: spotlight borders, gradient text fades, blended cursors.
- **`backdrop-filter`**: glass headers/menus (use sparingly over animated canvases — costly).
- **Logical properties:** `padding-inline`, `margin-block`, `inset` for RTL/i18n-safe layout.
- **`aspect-ratio`**: stable media boxes (`aspect-video`, `aspect-[4/3]`) — no CLS.
- **`accent-color`**: theme native checkboxes/radios/range cheaply.
- **`subgrid`**: align nested grids to a parent track (card rows with equal sections).
- **`text-wrap:balance|pretty`**: better headline/paragraph wrapping.

### Glass + grain + grid

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

### Useful keyframes

```css
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
```

> For marquee, aurora, float, shimmer, and gridpan keyframes see
> [animated-components.md](references/animated-components.md).

### Reduced motion (global safety net)

```css
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
React state to avoid re-renders. Reserve React state for discrete UI (open/closed, active tab).

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
| `useVelocity`                                  | speed of a value               | scroll-reactive marquee                    |
| `useAnimationFrame`                            | per-frame loop                 | marquee, canvas sync                       |
| `useInView`                                    | boolean in-view (ref)          | counters, lazy effects                     |
| `layout` / `layoutId`                          | auto + shared-element layout   | tab indicator, list reorder                |
| `AnimatePresence`                              | exit anims, swaps              | modals, tabs, route/page                   |
| `MotionConfig`                                 | global config                  | `reducedMotion="user"`                     |
| `useReducedMotion`                             | respect setting                | static fallbacks                           |

### 5.3 Springs vs tweens

- **Spring** = physical, interruptible, natural for anything that follows input (cursor, drag,
  layout). Tune `stiffness` (snappiness), `damping` (settle), `mass` (weight). Higher
  stiffness + higher damping = tight & quick; lower = floaty.
- **Tween** = fixed `duration` + `ease`; use for choreographed reveals where timing matters.
- **Signature easing:** `cubic-bezier(0.16,0.84,0.3,1)` (fast-out, soft land). Pick one, reuse it.
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

> With Lenis, `useScroll` keeps working because Lenis drives native scroll. Don't also set CSS
> `scroll-behavior:smooth` (Lenis handles it).

### 5.6 Layout & shared-element

`layout` animates an element when its size/position changes; `layoutId` morphs between two
different elements (e.g., a moving active-tab/nav underline). Wrap conditionally rendered
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

> ⚠️ **Never hide content behind a transform-only reveal.** Under reduced motion Motion won't
> run the transform and the element stays where `initial` put it (e.g. `y:"115%"` → invisible).
> Provide a static fallback (`useReducedMotion()` → render plain) for anything that _contains_
> content (headlines!). Decorative-only motion can simply stop.

---

## 6. Images, media & icons

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
  on-brand imagery; avoid obvious clip-art. Verify each remote URL actually loads.

---

## 7. SEO, metadata & sharing

- **Per-route `metadata`** (title template, description, keywords, canonical).
- **Open Graph + Twitter** cards; generate **OG images** with `next/og` (`ImageResponse`).
- **Structured data (JSON-LD):** `Organization`, `LocalBusiness`, `BreadcrumbList`, `FAQPage`,
  `Product`, `JobPosting`, `Article` as relevant.
- **Sitemap & robots:** `app/sitemap.ts`, `app/robots.ts`.
- **Semantics + perf are SEO:** fast Core Web Vitals, real headings, descriptive links, alt text.
- Refs: [Next metadata](https://nextjs.org/docs/app/api-reference/functions/generate-metadata) ·
  [schema.org](https://schema.org) · [OG protocol](https://ogp.me).

---

## 8. Page & view transitions

- **Native View Transitions API** for element/route morphs (progressive-enhance):
  <https://developer.mozilla.org/docs/Web/API/View_Transitions_API>. Next's App Router supports
  view transitions patterns; tag shared elements with `view-transition-name`.
- **Motion route transitions:** `AnimatePresence` in a `template.tsx`, keyed by pathname; keep
  exits short (≤300ms) so navigation feels instant. Persisted chrome (header/footer) lives in
  `layout.tsx` so it doesn't re-mount.
- Respect reduced motion (cross-fade or none).

---

## 9. Library directory

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
Land-book <https://land-book.com> · Refero <https://refero.design> · Mobbin
<https://mobbin.com> · SiteInspire <https://www.siteinspire.com> · Cosmos
<https://www.cosmos.so> · Httpster <https://httpster.net>

**Learning:** Josh W. Comeau <https://www.joshwcomeau.com> · web.dev <https://web.dev> · MDN
<https://developer.mozilla.org> · Motion examples <https://motion.dev/examples> · Codrops
<https://tympanus.net/codrops> · Smashing <https://www.smashingmagazine.com>

---

_Baseline: Next.js 16 · React 19 · Tailwind CSS v4 · Motion v12 · Lenis v1. Classify the
site, lay the foundation (this file), compose from patterns, layer motion to the right amount
for the category, and verify (a11y, performance, reduced motion)._
