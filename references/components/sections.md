# Page Sections

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Feature grid

```tsx
// components/feature-grid.tsx
import { FeatureCard } from "@/components/feature-card";
const features = [
  {
    title: "Fast by default",
    body: "RSC, streaming, and image optimization out of the box.",
  },
  {
    title: "Accessible",
    body: "WCAG AA, keyboard-first, reduced-motion aware.",
  },
  { title: "Themeable", body: "One token layer drives light and dark." },
];
const Icon = () => (
  <svg
    className="size-5"
    viewBox="0 0 24 24"
    fill="none"
    stroke="currentColor"
    strokeWidth="2"
  >
    <path d="m5 13 4 4L19 7" />
  </svg>
);
export function FeatureGrid() {
  return (
    <section className="mx-auto max-w-7xl px-6 py-20">
      <h2 className="font-display text-3xl font-bold tracking-tight text-ink sm:text-4xl">
        Everything you need
      </h2>
      <div className="mt-10 grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        {features.map((f) => (
          <FeatureCard key={f.title} icon={<Icon />} title={f.title}>
            {f.body}
          </FeatureCard>
        ))}
      </div>
    </section>
  );
}
```

For a **bento** layout (mixed tile sizes), see [animated-components.md › Bento grid](../animated-components.md).

---

## Pricing

```tsx
// components/pricing.tsx
import { Button } from "@/components/ui/button";
import { cn } from "@/lib/cn";

const tiers = [
  {
    name: "Starter",
    price: "$0",
    desc: "For side projects",
    featured: false,
    features: ["1 project", "Community support", "1 GB storage"],
  },
  {
    name: "Pro",
    price: "$24",
    desc: "For growing teams",
    featured: true,
    features: [
      "Unlimited projects",
      "Priority support",
      "100 GB storage",
      "Advanced analytics",
    ],
  },
  {
    name: "Enterprise",
    price: "Custom",
    desc: "For organizations",
    featured: false,
    features: ["SSO & SAML", "Dedicated support", "Unlimited storage", "SLA"],
  },
];

export function Pricing() {
  return (
    <section className="mx-auto max-w-6xl px-6 py-20">
      <div className="mx-auto max-w-xl text-center">
        <h2 className="font-display text-4xl font-bold tracking-tight text-ink">
          Simple, fair pricing
        </h2>
        <p className="mt-3 text-ink-3">Start free. Upgrade when you grow.</p>
      </div>
      <div className="mt-12 grid gap-6 lg:grid-cols-3">
        {tiers.map((t) => (
          <div
            key={t.name}
            className={cn(
              "relative rounded-3xl border p-7",
              t.featured
                ? "border-accent/40 bg-surface shadow-[0_24px_70px_-24px_var(--accent)]"
                : "border-line bg-surface",
            )}
          >
            {t.featured && (
              <span className="absolute -top-3 left-7 rounded-full bg-accent px-3 py-1 text-xs font-semibold text-accent-ink">
                Most popular
              </span>
            )}
            <h3 className="font-display text-lg font-semibold text-ink">
              {t.name}
            </h3>
            <p className="mt-1 text-sm text-ink-3">{t.desc}</p>
            <div className="mt-5 flex items-baseline gap-1">
              <span className="font-display text-4xl font-bold text-ink">
                {t.price}
              </span>
              {t.price !== "Custom" && <span className="text-ink-3">/mo</span>}
            </div>
            <Button
              className="mt-6 w-full"
              variant={t.featured ? "primary" : "outline"}
            >
              {t.price === "Custom" ? "Contact sales" : "Get started"}
            </Button>
            <ul className="mt-7 space-y-3">
              {t.features.map((f) => (
                <li
                  key={f}
                  className="flex items-center gap-2.5 text-sm text-ink-2"
                >
                  <svg
                    className="size-4 shrink-0 text-accent"
                    viewBox="0 0 24 24"
                    fill="none"
                    stroke="currentColor"
                    strokeWidth="2.5"
                  >
                    <path d="m5 13 4 4L19 7" />
                  </svg>
                  {f}
                </li>
              ))}
            </ul>
          </div>
        ))}
      </div>
    </section>
  );
}
```

---

## Testimonials

```tsx
// components/testimonials.tsx
import Image from "next/image";
const quotes = [
  {
    quote: "Shipped our rebrand in a week. The motion system is unreal.",
    name: "Ava Chen",
    role: "CEO, Northwind",
    avatar: "/people/ava.jpg",
  },
  {
    quote:
      "The cleanest dark mode we've ever shipped. Zero contrast complaints.",
    name: "Marco Diaz",
    role: "Design Lead, Vela",
    avatar: "/people/marco.jpg",
  },
  {
    quote: "Accessibility was handled by default. Huge time saver.",
    name: "Priya N.",
    role: "Eng Manager, Loop",
    avatar: "/people/priya.jpg",
  },
];
export function Testimonials() {
  return (
    <section className="mx-auto max-w-7xl px-6 py-20">
      <div className="grid gap-6 md:grid-cols-3">
        {quotes.map((q) => (
          <figure
            key={q.name}
            className="flex flex-col rounded-2xl border border-line bg-surface p-7"
          >
            <blockquote className="flex-1 text-lg leading-relaxed text-ink">
              “{q.quote}”
            </blockquote>
            <figcaption className="mt-6 flex items-center gap-3">
              <Image
                src={q.avatar}
                alt={q.name}
                width={44}
                height={44}
                className="size-11 rounded-full object-cover"
              />
              <div>
                <div className="text-sm font-semibold text-ink">{q.name}</div>
                <div className="text-xs text-ink-3">{q.role}</div>
              </div>
            </figcaption>
          </figure>
        ))}
      </div>
    </section>
  );
}
```

