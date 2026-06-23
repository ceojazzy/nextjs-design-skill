# Animated, Glowing & Neon Components

The wow-factor layer: animated headers, **glowing** and **neon** headers, **neon borders & cards**,
gradient-border / spotlight / tilt cards, aurora & particle backgrounds, magnetic & shimmer
buttons, animated text, scroll reveals, marquees, counters, and page transitions.

**Stack:** Next.js App Router + React 19 + Tailwind v4 + Motion (`motion/react`). Uses the
semantic tokens from [../nextjs.md §2](../nextjs.md). **Every effect must degrade** under
`prefers-reduced-motion` — the reduced-motion block at the end handles most of it globally.

**Contents**

- [Add this CSS once](#add-this-css-once) (keyframes + neon/glow/aurora/marquee utilities)
- [Headers](#headers): [Animated (hide-on-scroll + glass)](#animated-header) · [Glowing header](#glowing-header) · [Neon header](#neon-header)
- [Neon & glow](#neon--glow): [Neon border utility](#neon-border-utility) · [Neon card](#neon-card) · [Glow-on-hover card](#glow-on-hover-card) · [Gradient-border card](#gradient-border-card) · [Neon button](#neon-button)
- [Cards](#cards): [Spotlight card](#spotlight-card) · [3D tilt card](#3d-tilt-card) · [Glass card](#glass-card) · [Shine card](#shine-sweep-card)
- [Backgrounds](#backgrounds): [Aurora](#aurora-background) · [Particle field](#particle-field-canvas) · [Animated grid](#animated-grid) · [Spotlight follow](#cursor-spotlight)
- [Buttons](#buttons): [Magnetic](#magnetic-button) · [Shimmer](#shimmer-button) · [Pulse-glow](#pulse-glow-button)
- [Text](#animated-text): [Gradient text](#gradient-text) · [Shimmer text](#shimmer-text) · [Words reveal (scroll)](#words-reveal-on-scroll) · [Typewriter](#typewriter)
- [Motion primitives](#motion-primitives): [Reveal](#reveal) · [Stagger](#stagger) · [Parallax](#parallax) · [Marquee](#marquee) · [Animated counter](#animated-counter) · [Scroll progress](#scroll-progress)
- [Bento grid](#bento-grid)
- [Page transitions](#page-transitions)
- [Reduced-motion (required)](#reduced-motion-global)

---

## Add this CSS once

Append to `globals.css` (after your tokens). These power the neon/glow/aurora/marquee effects.
Tune the neon palette to your brand.

```css
/* ---- neon palette (used by glow utilities) ---- */
:root {
  --neon-1: #00e5ff; /* cyan   */
  --neon-2: #ff2bd6; /* magenta */
  --neon-3: #b14bff; /* violet */
  --neon-4: #39ff14; /* green  */
  --glow-sm: 0 0 8px;
  --glow-md: 0 0 18px;
  --glow-lg: 0 0 36px;
}

/* ---- keyframes ---- */
@keyframes marquee {
  from {
    transform: translateX(0);
  }
  to {
    transform: translateX(-50%);
  }
}
@keyframes aurora {
  0%,
  100% {
    transform: translate(0, 0) scale(1);
  }
  33% {
    transform: translate(6%, -4%) scale(1.1);
  }
  66% {
    transform: translate(-5%, 5%) scale(0.95);
  }
}
@keyframes shimmer {
  from {
    background-position: -200% 0;
  }
  to {
    background-position: 200% 0;
  }
}
@keyframes borderspin {
  to {
    --angle: 360deg;
  }
}
@keyframes pulseGlow {
  0%,
  100% {
    box-shadow: var(--glow-md)
      color-mix(in oklab, var(--accent) 60%, transparent);
  }
  50% {
    box-shadow: var(--glow-lg)
      color-mix(in oklab, var(--accent) 90%, transparent);
  }
}
@keyframes floatY {
  0%,
  100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-10px);
  }
}
@keyframes gridpan {
  to {
    background-position: 40px 40px;
  }
}

/* property for animatable conic angle (gradient borders) */
@property --angle {
  syntax: "<angle>";
  inherits: false;
  initial-value: 0deg;
}

/* ---- utilities ---- */
.neon-text {
  color: #fff;
  text-shadow:
    var(--glow-sm) currentColor,
    var(--glow-md) var(--neon-1);
}
.neon-ring {
  /* glowing 1px ring */
  border: 1px solid color-mix(in oklab, var(--neon-1) 70%, transparent);
  box-shadow:
    var(--glow-md) color-mix(in oklab, var(--neon-1) 45%, transparent),
    inset 0 0 12px color-mix(in oklab, var(--neon-1) 18%, transparent);
}
.glow-accent {
  box-shadow: var(--glow-md) color-mix(in oklab, var(--accent) 55%, transparent);
}

/* animated conic gradient border (set --c1/--c2 to taste) */
.gradient-border {
  position: relative;
  border-radius: 1rem;
  isolation: isolate;
}
.gradient-border::before {
  content: "";
  position: absolute;
  inset: 0;
  padding: 1px;
  border-radius: inherit;
  background: conic-gradient(
    from var(--angle),
    var(--c1, var(--neon-1)),
    var(--c2, var(--neon-2)),
    var(--c1, var(--neon-1))
  );
  -webkit-mask:
    linear-gradient(#000 0 0) content-box,
    linear-gradient(#000 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  animation: borderspin 6s linear infinite;
}

/* infinite marquee track (duplicate children inside) */
.marquee-track {
  display: flex;
  width: max-content;
  animation: marquee 30s linear infinite;
}
.marquee-track:hover {
  animation-play-state: paused;
}

/* faint moving grid background */
.bg-grid {
  background-image:
    linear-gradient(
      to right,
      color-mix(in oklab, var(--ink) 8%, transparent) 1px,
      transparent 1px
    ),
    linear-gradient(
      to bottom,
      color-mix(in oklab, var(--ink) 8%, transparent) 1px,
      transparent 1px
    );
  background-size: 40px 40px;
}
.bg-grid-animated {
  animation: gridpan 8s linear infinite;
}

@media (prefers-reduced-motion: reduce) {
  .marquee-track,
  .gradient-border::before,
  .bg-grid-animated {
    animation: none !important;
  }
}
```

---

## Headers

### Animated header

Hides on scroll-down, reveals on scroll-up, and turns to glass once you leave the top. Pair with
the animated underline from [components/navbars.md › Navbars](components/navbars.md#navbars).

```tsx
// components/animated-header.tsx
"use client";
import Link from "next/link";
import { useState } from "react";
import { motion, useMotionValueEvent, useScroll } from "motion/react";
import { cn } from "@/lib/cn";

const nav = [
  ["Home", "/"],
  ["Work", "/work"],
  ["Services", "/services"],
  ["Contact", "/contact"],
] as const;

export function AnimatedHeader() {
  const { scrollY } = useScroll();
  const [hidden, setHidden] = useState(false);
  const [scrolled, setScrolled] = useState(false);

  useMotionValueEvent(scrollY, "change", (y) => {
    const prev = scrollY.getPrevious() ?? 0;
    setHidden(y > prev && y > 140); // scrolling down past 140px → hide
    setScrolled(y > 8);
  });

  return (
    <motion.header
      variants={{ visible: { y: 0 }, hidden: { y: "-100%" } }}
      animate={hidden ? "hidden" : "visible"}
      transition={{ duration: 0.35, ease: [0.22, 1, 0.36, 1] }}
      className={cn(
        "fixed inset-x-0 top-0 z-40 transition-[background-color,backdrop-filter,border-color] duration-300",
        scrolled
          ? "border-b border-line/70 bg-bg/75 backdrop-blur-xl"
          : "border-b border-transparent bg-transparent",
      )}
    >
      <div className="mx-auto flex h-16 max-w-7xl items-center justify-between px-6">
        <Link href="/" className="font-display text-lg font-bold text-ink">
          Acme<span className="text-accent">.</span>
        </Link>
        <nav className="flex gap-7">
          {nav.map(([label, href]) => (
            <Link
              key={href}
              href={href}
              className="text-sm font-semibold text-ink-2 transition-colors hover:text-ink"
            >
              {label}
            </Link>
          ))}
        </nav>
      </div>
    </motion.header>
  );
}
```

### Glowing header

A soft animated glow line under a glass bar — premium without going full neon.

```tsx
// components/glowing-header.tsx
"use client";
import Link from "next/link";
export function GlowingHeader() {
  return (
    <header className="sticky top-0 z-40 bg-bg/70 backdrop-blur-xl">
      <div className="mx-auto flex h-16 max-w-7xl items-center justify-between px-6">
        <Link href="/" className="font-display text-lg font-bold text-ink">
          Lumen<span className="text-accent">.</span>
        </Link>
        <nav className="flex gap-7 text-sm font-semibold text-ink-2">
          <Link href="#" className="hover:text-ink">
            Platform
          </Link>
          <Link href="#" className="hover:text-ink">
            Pricing
          </Link>
          <Link href="#" className="hover:text-ink">
            Docs
          </Link>
        </nav>
      </div>
      {/* animated glow underline */}
      <div aria-hidden className="relative h-px w-full overflow-hidden">
        <div className="absolute inset-0 bg-line" />
        <div
          className="absolute inset-y-0 left-0 w-1/3 animate-[marquee_4s_linear_infinite]"
          style={{
            background:
              "linear-gradient(90deg, transparent, color-mix(in oklab, var(--accent) 90%, transparent), transparent)",
            boxShadow: "0 0 16px var(--accent)",
          }}
        />
      </div>
    </header>
  );
}
```

### Neon header

Full cyberpunk treatment: dark glass bar, neon glowing logo + active link, neon bottom ring.

```tsx
// components/neon-header.tsx
"use client";
import Link from "next/link";
import { usePathname } from "next/navigation";
import { cn } from "@/lib/cn";

const items = [
  ["Home", "/"],
  ["Arcade", "/arcade"],
  ["Store", "/store"],
  ["Crew", "/crew"],
] as const;

export function NeonHeader() {
  const pathname = usePathname();
  return (
    <header className="sticky top-0 z-40 border-b border-white/10 bg-[#0a0a12]/85 backdrop-blur-xl">
      <div className="mx-auto flex h-16 max-w-7xl items-center justify-between px-6">
        <Link
          href="/"
          className="neon-text font-display text-xl font-bold tracking-widest"
          style={{ ["--neon-1" as string]: "#ff2bd6" }}
        >
          NOVA
        </Link>
        <nav className="flex gap-6">
          {items.map(([label, href]) => {
            const active = pathname === href;
            return (
              <Link
                key={href}
                href={href}
                className={cn(
                  "text-sm font-semibold uppercase tracking-wide transition-all",
                  active ? "neon-text" : "text-white/55 hover:text-white",
                )}
                style={
                  active ? { ["--neon-1" as string]: "#00e5ff" } : undefined
                }
              >
                {label}
              </Link>
            );
          })}
        </nav>
      </div>
      <div
        aria-hidden
        className="h-px w-full"
        style={{
          background:
            "linear-gradient(90deg, transparent, #00e5ff, #ff2bd6, transparent)",
          boxShadow: "0 0 18px #00e5ff",
        }}
      />
    </header>
  );
}
```

---

## Neon & glow

### Neon border utility

Any element becomes a glowing neon panel with `.neon-ring` (defined in the CSS block):

```tsx
<div
  className="neon-ring rounded-2xl bg-[#0b0b14] p-6 text-white"
  style={{ ["--neon-1" as string]: "#b14bff" }}
>
  Neon panel — swap <code>--neon-1</code> for any color.
</div>
```

### Neon card

A dark card with an always-on neon edge and an intensifying glow on hover.

```tsx
// components/neon-card.tsx
import { cn } from "@/lib/cn";
export function NeonCard({
  color = "#00e5ff",
  title,
  children,
  className,
}: {
  color?: string;
  title: string;
  children: React.ReactNode;
  className?: string;
}) {
  return (
    <div
      style={{ ["--neon" as string]: color }}
      className={cn(
        "group relative rounded-2xl border bg-[#0b0b14] p-6 text-white/85 transition-all duration-300",
        "border-[color-mix(in_oklab,var(--neon)_55%,transparent)]",
        "shadow-[0_0_18px_color-mix(in_oklab,var(--neon)_30%,transparent)]",
        "hover:-translate-y-1 hover:shadow-[0_0_42px_color-mix(in_oklab,var(--neon)_60%,transparent)]",
        className,
      )}
    >
      <h3
        className="font-display text-lg font-semibold"
        style={{ color: "#fff", textShadow: `0 0 10px ${color}` }}
      >
        {title}
      </h3>
      <p className="mt-2 text-sm leading-relaxed text-white/60">{children}</p>
      {/* top accent line */}
      <span
        aria-hidden
        className="absolute inset-x-6 top-0 h-px"
        style={{
          background: `linear-gradient(90deg, transparent, ${color}, transparent)`,
        }}
      />
    </div>
  );
}
```

### Glow-on-hover card

Token-driven (works in light & dark). A radial accent glow blooms on hover.

```tsx
// components/glow-card.tsx
export function GlowCard({
  title,
  children,
}: {
  title: string;
  children: React.ReactNode;
}) {
  return (
    <div className="group relative overflow-hidden rounded-2xl border border-line bg-surface p-6 transition-transform duration-300 hover:-translate-y-1">
      <div
        aria-hidden
        className="pointer-events-none absolute -inset-px -z-0 opacity-0 transition-opacity duration-500 group-hover:opacity-100"
        style={{
          background:
            "radial-gradient(40% 60% at 50% 0%, color-mix(in oklab, var(--accent) 26%, transparent), transparent)",
        }}
      />
      <div className="relative z-10">
        <h3 className="font-display text-lg font-semibold text-ink">{title}</h3>
        <p className="mt-2 text-sm leading-relaxed text-ink-3">{children}</p>
      </div>
    </div>
  );
}
```

### Gradient-border card

Animated conic-gradient border via the `.gradient-border` utility (uses `@property --angle`).

```tsx
// components/gradient-border-card.tsx
export function GradientBorderCard({
  title,
  children,
}: {
  title: string;
  children: React.ReactNode;
}) {
  return (
    <div
      className="gradient-border"
      style={{ ["--c1" as string]: "#7c3aed", ["--c2" as string]: "#22d3ee" }}
    >
      <div className="rounded-2xl bg-surface p-6">
        <h3 className="font-display text-lg font-semibold text-ink">{title}</h3>
        <p className="mt-2 text-sm leading-relaxed text-ink-3">{children}</p>
      </div>
    </div>
  );
}
```

### Neon button

```tsx
// components/neon-button.tsx
export function NeonButton({
  children,
  color = "#00e5ff",
}: {
  children: React.ReactNode;
  color?: string;
}) {
  return (
    <button
      style={{ ["--neon" as string]: color }}
      className="relative inline-flex h-11 items-center justify-center rounded-xl border border-[var(--neon)] bg-[var(--neon)]/5 px-6 text-sm font-semibold uppercase tracking-wide text-white transition-all duration-200
                 shadow-[0_0_14px_color-mix(in_oklab,var(--neon)_50%,transparent),inset_0_0_14px_color-mix(in_oklab,var(--neon)_18%,transparent)]
                 hover:bg-[var(--neon)]/15 hover:shadow-[0_0_28px_color-mix(in_oklab,var(--neon)_75%,transparent)] active:scale-95"
    >
      {children}
    </button>
  );
}
```

---

## Cards

### Spotlight card

A radial highlight follows the cursor across the card.

```tsx
// components/spotlight-card.tsx
"use client";
import { useRef } from "react";
export function SpotlightCard({
  title,
  children,
}: {
  title: string;
  children: React.ReactNode;
}) {
  const ref = useRef<HTMLDivElement>(null);
  function onMove(e: React.MouseEvent) {
    const el = ref.current;
    if (!el) return;
    const r = el.getBoundingClientRect();
    el.style.setProperty("--mx", `${e.clientX - r.left}px`);
    el.style.setProperty("--my", `${e.clientY - r.top}px`);
  }
  return (
    <div
      ref={ref}
      onMouseMove={onMove}
      className="group relative overflow-hidden rounded-2xl border border-line bg-surface p-6"
    >
      <div
        aria-hidden
        className="pointer-events-none absolute inset-0 opacity-0 transition-opacity duration-300 group-hover:opacity-100"
        style={{
          background:
            "radial-gradient(220px circle at var(--mx) var(--my), color-mix(in oklab, var(--accent) 18%, transparent), transparent 70%)",
        }}
      />
      <h3 className="relative font-display text-lg font-semibold text-ink">
        {title}
      </h3>
      <p className="relative mt-2 text-sm leading-relaxed text-ink-3">
        {children}
      </p>
    </div>
  );
}
```

### 3D tilt card

Pointer-driven perspective tilt with a spring settle.

```tsx
// components/tilt-card.tsx
"use client";
import { motion, useMotionValue, useSpring, useTransform } from "motion/react";
export function TiltCard({ children }: { children: React.ReactNode }) {
  const x = useMotionValue(0.5),
    y = useMotionValue(0.5);
  const rx = useSpring(useTransform(y, [0, 1], [8, -8]), {
    stiffness: 200,
    damping: 18,
  });
  const ry = useSpring(useTransform(x, [0, 1], [-8, 8]), {
    stiffness: 200,
    damping: 18,
  });
  return (
    <motion.div
      onPointerMove={(e) => {
        const r = e.currentTarget.getBoundingClientRect();
        x.set((e.clientX - r.left) / r.width);
        y.set((e.clientY - r.top) / r.height);
      }}
      onPointerLeave={() => {
        x.set(0.5);
        y.set(0.5);
      }}
      style={{ rotateX: rx, rotateY: ry, transformPerspective: 900 }}
      className="rounded-2xl border border-line bg-surface p-8 will-change-transform"
    >
      {children}
    </motion.div>
  );
}
```

### Glass card

```tsx
// components/glass-card.tsx — place over an aurora/photo background
export function GlassCard({
  title,
  children,
}: {
  title: string;
  children: React.ReactNode;
}) {
  return (
    <div className="rounded-2xl border border-white/15 bg-white/10 p-6 backdrop-blur-xl shadow-[0_8px_40px_-12px_rgba(0,0,0,.4)]">
      <h3 className="font-display text-lg font-semibold text-white">{title}</h3>
      <p className="mt-2 text-sm leading-relaxed text-white/75">{children}</p>
    </div>
  );
}
```

### Shine sweep card

A diagonal light sweep crosses the card on hover.

```tsx
// components/shine-card.tsx
export function ShineCard({
  title,
  children,
}: {
  title: string;
  children: React.ReactNode;
}) {
  return (
    <div className="group relative overflow-hidden rounded-2xl border border-line bg-surface p-6">
      <span
        aria-hidden
        className="pointer-events-none absolute inset-0 -translate-x-full skew-x-12 bg-gradient-to-r from-transparent via-white/15 to-transparent transition-transform duration-700 ease-out group-hover:translate-x-full"
      />
      <h3 className="font-display text-lg font-semibold text-ink">{title}</h3>
      <p className="mt-2 text-sm leading-relaxed text-ink-3">{children}</p>
    </div>
  );
}
```

---

## Backgrounds

### Aurora background

Soft animated gradient blobs. Put it behind a hero; layer glass cards on top.

```tsx
// components/aurora-bg.tsx
export function AuroraBg() {
  return (
    <div
      aria-hidden
      className="pointer-events-none absolute inset-0 -z-10 overflow-hidden"
    >
      <div
        className="absolute -left-1/4 top-[-10%] size-[60vmax] rounded-full opacity-50 blur-3xl"
        style={{
          background: "radial-gradient(circle, #7c3aed, transparent 60%)",
          animation: "aurora 16s ease-in-out infinite",
        }}
      />
      <div
        className="absolute right-[-15%] top-[10%] size-[55vmax] rounded-full opacity-40 blur-3xl"
        style={{
          background: "radial-gradient(circle, #22d3ee, transparent 60%)",
          animation: "aurora 20s ease-in-out infinite reverse",
        }}
      />
      <div
        className="absolute bottom-[-20%] left-1/3 size-[50vmax] rounded-full opacity-40 blur-3xl"
        style={{
          background: "radial-gradient(circle, #ff2bd6, transparent 60%)",
          animation: "aurora 24s ease-in-out infinite",
        }}
      />
    </div>
  );
}
```

### Particle field (canvas)

A lightweight connected-dots network that reads your accent color and respects reduced-motion.

```tsx
// components/particle-field.tsx
"use client";
import { useEffect, useRef } from "react";

export function ParticleField({ density = 0.00008 }: { density?: number }) {
  const ref = useRef<HTMLCanvasElement>(null);
  useEffect(() => {
    const canvas = ref.current;
    if (!canvas) return;
    const ctx = canvas.getContext("2d");
    if (!ctx) return;
    const reduce = matchMedia("(prefers-reduced-motion: reduce)").matches;
    const css = getComputedStyle(document.documentElement);
    const dot = css.getPropertyValue("--accent").trim() || "#74a6f2";
    let raf = 0,
      w = 0,
      h = 0;
    let pts: { x: number; y: number; vx: number; vy: number }[] = [];

    const resize = () => {
      w = canvas.width = canvas.offsetWidth * devicePixelRatio;
      h = canvas.height = canvas.offsetHeight * devicePixelRatio;
      const n = Math.min(140, Math.floor(w * h * density));
      pts = Array.from({ length: n }, () => ({
        x: Math.random() * w,
        y: Math.random() * h,
        vx: (Math.random() - 0.5) * 0.3 * devicePixelRatio,
        vy: (Math.random() - 0.5) * 0.3 * devicePixelRatio,
      }));
    };
    const draw = () => {
      ctx.clearRect(0, 0, w, h);
      for (const p of pts) {
        if (!reduce) {
          p.x += p.vx;
          p.y += p.vy;
        }
        if (p.x < 0 || p.x > w) p.vx *= -1;
        if (p.y < 0 || p.y > h) p.vy *= -1;
        ctx.beginPath();
        ctx.arc(p.x, p.y, 1.4 * devicePixelRatio, 0, Math.PI * 2);
        ctx.fillStyle = dot;
        ctx.globalAlpha = 0.5;
        ctx.fill();
      }
      for (let i = 0; i < pts.length; i++)
        for (let j = i + 1; j < pts.length; j++) {
          const a = pts[i],
            b = pts[j],
            d = Math.hypot(a.x - b.x, a.y - b.y);
          if (d < 120 * devicePixelRatio) {
            ctx.beginPath();
            ctx.moveTo(a.x, a.y);
            ctx.lineTo(b.x, b.y);
            ctx.strokeStyle = dot;
            ctx.globalAlpha = 0.12 * (1 - d / (120 * devicePixelRatio));
            ctx.lineWidth = devicePixelRatio;
            ctx.stroke();
          }
        }
      if (!reduce) raf = requestAnimationFrame(draw);
    };
    resize();
    draw();
    if (reduce) draw(); // single static frame
    addEventListener("resize", resize);
    return () => {
      cancelAnimationFrame(raf);
      removeEventListener("resize", resize);
    };
  }, [density]);

  return (
    <canvas
      ref={ref}
      aria-hidden
      className="pointer-events-none absolute inset-0 -z-10 size-full"
    />
  );
}
```

### Animated grid

```tsx
// usage — a slowly panning blueprint grid behind a section
<div className="relative">
  <div
    aria-hidden
    className="absolute inset-0 -z-10 bg-grid bg-grid-animated [mask-image:radial-gradient(ellipse_at_center,#000_40%,transparent_75%)]"
  />
  {/* section content */}
</div>
```

### Cursor spotlight

A spotlight that follows the pointer across a whole section (great over `bg-grid`).

```tsx
// components/cursor-spotlight.tsx
"use client";
import { useRef } from "react";
export function CursorSpotlight({ children }: { children: React.ReactNode }) {
  const ref = useRef<HTMLDivElement>(null);
  return (
    <div
      ref={ref}
      onMouseMove={(e) => {
        const el = ref.current!;
        const r = el.getBoundingClientRect();
        el.style.setProperty("--x", `${e.clientX - r.left}px`);
        el.style.setProperty("--y", `${e.clientY - r.top}px`);
      }}
      className="group relative overflow-hidden"
      style={{ ["--x" as string]: "50%", ["--y" as string]: "50%" }}
    >
      <div
        aria-hidden
        className="pointer-events-none absolute inset-0 opacity-0 transition-opacity duration-300 group-hover:opacity-100"
        style={{
          background:
            "radial-gradient(400px circle at var(--x) var(--y), color-mix(in oklab, var(--accent) 14%, transparent), transparent 70%)",
        }}
      />
      {children}
    </div>
  );
}
```

---

## Buttons

### Magnetic button

Eases toward the cursor, snaps back on leave.

```tsx
// components/magnetic.tsx
"use client";
import { motion, useMotionValue, useSpring } from "motion/react";
export function Magnetic({ children }: { children: React.ReactNode }) {
  const x = useMotionValue(0),
    y = useMotionValue(0);
  const sx = useSpring(x, { stiffness: 250, damping: 18 }),
    sy = useSpring(y, { stiffness: 250, damping: 18 });
  return (
    <motion.div
      style={{ x: sx, y: sy }}
      className="inline-block"
      onPointerMove={(e) => {
        const r = e.currentTarget.getBoundingClientRect();
        x.set((e.clientX - r.left - r.width / 2) * 0.35);
        y.set((e.clientY - r.top - r.height / 2) * 0.35);
      }}
      onPointerLeave={() => {
        x.set(0);
        y.set(0);
      }}
    >
      {children}
    </motion.div>
  );
}
```

Wrap any button: `<Magnetic><Button size="lg">Get started</Button></Magnetic>`.

### Shimmer button

A sweeping highlight crosses the label continuously.

```tsx
// components/shimmer-button.tsx
export function ShimmerButton({ children }: { children: React.ReactNode }) {
  return (
    <button className="relative inline-flex h-11 items-center justify-center overflow-hidden rounded-xl bg-ink px-6 text-sm font-semibold text-bg">
      <span className="relative z-10">{children}</span>
      <span
        aria-hidden
        className="absolute inset-0"
        style={{
          background:
            "linear-gradient(110deg, transparent 30%, rgba(255,255,255,.45) 50%, transparent 70%)",
          backgroundSize: "200% 100%",
          animation: "shimmer 2.4s linear infinite",
        }}
      />
    </button>
  );
}
```

### Pulse-glow button

```tsx
// components/pulse-button.tsx
export function PulseButton({ children }: { children: React.ReactNode }) {
  return (
    <button
      className="inline-flex h-12 items-center justify-center rounded-full bg-accent px-7 text-sm font-semibold text-accent-ink"
      style={{ animation: "pulseGlow 2.4s ease-in-out infinite" }}
    >
      {children}
    </button>
  );
}
```

---

## Animated text

### Gradient text

```tsx
export function GradientText({ children }: { children: React.ReactNode }) {
  return (
    <span className="bg-gradient-to-r from-accent via-fuchsia-500 to-cyan-400 bg-clip-text text-transparent">
      {children}
    </span>
  );
}
```

### Shimmer text

```tsx
export function ShimmerText({ children }: { children: React.ReactNode }) {
  return (
    <span
      className="bg-clip-text text-transparent"
      style={{
        backgroundImage:
          "linear-gradient(110deg, var(--ink-3) 35%, var(--ink) 50%, var(--ink-3) 65%)",
        backgroundSize: "200% 100%",
        animation: "shimmer 3s linear infinite",
      }}
    >
      {children}
    </span>
  );
}
```

### Words reveal (on scroll)

Headline words rise + fade in, orchestrated by a parent. Static fallback under reduced motion.

```tsx
// components/words-reveal.tsx
"use client";
import { motion, useReducedMotion } from "motion/react";
export function WordsReveal({
  text,
  className,
}: {
  text: string;
  className?: string;
}) {
  const reduce = useReducedMotion();
  const words = text.split(" ");
  if (reduce) return <span className={className}>{text}</span>;
  return (
    <motion.span
      className={className}
      initial="hidden"
      whileInView="show"
      viewport={{ once: true, margin: "-15%" }}
      transition={{ staggerChildren: 0.06 }}
      aria-label={text}
    >
      {words.map((w, i) => (
        <span
          key={i}
          className="inline-block overflow-hidden align-bottom"
          aria-hidden
        >
          <motion.span
            className="inline-block"
            variants={{ hidden: { y: "110%" }, show: { y: 0 } }}
            transition={{ duration: 0.6, ease: [0.22, 1, 0.36, 1] }}
          >
            {w}&nbsp;
          </motion.span>
        </span>
      ))}
    </motion.span>
  );
}
```

Use: `<h1 className="font-display text-6xl font-bold text-ink"><WordsReveal text="Design that moves people" /></h1>`.

### Typewriter

```tsx
// components/typewriter.tsx
"use client";
import { useEffect, useState } from "react";
export function Typewriter({
  words,
  className,
}: {
  words: string[];
  className?: string;
}) {
  const [i, setI] = useState(0),
    [sub, setSub] = useState(""),
    [del, setDel] = useState(false);
  useEffect(() => {
    const full = words[i % words.length];
    if (!del && sub === full) {
      const t = setTimeout(() => setDel(true), 1400);
      return () => clearTimeout(t);
    }
    if (del && sub === "") {
      setDel(false);
      setI((v) => v + 1);
      return;
    }
    const t = setTimeout(
      () => setSub(full.slice(0, sub.length + (del ? -1 : 1))),
      del ? 45 : 90,
    );
    return () => clearTimeout(t);
  }, [sub, del, i, words]);
  return (
    <span className={className}>
      {sub}
      <span className="ml-0.5 inline-block w-px animate-pulse bg-accent">
        &nbsp;
      </span>
    </span>
  );
}
```

---

## Motion primitives

Small reusable building blocks. Import and wrap anything.

### Reveal

```tsx
// components/motion/reveal.tsx
"use client";
import { motion, useReducedMotion } from "motion/react";
export function Reveal({
  children,
  delay = 0,
  y = 24,
}: {
  children: React.ReactNode;
  delay?: number;
  y?: number;
}) {
  const reduce = useReducedMotion();
  return (
    <motion.div
      initial={reduce ? false : { opacity: 0, y }}
      whileInView={{ opacity: 1, y: 0 }}
      viewport={{ once: true, margin: "-12%" }}
      transition={{ duration: 0.6, delay, ease: [0.22, 1, 0.36, 1] }}
    >
      {children}
    </motion.div>
  );
}
```

### Stagger

```tsx
// components/motion/stagger.tsx
"use client";
import { motion } from "motion/react";
const parent = { hidden: {}, show: { transition: { staggerChildren: 0.08 } } };
const child = {
  hidden: { opacity: 0, y: 20 },
  show: {
    opacity: 1,
    y: 0,
    transition: { duration: 0.5, ease: [0.22, 1, 0.36, 1] },
  },
};
export function Stagger({
  children,
  className,
}: {
  children: React.ReactNode;
  className?: string;
}) {
  return (
    <motion.div
      className={className}
      variants={parent}
      initial="hidden"
      whileInView="show"
      viewport={{ once: true, margin: "-10%" }}
    >
      {children}
    </motion.div>
  );
}
export function StaggerItem({
  children,
  className,
}: {
  children: React.ReactNode;
  className?: string;
}) {
  return (
    <motion.div className={className} variants={child}>
      {children}
    </motion.div>
  );
}
```

### Parallax

```tsx
// components/motion/parallax.tsx
"use client";
import { useRef } from "react";
import { motion, useScroll, useTransform } from "motion/react";
export function Parallax({
  children,
  distance = 80,
}: {
  children: React.ReactNode;
  distance?: number;
}) {
  const ref = useRef<HTMLDivElement>(null);
  const { scrollYProgress } = useScroll({
    target: ref,
    offset: ["start end", "end start"],
  });
  const y = useTransform(scrollYProgress, [0, 1], [distance, -distance]);
  return (
    <motion.div ref={ref} style={{ y }}>
      {children}
    </motion.div>
  );
}
```

### Marquee

Infinite horizontal scroller (duplicate children for a seamless loop). Pauses on hover.

```tsx
// components/marquee.tsx
export function Marquee({ children }: { children: React.ReactNode }) {
  return (
    <div className="group relative overflow-hidden [mask-image:linear-gradient(90deg,transparent,#000_8%,#000_92%,transparent)]">
      <div className="marquee-track gap-12 pr-12">
        {children}
        {children /* duplicate for seamless loop */}
      </div>
    </div>
  );
}
```

### Animated counter

Counts up when scrolled into view.

```tsx
// components/animated-counter.tsx
"use client";
import { useEffect, useRef, useState } from "react";
import { animate, useInView } from "motion/react";
export function AnimatedCounter({
  to,
  suffix = "",
  duration = 1.6,
}: {
  to: number;
  suffix?: string;
  duration?: number;
}) {
  const ref = useRef<HTMLSpanElement>(null);
  const inView = useInView(ref, { once: true, margin: "-20%" });
  const [val, setVal] = useState(0);
  useEffect(() => {
    if (!inView) return;
    const controls = animate(0, to, {
      duration,
      ease: [0.22, 1, 0.36, 1],
      onUpdate: (v) => setVal(v),
    });
    return () => controls.stop();
  }, [inView, to, duration]);
  return (
    <span ref={ref} className="font-display tabular-nums">
      {Math.round(val).toLocaleString()}
      {suffix}
    </span>
  );
}
```

### Scroll progress

Top progress bar bound to page scroll.

```tsx
// components/scroll-progress.tsx
"use client";
import { motion, useScroll, useSpring } from "motion/react";
export function ScrollProgress() {
  const { scrollYProgress } = useScroll();
  const scaleX = useSpring(scrollYProgress, {
    stiffness: 120,
    damping: 30,
    restDelta: 0.001,
  });
  return (
    <motion.div
      style={{ scaleX }}
      className="fixed inset-x-0 top-0 z-50 h-0.5 origin-left bg-accent"
    />
  );
}
```

---

## Bento grid

Mixed-size tiles with staggered reveal. Combine with spotlight/glow cards above.

```tsx
// components/bento.tsx
import { Stagger, StaggerItem } from "@/components/motion/stagger";
import { cn } from "@/lib/cn";
const tiles = [
  {
    title: "Realtime",
    span: "md:col-span-2 md:row-span-2",
    body: "Sub-second updates across the board.",
  },
  { title: "Secure", span: "", body: "SOC2 + SSO." },
  { title: "Global", span: "", body: "150+ regions." },
  { title: "Open API", span: "md:col-span-2", body: "Build anything." },
];
export function Bento() {
  return (
    <Stagger className="mx-auto grid max-w-6xl auto-rows-[11rem] grid-cols-1 gap-4 px-6 py-20 md:grid-cols-3">
      {tiles.map((t) => (
        <StaggerItem key={t.title} className={cn("h-full", t.span)}>
          <div className="flex h-full flex-col justify-between rounded-3xl border border-line bg-surface p-6 transition-transform duration-300 hover:-translate-y-1">
            <h3 className="font-display text-lg font-semibold text-ink">
              {t.title}
            </h3>
            <p className="text-sm text-ink-3">{t.body}</p>
          </div>
        </StaggerItem>
      ))}
    </Stagger>
  );
}
```

---

## Page transitions

Fade/slide between routes with the App Router. `key` on the pathname re-mounts on navigation.

```tsx
// components/page-transition.tsx
"use client";
import { usePathname } from "next/navigation";
import { motion } from "motion/react";
export function PageTransition({ children }: { children: React.ReactNode }) {
  const pathname = usePathname();
  return (
    <motion.main
      key={pathname}
      initial={{ opacity: 0, y: 8 }}
      animate={{ opacity: 1, y: 0 }}
      transition={{ duration: 0.35, ease: [0.22, 1, 0.36, 1] }}
    >
      {children}
    </motion.main>
  );
}
```

For the **native** View Transitions API approach, see [../nextjs.md §16](../nextjs.md).

---

## Reduced-motion (global)

Add once to `globals.css`. This is the safety net so every animation above degrades gracefully.

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.001ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.001ms !important;
    scroll-behavior: auto !important;
  }
}
```

Motion components also respect the user setting via `useReducedMotion()` and
`<MotionConfig reducedMotion="user">` (wrap your app once). Always verify each effect leaves a
**readable, static end-state** when motion is reduced.
