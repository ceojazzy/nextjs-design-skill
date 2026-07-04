---
name: nextjs-design-skill
author: https://github.com/ceojazzy
description: >-
  Modern Next.js website & UI design intelligence. Use this skill whenever the task involves
  building, designing, creating, implementing, reviewing, fixing, improving, or refactoring a
  Next.js (App Router) website or its UI. Covers: design tokens & theming (light/dark), color
  systems, typography pairing, spacing scales, modern CSS, motion engineering (Motion / Framer),
  scroll animations, and a large copy-paste component library — buttons, cards, hero sections,
  headers, navbars, footers, mega-menus, drawers, forms, modals, bento grids, marquees — plus
  animated and effect-driven components: animated headers, glowing headers, neon headers, neon
   borders, neon cards, gradient-border cards, BorderGlow cards (cursor-tracking glow), spotlight cards, aurora backgrounds, particle
  fields, magnetic buttons, shimmer/gradient text, and scroll-linked reveals. Includes 30+ UI
  styles (minimalism, glassmorphism, neumorphism, claymorphism, brutalism, bento, aurora,
  retro-futuristic/neon, dark OLED, soft-slate dark). Stack: Next.js App Router + React 19 +
  Tailwind CSS v4 + Motion + Lenis. Also covers multi-page generation (services, products,
  projects, team, blog — a hub plus a fully designed page per item). Trigger words: landing page,
  portfolio, dashboard, SaaS, agency, marketing site, hero, navbar, header, footer, card, button,
  modal, animation, dark mode, glassmorphism, neon, glow, responsive, accessible, services page,
  product page, blog.
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

## Design Philosophy — Creative Approach

> Read this **before** picking styles, palettes, or components. It governs every design decision.

### Your role

Approach as a design lead at a small studio that gives every client a visual identity
indistinguishable from anyone else's. The client has rejected templated proposals and is paying
for a distinctive point of view. Make deliberate, opinionated choices about palette, typography,
and layout that are specific to this brief — not defaults applied generically.

### Ground it in the subject

If the brief does not pin down the product or subject, name one concrete subject, its audience,
and the page's single job — then state your choice. Check memory for user preferences, prior
projects, or design context before deciding. The subject's own world — its materials,
instruments, artifacts, and vernacular — is where distinctive choices come from. Build with the
brief's real content and subject matter throughout.

### Design principles

**Hero as thesis.** Open with the most characteristic thing in the subject's world, in whatever
form makes sense: a headline, image, animation, live demo, interactive moment. Be deliberate:
a big number with a small label and a gradient accent is the template answer — only use if
that's truly the best option.

**Typography carries personality.** Pair display and body faces deliberately, not the same
families you would reach for on any project. Set a clear type scale with intentional weights,
widths, and spacing. Make the type treatment itself memorable — not a neutral delivery vehicle.

**Structure is information.** Numbering, eyebrows, dividers, labels should encode something true
about the content, not decorate it. Question whether numbered markers (01 / 02 / 03) actually
make sense before using them — they're only appropriate if the content is a real sequence where
order carries information the reader needs.

**Motion serves the subject.** Think about where animation adds value: page-load sequences,
scroll-triggered reveals, hover micro-interactions, ambient atmosphere. One orchestrated moment
lands harder than scattered effects. Sometimes less is more — extra animation can make a design
feel AI-generated.

**Complexity matches the vision.** Maximalist directions need elaborate execution; minimal
directions need precision in spacing, type, and detail. Elegance is executing the chosen vision well.

**Written content matters.** Copy can make a design feel as templated as the visual itself.
Write content specific to the subject and audience — generic filler undermines the whole effort.

### Process: brainstorm → explore → plan → critique → build → critique

**Two-pass approach.** First, brainstorm a compact design plan: color palette (4–6 named hex
values), typography (display + body + utility faces), layout concept (prose + ASCII wireframes),
and a signature element — the single unique thing this page will be remembered by.

Then review against the brief: if any part reads like a generic default rather than a choice
made for this specific brief, revise it. State what you changed and why. Only after confirming
uniqueness should you write code.

