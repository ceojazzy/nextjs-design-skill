---
name: nextjs-modern-design
description: >-
  Modern Next.js website & UI design intelligence. Use this skill whenever the task involves
  building, designing, creating, implementing, reviewing, fixing, improving, or refactoring a
  Next.js (App Router) website or its UI. Covers: design tokens & theming (light/dark), color
  systems, typography pairing, spacing scales, modern CSS, motion engineering (Motion / Framer),
  scroll animations, and a large copy-paste component library — buttons, cards, hero sections,
  headers, navbars, footers, mega-menus, drawers, forms, modals, bento grids, marquees — plus
  animated and effect-driven components: animated headers, glowing headers, neon headers, neon
  borders, neon cards, gradient-border cards, spotlight cards, aurora backgrounds, particle
  fields, magnetic buttons, shimmer/gradient text, and scroll-linked reveals. Includes 30+ UI
  styles (minimalism, glassmorphism, neumorphism, claymorphism, brutalism, bento, aurora,
  retro-futuristic/neon, dark OLED, soft-slate dark). Stack: Next.js App Router + React 19 +
  Tailwind CSS v4 + Motion + Lenis. Also covers full-stack wiring: multi-page generation
  (services, products, projects, team, blog — a hub plus a fully designed page per item), server
  actions, route handlers (API), form handling, validation, email, data fetching/caching,
  database, and auth. Trigger words: landing page, portfolio, dashboard, SaaS, agency, marketing
  site, hero, navbar, header, footer, card, button, modal, animation, dark mode, glassmorphism,
  neon, glow, responsive, accessible, services page, product page, blog, contact form, API route,
  server action, database, authentication.
license: MIT
---

# Next.js Modern Design — Skill

A self-contained, **readable** design skill for building **award-tier modern Next.js websites**.
No scripts, no CLI, no Python — everything is inline Markdown you read on demand. It encodes a
complete design system, deep motion engineering, a style catalog, a category playbook, and a
large **copy-paste component library** (including animated, glowing, and neon components).

**Doctrine:** _calm layout, loud motion._ Restraint in color and structure; richness in motion
and micro-interaction. Modern only — no dated bevels, no drop-shadows on everything, no
pure-black dark mode, no carousel-as-hero. Always ship a `prefers-reduced-motion` fallback and
hit WCAG AA.

**Default stack:** Next.js (App Router, RSC) · React 19 · Tailwind CSS v4 · Motion (`motion/react`) · Lenis.

---

## When to use this skill

Use it when the task changes **how a website looks, feels, moves, or is interacted with**.

### Must use

- Building or scaffolding a new Next.js page/site (landing, portfolio, dashboard, SaaS, agency, blog, e-commerce).
- Creating or refactoring UI components (header, navbar, footer, hero, card, button, form, modal, table).
- Choosing a visual style, color palette, type system, spacing, or motion language.
- Adding animation, dark mode, glow/neon effects, scroll interactions, or page transitions.
- Scaffolding a content **collection** (services, products, projects, team, blog…) that needs a hub + a page per item.
- Wiring forms, search, APIs, server actions, email, data, or auth that a Next.js UI depends on.
- Reviewing UI for accessibility, responsiveness, performance, or visual polish.

### Skip

- DevOps/infra, or backend logic with **no** connection to a Next.js UI (this skill covers the backend that powers the UI — forms, APIs, data, auth — not unrelated services).

**Decision rule:** if the user says _"build / design / make / style / animate / improve / fix the look of"_ anything web-facing → use this skill.

---

## The design loop (run every time)

1. **Classify the site** → pick a category in [nextjs.md §6](nextjs.md) (Portfolio/Agency,
   Dashboard/Admin, SaaS Landing, E-commerce, Blog/Docs, Local/Business, Event, Personal).
   Category sets layout density, component set, and _how much_ motion is appropriate.
2. **Pick a visual style + palette + fonts** → [references/design-systems.md](references/design-systems.md).
   Match the style to the product; never mix styles randomly.
3. **Establish the foundation** → tokens, light + dark theming, type scale, spacing, one
   signature easing. See [nextjs.md §2–§4](nextjs.md).
4. **Compose from components** → headers/navbars/footers/heroes/cards/forms in
   [references/components/](references/components/README.md); add wow-factor with
   [references/animated-components.md](references/animated-components.md).
5. **Layer motion** (ambient → scroll-linked → interaction) → [nextjs.md §5, §8, §9](nextjs.md).
   Never animate everything at once.
6. **Verify** → [references/ux-rules.md](references/ux-rules.md) + [nextjs.md §18 checklists](nextjs.md):
   AA contrast in both themes, keyboard + focus, responsive, reduced-motion fallback,
   transform/opacity-only animation, lazy media, build + lint clean.

**For a content collection** (services / products / projects / team / blog…), don't dump it on one
page — scaffold a hub **and a designed page per item** → [references/page-patterns.md](references/page-patterns.md).
**To make forms, search, APIs, data, or auth actually work**, use [references/backend.md](references/backend.md).

**Infer intent, don't interrogate.** Read the brief, choose the most useful interpretation, and
build. Only ask when truly blocked.

---

## Intent cheatsheet (fuzzy request → concrete action)

