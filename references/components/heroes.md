# Hero Sections

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Hero sections

### A. Split hero (text + media)

```tsx
// components/hero-split.tsx
import { Button } from "@/components/ui/button";
import { Badge } from "@/components/ui/badge";
import Image from "next/image";
export function HeroSplit() {
  return (
    <section className="mx-auto grid max-w-7xl items-center gap-12 px-6 py-20 md:grid-cols-2 md:py-28">
      <div>
        <Badge tone="accent">New · v2 is live</Badge>
        <h1 className="mt-5 font-display text-4xl font-bold leading-[1.05] tracking-tight text-ink sm:text-5xl lg:text-6xl">
          Build modern products <span className="text-accent">faster</span>.
        </h1>
        <p className="mt-5 max-w-md text-lg leading-relaxed text-ink-3">
          A calm, powerful toolkit for teams who care about craft. Ship in days,
          not quarters.
        </p>
        <div className="mt-8 flex flex-wrap gap-3">
          <Button size="lg">Start free</Button>
          <Button size="lg" variant="outline">
            Book a demo
          </Button>
        </div>
      </div>
      <div className="relative aspect-square overflow-hidden rounded-3xl border border-line bg-surface-2">
        <Image
          src="/hero.jpg"
          alt="Product preview"
          fill
          priority
          sizes="(max-width:768px) 100vw, 50vw"
          className="object-cover"
        />
      </div>
    </section>
  );
}
```

### B. Centered hero (marketing)

```tsx
// components/hero-centered.tsx
import { Button } from "@/components/ui/button";
export function HeroCentered() {
  return (
    <section className="relative mx-auto max-w-4xl px-6 py-28 text-center md:py-36">
      <h1 className="font-display text-5xl font-bold leading-[1.04] tracking-tight text-ink sm:text-6xl lg:text-7xl">
        The fastest way to a beautiful site.
      </h1>
      <p className="mx-auto mt-6 max-w-xl text-lg text-ink-3">
        Design tokens, motion, and components — already wired together.
      </p>
      <div className="mt-9 flex justify-center gap-3">
        <Button size="lg">Get started</Button>
        <Button size="lg" variant="ghost">
          Documentation →
        </Button>
      </div>
    </section>
  );
}
```

For an **aurora / particle / neon** hero backdrop, wrap these in the backgrounds from
[animated-components.md](../animated-components.md).
