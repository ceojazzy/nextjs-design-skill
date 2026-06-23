# Core Component Library

Copy-paste, production-ready components for **Next.js App Router + React 19 + Tailwind v4 +
Motion**, split into focused files. They use the **semantic tokens** from
[../../nextjs.md §2](../../nextjs.md) (`bg-surface`, `text-ink`, `text-ink-3`, `border-line`,
`text-accent`, `font-display`…), so they **theme-switch automatically**. For animated / glow /
neon variants see [../animated-components.md](../animated-components.md).

## Conventions

- Server Components by default; add `"use client"` only when a component uses state/effects/events.
- `cn()` is the tiny class helper below. Swap for `clsx`+`tailwind-merge` if you already use them.
- Replace `Link`/`Image` imports as shown; never animate layout properties (use transform/opacity).
- All paths are relative to this folder; the master handbook lives at `../../nextjs.md`.

```ts
// lib/cn.ts
export function cn(...c: Array<string | false | null | undefined>) {
  return c.filter(Boolean).join(" ");
}
```

## Files

| File | Components |
| --- | --- |
| **[primitives.md](primitives.md)** | Buttons (variants/sizes/loading) · Badges & pills · Cards (base, feature, image) |
| **[heroes.md](heroes.md)** | Hero sections — split (text + media) and centered |
| **[headers.md](headers.md)** | Static sticky header (see [../animated-components.md](../animated-components.md) for animated/glowing/neon headers) |
| **[navbars.md](navbars.md)** | Desktop nav (animated underline) · Dropdown & mega-menu · Mobile drawer · Sidebar (incl. collapsible with collapse button) · Bottom tab bar · Breadcrumbs |
| **[footers.md](footers.md)** | Minimal footer · Mega footer (columns + newsletter) |
| **[sections.md](sections.md)** | Feature grid · Pricing · Testimonials · Stats · FAQ accordion · CTA band · Logo cloud |
| **[forms.md](forms.md)** | Field + inputs · Contact form (validation + states) |
| **[overlays.md](overlays.md)** | Modal / dialog · Toast · Tooltip · Command palette (⌘K) |
| **[ui-elements.md](ui-elements.md)** | Tabs · FAB · Announcement bar · Alert · Skeleton · Steps · Empty state · Pagination · Avatar & group · Progress · Rating · Stat card · Data table |

## Quick index

- **Navigation:** [headers](headers.md) · [navbars](navbars.md) · [footers](footers.md) · [breadcrumbs](navbars.md#breadcrumbs) · [bottom tabs](navbars.md#bottom-tab-bar-mobile-app-style)
- **Above the fold:** [heroes](heroes.md) · [logo cloud](sections.md#logo-cloud)
- **Marketing sections:** [feature grid](sections.md#feature-grid) · [pricing](sections.md#pricing) · [testimonials](sections.md#testimonials) · [stats](sections.md#stats) · [FAQ](sections.md#faq-accordion) · [CTA band](sections.md#cta-band)
- **Building blocks:** [buttons & cards](primitives.md) · [tabs](ui-elements.md#tabs) · [alerts](ui-elements.md#alert--inline-message) · [skeletons](ui-elements.md#skeleton-loader) · [pagination](ui-elements.md#pagination) · [data table](ui-elements.md#data-table-sortable-responsive)
- **Input & feedback:** [forms](forms.md) · [modal/toast/tooltip](overlays.md) · [command palette](overlays.md#command-palette-k)
- **Persistent contact:** [FAB](ui-elements.md#floating-action-button)

---

> **Building full pages or a content collection?** When the brief implies many similar pages
> (services, products, projects, team, blog…), scaffold a **hub + a designed page per item** —
> see [../page-patterns.md](../page-patterns.md). To make forms, search, and data actually work
> (server actions, API routes, validation, email, database, auth), see [../backend.md](../backend.md).