| User says                                            | Likely means                                      | Go to                                                                                                     |
| ---------------------------------------------------- | ------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| "make it modern / stylish / premium"                 | strong type, whitespace, restrained color, motion | [design-systems.md](references/design-systems.md) → [components/](references/components/README.md)        |
| "more alive / animated / wow"                        | ambient + scroll motion                           | [animated-components.md](references/animated-components.md), [nextjs.md §5](nextjs.md)                    |
| "glowing / neon / cyberpunk / futuristic"            | glow + neon style                                 | design-systems.md "Retro-Futuristic / Neon" + [animated-components.md](references/animated-components.md) |
| "animated header / sticky nav / hide on scroll"      | smart header                                      | animated-components.md "Headers"                                                                          |
| "navbar / menu / dropdown / mega menu / mobile menu" | navigation                                        | [components/navbars.md](references/components/navbars.md)                                                 |
| "footer"                                             | footer                                            | [components/footers.md](references/components/footers.md)                                                 |
| "neon border / glowing card / gradient border"       | effect card                                       | animated-components.md "Neon & glow"                                                                      |
| "hero section"                                       | above-the-fold                                    | [components/heroes.md](references/components/heroes.md)                                                   |
| "too dark / hard to read"                            | pure-black, low contrast                          | soft-slate dark tokens ([nextjs.md §2](nextjs.md)), lift muted text, AA                                   |
| "dashboard / admin / analytics"                      | dense data UI                                     | [nextjs.md §6.2](nextjs.md), restrained motion                                                            |
| "add WhatsApp / chat / call button"                  | persistent contact                                | [components/ui-elements.md](references/components/ui-elements.md)                                         |
| "add services / products / projects / team / blog"   | a collection → hub + a page per item              | [page-patterns.md](references/page-patterns.md)                                                           |
| "make the form work / send email / save / sign in"   | server action + validation + email/db/auth        | [backend.md](references/backend.md)                                                                       |
| "API route / endpoint / webhook / server action"     | backend handler                                   | [backend.md](references/backend.md)                                                                       |
| "it hangs when scrolling"                            | heavy sticky-pin section                          | remove tall `position:sticky` pins ([nextjs.md §19](nextjs.md))                                           |

---

## Files in this skill

Read the file that matches the task. Each is standalone and readable.

| File                                                                       | Read it for                                                                                                                                                                                                                                                               |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **[nextjs.md](nextjs.md)**                                                 | The master handbook: stack/setup, tokens & theming, color/type/spacing scales, modern CSS, motion engineering, category playbook, pattern catalog, accessibility/performance/responsive/SEO, and base copy-paste components. **Start here for foundations.**              |
| **[references/design-systems.md](references/design-systems.md)**           | 30+ modern UI **styles** (with Tailwind/CSS tokens), curated **color palettes** by product type, and **font pairings**. Read when choosing the look.                                                                                                                      |
| **[references/components/](references/components/README.md)**              | Core **component library** (split into files): [primitives](references/components/primitives.md), [heroes](references/components/heroes.md), [headers](references/components/headers.md), [navbars](references/components/navbars.md), [footers](references/components/footers.md), [sections](references/components/sections.md), [forms](references/components/forms.md), [overlays](references/components/overlays.md), [ui-elements](references/components/ui-elements.md).                                                                |
| **[references/animated-components.md](references/animated-components.md)** | **Animated & effect** components: animated/glowing/neon **headers**, neon **borders & cards**, gradient-border cards, spotlight/tilt cards, aurora & particle backgrounds, marquees, magnetic buttons, shimmer/gradient text, scroll reveals, counters, page transitions. |
| **[references/page-patterns.md](references/page-patterns.md)**             | **Multi-page generation**: when asked to add a collection (services, products, projects, team, blog…), scaffold a hub **and a designed page per item** — typed data source, dynamic routes, `generateStaticParams`, `generateMetadata`, `notFound`, sitemap + nav wiring. |
| **[references/backend.md](references/backend.md)**                         | **Backend that powers the UI**: server actions, route handlers (API), Zod validation, email, newsletter, data fetching/caching, database, auth + middleware, rate limiting, webhooks, dynamic OG images, env/secrets, security checklist.                                  |
| **[references/ux-rules.md](references/ux-rules.md)**                       | Condensed **rules checklist**: accessibility, interaction, performance, layout, typography/color, animation, forms, navigation. Read before shipping.                                                                                                                     |
| **[HOW-TO-USE.md](HOW-TO-USE.md)**                                         | How to install this skill into OpenCode and how to invoke it.                                                                                                                                                                                                             |

---

## Hard rules (never violate)

- **Animate `transform` / `opacity` only** for movement — never `width`, `height`, `top`, `left`.
- **Always provide a `prefers-reduced-motion` fallback** (static, readable end-state).
- **AA contrast in BOTH themes** (4.5:1 body, 3:1 large/UI). Test dark mode independently.
- **Dark mode = soft slate, not pure black** (`#0b0b0f`–`#161922` range), desaturated tonal variants.
- **No emoji as structural icons** — use SVG (Lucide) or hand-rolled `<svg>`.
- **Semantic tokens, not raw hex** in components (`bg-surface`, `text-ink`, `text-accent`).
- **One primary CTA per view**; secondary actions are visually subordinate.
- **`"use client"` only where interactivity lives**; keep pages/layouts as Server Components.
- **Self-host fonts with `next/font`** (zero CLS); **`next/image`** for raster media.
- Heading/body fonts must be loaded and tokenized; minimum 16px body on mobile.

> This Next.js version may differ from older training data. When unsure about an API, consult
> `node_modules/next/dist/docs/` and `motion.dev/docs` before writing code.
