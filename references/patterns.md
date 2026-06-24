# Pattern Catalog — "Add X"

Map any feature request to a reusable pattern. Implement the pattern, not a one-off. Each pattern
points to the component file that contains the full implementation.

---

## Navigation

| Request | Pattern | Implementation |
| --- | --- | --- |
| "menu / navbar / dropdown / mega menu" | Fixed bar, hide on scroll-down, glass on scroll, animated active underline, hover dropdowns, mobile drawer | [components/navbars.md](components/navbars.md) |
| "header / top bar" | Static sticky or animated hide-on-scroll header | [components/headers.md](components/headers.md) + [animated-components.md › Headers](animated-components.md#headers) |
| "sidebar / rail" | Collapsible sidebar with nav sections | [components/navbars.md › Sidebar](navbars.md#sidebar-nav-dashboardadmin) |
| "bottom tabs / mobile nav" | Fixed bottom tab bar ≤5 items | [components/navbars.md › Bottom tabs](navbars.md#bottom-tab-bar-mobile-app-style) |
| "breadcrumbs" | Path trail with links | [components/navbars.md › Breadcrumbs](navbars.md#breadcrumbs) |
| "footer" | Minimal or mega footer with columns + newsletter | [components/footers.md](components/footers.md) |

## Hero & Above Fold

| Request | Pattern | Implementation |
| --- | --- | --- |
| "hero section" | Split (text + media) or centered hero with headline, sub-copy, 1–2 CTAs, signature visual | [components/heroes.md](components/heroes.md) + [animated-components.md](animated-components.md) for aurora/particle/neon backdrops |
| "logo cloud / trust strip" | Static grid of logos | [components/sections.md › Logo cloud](sections.md#logo-cloud) |
| "marquee / logo ticker" | Infinite horizontal scroller | [animated-components.md › Marquee](animated-components.md#marquee) |

## Content Sections

| Request | Pattern | Implementation |
| --- | --- | --- |
| "features / what we do" | Feature grid with icons | [components/sections.md › Feature grid](sections.md#feature-grid) |
| "bento grid" | Mixed tile sizes with staggered reveal | [animated-components.md › Bento grid](animated-components.md#bento-grid) |
| "pricing" | Tiered cards with featured plan highlight, monthly/annual toggle | [components/sections.md › Pricing](sections.md#pricing) |
| "testimonials / quotes" | Card grid with avatars | [components/sections.md › Testimonials](sections.md#testimonials) |
| "stats / numbers / metrics" | Stat grid, optionally with count-up animation | [components/sections.md › Stats](sections.md#stats) + [animated-components.md › Counter](animated-components.md#animated-counter) |
| "FAQ / accordion" | Expandable items with animated height | [components/sections.md › FAQ](sections.md#faq-accordion) |
| "CTA / call to action" | Gradient CTA band | [components/sections.md › CTA band](sections.md#cta-band) |

## Cards

| Request | Pattern | Implementation |
| --- | --- | --- |
| "card / feature card" | Base card with hover lift | [components/primitives.md › Cards](primitives.md#cards) |
| "spotlight card / cursor glow" | Radial highlight follows cursor | [animated-components.md › Spotlight card](animated-components.md#spotlight-card) |
| "neon card / glowing card" | Dark card with neon edge + hover intensify | [animated-components.md › Neon card](animated-components.md#neon-card) |
| "glow-on-hover card" | Token-driven radial accent glow on hover | [animated-components.md › Glow-on-hover card](animated-components.md#glow-on-hover-card) |
| "gradient border card" | Animated conic-gradient border | [animated-components.md › Gradient-border card](animated-components.md#gradient-border-card) |
| "BorderGlow card / cursor-tracking glow" | Interactive glow follows cursor near edges | [animated-components.md › BorderGlow card](animated-components.md#borderglow-card-react-bits) |
| "3D tilt card" | Pointer-driven perspective tilt | [animated-components.md › 3D tilt card](animated-components.md#3d-tilt-card) |
| "glass card" | Frosted glass over background | [animated-components.md › Glass card](animated-components.md#glass-card) |
| "shine card" | Diagonal light sweep on hover | [animated-components.md › Shine sweep card](animated-components.md#shine-sweep-card) |

## Buttons & Actions

| Request | Pattern | Implementation |
| --- | --- | --- |
| "button" | Primary/secondary/ghost/outline/danger variants with sizes | [components/primitives.md › Buttons](primitives.md#buttons) |
| "magnetic button" | Eases toward cursor, snaps back on leave | [animated-components.md › Magnetic button](animated-components.md#magnetic-button) |
| "shimmer button" | Sweeping highlight across label | [animated-components.md › Shimmer button](animated-components.md#shimmer-button) |
| "pulse-glow button" | Pulsing glow ring | [animated-components.md › Pulse-glow button](animated-components.md#pulse-glow-button) |
| "neon button" | Cyberpunk glowing button | [animated-components.md › Neon button](animated-components.md#neon-button) |
| "WhatsApp / chat / call / back to top" | Persistent FAB fixed bottom-right | [components/ui-elements.md › FAB](components/ui-elements.md#floating-action-button) |

## Forms & Input

| Request | Pattern | Implementation |
| --- | --- | --- |
| "form / input / field" | Labeled field with hint/error states | [components/forms.md](components/forms.md) |
| "contact form" | Client form with validation + states | [components/forms.md › Contact form](components/forms.md#contact-form-client-with-validation--states) |
| "searchable select / combobox" | Filter options live as you type | [components/forms.md › Searchable select](components/forms.md#searchable-select-combobox) |
| "make the form work / send email" | Server action + Zod validation + email | [backend.md](backend.md) |

## Overlays & Feedback

| Request | Pattern | Implementation |
| --- | --- | --- |
| "modal / dialog" | Scale+fade with focus trap | [components/overlays.md › Modal](overlays.md#modal--dialog) |
| "toast / notification" | Spring-animated bottom toast | [components/overlays.md › Toast](overlays.md#toast) |
| "tooltip" | CSS-only hover tooltip | [components/overlays.md › Tooltip](overlays.md#tooltip) |
| "command palette ⌘K" | Quick nav/actions overlay | [components/overlays.md › Command palette](overlays.md#command-palette-k) |
| "skeleton / loading" | Pulsing placeholder | [components/ui-elements.md › Skeleton](components/ui-elements.md#skeleton-loader) |

## Dashboard Elements

| Request | Pattern | Implementation |
| --- | --- | --- |
| "tabs / segmented control" | Animated pill indicator | [components/ui-elements.md › Tabs](components/ui-elements.md#tabs) |
| "data table" | Sortable, responsive table | [components/ui-elements.md › Data table](components/ui-elements.md#data-table-sortable-responsive) |
| "stat card with trend" | Value + delta indicator | [components/ui-elements.md › Stat card](components/ui-elements.md#stat-card-with-trend) |
| "pagination" | Page numbers with ellipsis | [components/ui-elements.md › Pagination](components/ui-elements.md#pagination) |
| "steps / process" | Numbered progress indicator | [components/ui-elements.md › Steps](components/ui-elements.md#steps--process-indicator) |
| "charts" | Recharts / shadcn Charts / Tremor | [categories.md › Dashboard](categories.md#dashboard-admin-analytics) |

## Ambient & Decorative

| Request | Pattern | Implementation |
| --- | --- | --- |
| "aurora / gradient blobs" | Animated gradient blobs behind hero | [animated-components.md › Aurora background](animated-components.md#aurora-background) |
| "particle field / network" | Canvas connected-dots network | [animated-components.md › Particle field](animated-components.md#particle-field-canvas) |
| "animated grid / blueprint" | Panning grid background | [animated-components.md › Animated grid](animated-components.md#animated-grid) |
| "cursor spotlight" | Pointer-following radial light | [animated-components.md › Cursor spotlight](animated-components.md#cursor-spotlight) |
| "cursor / trailing cursor" | Blended ring + dot cursor | [animated-components.md › Cursor](animated-components.md#cursor--ambient-portfoliolanding-only) |
| "grain / noise overlay" | SVG fractal noise texture | [nextjs.md §4](nextjs.md) (grain CSS) |

## Text Effects

| Request | Pattern | Implementation |
| --- | --- | --- |
| "word reveal / text animation" | Per-word rise + fade on scroll | [animated-components.md › Words reveal](animated-components.md#words-reveal-on-scroll) |
| "typewriter" | Auto-typing cycling words | [animated-components.md › Typewriter](animated-components.md#typewriter) |
| "gradient text" | Multi-color gradient clip | [animated-components.md › Gradient text](animated-components.md#gradient-text) |
| "shimmer text" | Sweeping highlight on text | [animated-components.md › Shimmer text](animated-components.md#shimmer-text) |

## Motion Primitives

| Request | Pattern | Implementation |
| --- | --- | --- |
| "reveal / fade in on scroll" | whileInView fade+rise+deblur | [animated-components.md › Reveal](animated-components.md#reveal) |
| "stagger / staggered list" | Parent variants + staggerChildren | [animated-components.md › Stagger](animated-components.md#stagger) |
| "parallax" | Scroll-linked vertical offset | [animated-components.md › Parallax](animated-components.md#parallax) |
| "scroll progress bar" | Top bar bound to page scroll | [animated-components.md › Scroll progress](animated-components.md#scroll-progress) |
| "page transition" | Fade/slide between routes | [animated-components.md › Page transitions](animated-components.md#page-transitions) |

## Multi-Page Collections

| Request | Pattern | Implementation |
| --- | --- | --- |
| "add services / products / projects / team / blog" | Hub + a designed page per item | [page-patterns.md](page-patterns.md) |