---

## Stats

Static markup below; for **count-up animation** use the AnimatedCounter in
[animated-components.md](../animated-components.md).

```tsx
// components/stats.tsx
const stats = [
  { value: "99.99%", label: "Uptime" },
  { value: "8M+", label: "Requests / day" },
  { value: "120ms", label: "Median latency" },
  { value: "150+", label: "Countries" },
];
export function Stats() {
  return (
    <section className="border-y border-line bg-surface">
      <dl className="mx-auto grid max-w-7xl grid-cols-2 gap-px px-6 py-14 sm:grid-cols-4">
        {stats.map((s) => (
          <div key={s.label} className="text-center">
            <dt className="sr-only">{s.label}</dt>
            <dd className="font-display text-4xl font-bold text-ink">
              {s.value}
            </dd>
            <p className="mt-1 text-sm text-ink-3">{s.label}</p>
          </div>
        ))}
      </dl>
    </section>
  );
}
```

---

## FAQ accordion

```tsx
// components/faq.tsx
"use client";
import { useState } from "react";
import { AnimatePresence, motion } from "motion/react";

const faqs = [
  {
    q: "Can I use this with the App Router?",
    a: "Yes — every component targets Next.js App Router and React 19.",
  },
  {
    q: "Is dark mode included?",
    a: "Yes. One token layer drives both themes; dark mode is soft-slate, not pure black.",
  },
  {
    q: "Does it respect reduced motion?",
    a: "All animations fall back to a static end-state when the user prefers reduced motion.",
  },
];

export function Faq() {
  const [open, setOpen] = useState<number | null>(0);
  return (
    <section className="mx-auto max-w-3xl px-6 py-20">
      <h2 className="text-center font-display text-3xl font-bold text-ink sm:text-4xl">
        Questions & answers
      </h2>
      <div className="mt-10 divide-y divide-line rounded-2xl border border-line bg-surface">
        {faqs.map((f, i) => (
          <div key={i}>
            <button
              onClick={() => setOpen(open === i ? null : i)}
              aria-expanded={open === i}
              className="flex w-full items-center justify-between gap-4 px-6 py-5 text-left"
            >
              <span className="font-medium text-ink">{f.q}</span>
              <svg
                className="size-5 shrink-0 text-ink-3 transition-transform duration-300"
                style={{ transform: open === i ? "rotate(45deg)" : "" }}
                viewBox="0 0 24 24"
                fill="none"
                stroke="currentColor"
                strokeWidth="2"
              >
                <path d="M12 5v14M5 12h14" />
              </svg>
            </button>
            <AnimatePresence initial={false}>
              {open === i && (
                <motion.div
                  initial={{ height: 0, opacity: 0 }}
                  animate={{ height: "auto", opacity: 1 }}
                  exit={{ height: 0, opacity: 0 }}
                  transition={{ duration: 0.28, ease: [0.22, 1, 0.36, 1] }}
                  className="overflow-hidden"
                >
                  <p className="px-6 pb-5 text-sm leading-relaxed text-ink-3">
                    {f.a}
                  </p>
                </motion.div>
              )}
            </AnimatePresence>
          </div>
        ))}
      </div>
    </section>
  );
}
```

---

## CTA band

```tsx
// components/cta-band.tsx
import { Button } from "@/components/ui/button";
export function CtaBand() {
  return (
    <section className="mx-auto max-w-7xl px-6 py-16">
      <div className="relative overflow-hidden rounded-3xl border border-line bg-surface px-8 py-14 text-center sm:px-16">
        {/* soft accent glow */}
        <div
          aria-hidden
          className="pointer-events-none absolute inset-0 -z-10 opacity-60"
          style={{
            background:
              "radial-gradient(60% 80% at 50% 0%, color-mix(in oklab, var(--accent) 22%, transparent), transparent)",
          }}
        />
        <h2 className="mx-auto max-w-2xl font-display text-3xl font-bold tracking-tight text-ink sm:text-4xl">
          Ready to build something people love?
        </h2>
        <p className="mx-auto mt-3 max-w-md text-ink-3">
          Start free today. No credit card required.
        </p>
        <div className="mt-8 flex justify-center gap-3">
          <Button size="lg">Get started</Button>
          <Button size="lg" variant="outline">
            Talk to sales
          </Button>
        </div>
      </div>
    </section>
  );
}
```

---

## Logo cloud

Static grid; for an infinite **marquee** of logos see [animated-components.md › Marquee](../animated-components.md).

```tsx
// components/logo-cloud.tsx
export function LogoCloud() {
  const logos = ["Vercel", "Linear", "Stripe", "Figma", "Notion", "GitHub"];
  return (
    <section className="mx-auto max-w-5xl px-6 py-12">
      <p className="text-center text-sm text-ink-3">Trusted by teams at</p>
      <div className="mt-6 grid grid-cols-3 items-center gap-8 opacity-70 sm:grid-cols-6">
        {logos.map((l) => (
          <div
            key={l}
            className="text-center font-display text-lg font-semibold text-ink-2"
          >
            {l}
          </div>
        ))}
      </div>
    </section>
  );
}
```
