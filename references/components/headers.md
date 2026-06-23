# Headers

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Headers

A **static** sticky header (server-renderable shell). For the **animated** hide-on-scroll /
glass-on-scroll / glowing / neon headers, see [animated-components.md › Headers](../animated-components.md#headers).

```tsx
// components/site-header.tsx
import Link from "next/link";
import { Button } from "@/components/ui/button";

const nav = [
  { label: "Features", href: "/features" },
  { label: "Pricing", href: "/pricing" },
  { label: "About", href: "/about" },
  { label: "Blog", href: "/blog" },
];

export function SiteHeader() {
  return (
    <header className="sticky top-0 z-40 border-b border-line/70 bg-bg/80 backdrop-blur-md">
      <div className="mx-auto flex h-16 max-w-7xl items-center justify-between px-6">
        <Link
          href="/"
          className="font-display text-lg font-bold tracking-tight text-ink"
        >
          Acme<span className="text-accent">.</span>
        </Link>
        <nav className="hidden items-center gap-8 md:flex">
          {nav.map((n) => (
            <Link
              key={n.href}
              href={n.href}
              className="text-sm font-medium text-ink-2 transition-colors hover:text-ink"
            >
              {n.label}
            </Link>
          ))}
        </nav>
        <div className="flex items-center gap-2">
          <Link
            href="/login"
            className="hidden text-sm font-medium text-ink-2 hover:text-ink sm:block"
          >
            Sign in
          </Link>
          <Button size="sm">Get started</Button>
        </div>
      </div>
    </header>
  );
}
```