**Calibration:** AI-generated design currently clusters around three looks:
1. Warm cream (#F4F1EA) + high-contrast serif + terracotta accent
2. Near-black background + single acid-green or vermilion accent
3. Broadsheet layout + hairline rules + zero border-radius + dense newspaper columns

All three are legitimate for some briefs, but they are defaults rather than choices. Where the
brief pins a direction, follow it exactly. Where it leaves an axis free, don't spend that
freedom on one of these defaults.

### Restraint and self-critique

**Spend boldness in one place.** Let the signature element be the memorable thing; keep
everything around it quiet and disciplined. Cut decoration that does not serve the brief.
Not taking a risk can be a risk itself.

**Quality floor.** Responsive down to mobile, visible keyboard focus, reduced motion respected —
ship these without announcing them.

**Final check.** Before leaving the design, look in the mirror and remove one accessory.
Critique your own work as you build, taking screenshots when possible.

**CSS specificity.** Be careful with selector specificities — it's easy to generate classes that
cancel each other out (especially type-based selectors like `.section` vs element selectors
like `.cta`). Watch paddings/margins between sections.

---

## When to use this skill

Use it when the task changes **how a website looks, feels, moves, or is interacted with**.

### Must use

- Building or scaffolding a new Next.js page/site (landing, portfolio, dashboard, SaaS, agency, blog, e-commerce).
- Creating or refactoring UI components (header, navbar, footer, hero, card, button, form, modal, table).
- Choosing a visual style, color palette, type system, spacing, or motion language.
- Adding animation, dark mode, glow/neon effects, scroll interactions, or page transitions.
- Scaffolding a content **collection** (services, products, projects, team, blog…) that needs a hub + a page per item.
- Reviewing UI for accessibility, responsiveness, performance, or visual polish.

### Skip

- DevOps/infra or any backend logic unrelated to the UI.

**Decision rule:** if the user says _"build / design / make / style / animate / improve / fix the look of"_ anything web-facing → use this skill.

---

## The design loop (run every time)

0. **Design philosophy first** → read the Design Philosophy section above. Ground the design
   in the subject, plan a two-pass approach (brainstorm → critique → build → critique again),
   make one deliberate aesthetic risk, and reject defaults.
1. **Classify the site** → pick a category in [references/categories.md](references/categories.md) (Portfolio/Agency,
   Dashboard/Admin, SaaS Landing, E-commerce, Blog/Docs, Local/Business, Event, Personal).
   Category sets layout density, component set, and _how much_ motion is appropriate.
2. **Pick a visual style + palette + fonts** → [references/design-systems.md](references/design-systems.md).
   Match the style to the product; never mix styles randomly.
3. **Establish the foundation** → tokens, light + dark theming, type scale, spacing, one
   signature easing. See [nextjs.md §2–§4](nextjs.md).
4. **Compose from components** → use the pattern catalog in [references/patterns.md](references/patterns.md)
   to find the right component; build from [references/components/](references/components/README.md);
   add wow-factor with [references/animated-components.md](references/animated-components.md).
5. **Layer motion** (ambient → scroll-linked → interaction) → [nextjs.md §5](nextjs.md),
   [references/animated-components.md](references/animated-components.md).
   Never animate everything at once.
6. **Verify** → [references/ux-rules.md](references/ux-rules.md):
   AA contrast in both themes, keyboard + focus, responsive, reduced-motion fallback,
   transform/opacity-only animation, lazy media, build + lint clean.

**For a content collection** (services / products / projects / team / blog…), don't dump it on one
page — scaffold a hub **and a designed page per item** → [references/page-patterns.md](references/page-patterns.md).

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
| "dashboard / admin / analytics"                      | dense data UI                                     | [categories.md › Dashboard](references/categories.md#dashboard-admin-analytics), restrained motion        |
| "add WhatsApp / chat / call button"                  | persistent contact                                | [components/ui-elements.md](references/components/ui-elements.md)                                         |
| "add services / products / projects / team / blog"   | a collection → hub + a page per item              | [page-patterns.md](references/page-patterns.md)                                                           |
| "it hangs when scrolling"                            | heavy sticky-pin section                          | remove tall `position:sticky` pins (ux-rules.md anti-patterns)                                           |
| "what component should I use for X"                  | find the right pattern                            | [patterns.md](references/patterns.md) — search by feature name                                           |

---

## Files in this skill

Read the file that matches the task. Each is standalone and readable.

| File                                                                       | Read it for                                                                                                                                                                                                                                                               |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **[nextjs.md](nextjs.md)**                                                 | **Foundations:** stack/setup, tokens & theming, color/type/spacing scales, modern CSS, motion engineering, images, SEO, transitions, library directory. **Start here.**                                                                                                  |
| **[references/design-systems.md](references/design-systems.md)**           | 30+ modern UI **styles** (with Tailwind/CSS tokens), curated **color palettes** by product type, and **font pairings**. Read when choosing the look.                                                                                                                      |
| **[references/categories.md](references/categories.md)**                   | **Website category playbooks:** Portfolio, Dashboard, SaaS, E-commerce, Blog, Local Business, Event, Personal — each with goal, blueprint, component set, motion level, and recommended libraries.                                                                        |
| **[references/patterns.md](references/patterns.md)**                       | **"Add X" pattern catalog:** any feature request maps to a reusable pattern with a direct link to the component file that implements it.                                                                                                                                   |
| **[references/components/](references/components/README.md)**              | Core **component library** (split into files): [primitives](references/components/primitives.md), [heroes](references/components/heroes.md), [headers](references/components/headers.md), [navbars](references/components/navbars.md) (incl. collapsible sidebar), [footers](references/components/footers.md), [sections](references/components/sections.md), [forms](references/components/forms.md), [overlays](references/components/overlays.md), [ui-elements](references/components/ui-elements.md). |
| **[references/animated-components.md](references/animated-components.md)** | **Animated & effect** components: animated/glowing/neon **headers**, neon **borders & cards**, gradient-border cards, BorderGlow cards (cursor-tracking glow), spotlight/tilt cards, aurora & particle backgrounds, marquees, magnetic buttons, shimmer/gradient text, scroll reveals, counters, page transitions. |
| **[references/page-patterns.md](references/page-patterns.md)**             | **Multi-page generation**: when asked to add a collection (services, products, projects, team, blog…), scaffold a hub **and a designed page per item** — typed data source, dynamic routes, `generateStaticParams`, `generateMetadata`, `notFound`, sitemap + nav wiring. |
| **[references/ux-rules.md](references/ux-rules.md)**                       | Condensed **rules checklist**: accessibility, interaction, performance, layout, typography/color, animation, forms, navigation, anti-patterns, pre-delivery pass. Read before shipping.                                                                                    |
| **[HOW-TO-USE.md](HOW-TO-USE.md)**                                         | How to install this skill into OpenCode and how to invoke it.                                                                                                                                                                                                             |

---

## Hard rules (never violate)

- **Animate `transform` / `opacity` only** for movement — never `width`, `height`, `top`, `left`.
- **Always provide a `prefers-reduced-motion` fallback** (static, readable end-state).
- **AA contrast in BOTH themes** (4.5:1 body, 3:1 large/UI). Test dark mode independently.
- **Dark mode = soft slate, not pure black** (`#0b0b0f`–`#161922` range), desaturated tonal variants.
- **Always ship both light and dark themes by default**, unless the user explicitly asks for one.
- **No emoji as structural icons** — use SVG (Lucide) or hand-rolled `<svg>`.
- **Semantic tokens, not raw hex** in components (`bg-surface`, `text-ink`, `text-accent`).
- **One primary CTA per view**; secondary actions are visually subordinate.
- **`"use client"` only where interactivity lives**; keep pages/layouts as Server Components.
- **Self-host fonts with `next/font`** (zero CLS); **`next/image`** for raster media.
- Heading/body fonts must be loaded and tokenized; minimum 16px body on mobile.

> This Next.js version may differ from older training data. When unsure about an API, consult
> `node_modules/next/dist/docs/` and `motion.dev/docs` before writing code.
