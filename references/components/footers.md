# Footers

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Footers

### Minimal footer

```tsx
// components/footer-minimal.tsx
import Link from "next/link";
export function FooterMinimal() {
  return (
    <footer className="border-t border-line">
      <div className="mx-auto flex max-w-7xl flex-col items-center justify-between gap-4 px-6 py-8 sm:flex-row">
        <p className="text-sm text-ink-3">
          © {new Date().getFullYear()} Acme, Inc.
        </p>
        <nav className="flex gap-6 text-sm text-ink-3">
          <Link href="/privacy" className="hover:text-ink">
            Privacy
          </Link>
          <Link href="/terms" className="hover:text-ink">
            Terms
          </Link>
          <Link href="/contact" className="hover:text-ink">
            Contact
          </Link>
        </nav>
      </div>
    </footer>
  );
}
```

### Mega footer (columns + newsletter)

```tsx
// components/footer-mega.tsx
import Link from "next/link";

const cols = [
  {
    title: "Product",
    links: [
      ["Features", "/features"],
      ["Pricing", "/pricing"],
      ["Changelog", "/changelog"],
      ["Roadmap", "/roadmap"],
    ],
  },
  {
    title: "Company",
    links: [
      ["About", "/about"],
      ["Careers", "/careers"],
      ["Blog", "/blog"],
      ["Contact", "/contact"],
    ],
  },
  {
    title: "Resources",
    links: [
      ["Docs", "/docs"],
      ["Help center", "/help"],
      ["API", "/api"],
      ["Status", "/status"],
    ],
  },
  {
    title: "Legal",
    links: [
      ["Privacy", "/privacy"],
      ["Terms", "/terms"],
      ["Security", "/security"],
    ],
  },
] as const;

export function FooterMega() {
  return (
    <footer className="border-t border-line bg-surface">
      <div className="mx-auto max-w-7xl px-6 py-16">
        <div className="grid gap-12 lg:grid-cols-[1.5fr_2fr]">
          <div className="max-w-sm">
            <Link href="/" className="font-display text-xl font-bold text-ink">
              Acme<span className="text-accent">.</span>
            </Link>
            <p className="mt-3 text-sm leading-relaxed text-ink-3">
              The modern toolkit for teams who care about craft.
            </p>
            <form
              className="mt-5 flex gap-2"
              action="/api/subscribe"
              method="post"
            >
              <input
                type="email"
                required
                placeholder="you@company.com"
                aria-label="Email"
                className="h-11 w-full rounded-xl border border-line bg-bg px-3.5 text-sm text-ink placeholder:text-ink-3 focus-visible:border-accent focus-visible:outline-none"
              />
              <button className="h-11 shrink-0 rounded-xl bg-accent px-4 text-sm font-semibold text-accent-ink hover:brightness-110">
                Subscribe
              </button>
            </form>
          </div>
          <div className="grid grid-cols-2 gap-8 sm:grid-cols-4">
            {cols.map((c) => (
              <div key={c.title}>
                <h3 className="text-sm font-semibold text-ink">{c.title}</h3>
                <ul className="mt-3 space-y-2.5">
                  {c.links.map(([label, href]) => (
                    <li key={href}>
                      <Link
                        href={href}
                        className="text-sm text-ink-3 hover:text-ink"
                      >
                        {label}
                      </Link>
                    </li>
                  ))}
                </ul>
              </div>
            ))}
          </div>
        </div>
        <div className="mt-12 flex flex-col items-center justify-between gap-4 border-t border-line pt-8 sm:flex-row">
          <p className="text-sm text-ink-3">
            © {new Date().getFullYear()} Acme, Inc. All rights reserved.
          </p>
          <div className="flex gap-4 text-ink-3">
            {/* swap for real social SVGs */}
            <Link href="#" aria-label="X" className="hover:text-ink">
              𝕏
            </Link>
            <Link href="#" aria-label="GitHub" className="hover:text-ink">
              GitHub
            </Link>
            <Link href="#" aria-label="LinkedIn" className="hover:text-ink">
              in
            </Link>
          </div>
        </div>
      </div>
    </footer>
  );
}
```

For a **gradient CTA band above the footer**, see [CTA band](sections.md#cta-band). For a **glowing/neon footer
top-border**, see [animated-components.md](../animated-components.md).
