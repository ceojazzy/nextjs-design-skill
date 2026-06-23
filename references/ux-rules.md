# UX Rules & Pre-Ship Checklist

The quality gate. Distilled, web/Next.js-flavored rules ordered by impact. Run **§1–§3 (critical)**
on every build; run the full list before shipping. Each line is a pass/fail check.

> Doctrine reminder: _calm layout, loud motion._ Restraint in color/structure, richness in motion.
> Modern only. AA in both themes. Always ship a reduced-motion fallback.

---

## 1. Accessibility — CRITICAL

- [ ] **Contrast** ≥ 4.5:1 body, 3:1 large/UI text — verified **independently** in light AND dark.
- [ ] **Focus visible**: every interactive element has a 2–3px `focus-visible` ring (never remove outlines).
- [ ] **Keyboard**: full tab order matches visual order; modals trap focus + close on `Esc`; menus reachable.
- [ ] **Labels**: icon-only buttons have `aria-label`; inputs have real `<label>` (not placeholder-only).
- [ ] **Headings**: one `<h1>`; no skipped levels.
- [ ] **Alt text** on meaningful images; `aria-hidden` on decorative SVG/canvas.
- [ ] **Color not alone**: pair functional color with icon/text (errors, status).
- [ ] **Reduced motion**: `prefers-reduced-motion` respected; animations leave a readable static end-state.
- [ ] **Semantics**: use `<nav> <main> <header> <footer> <button> <a>` correctly; live regions (`aria-live`) for async feedback.

## 2. Interaction — CRITICAL

- [ ] **Touch targets** ≥ 44×44px; ≥ 8px gap between targets.
- [ ] `cursor-pointer` on everything clickable; no hover-only critical actions.
- [ ] **Feedback within 100ms** of tap/click (press scale, ripple, or color).
- [ ] **Async buttons** disable + show spinner; never double-submit.
- [ ] Errors appear **near the field**, with a recovery path.
- [ ] No layout shift on press/hover (animate transform/opacity, not size/position).

## 3. Performance — HIGH

- [ ] Images via `next/image` (AVIF/WebP, `sizes`, `priority` only on the LCP image).
- [ ] Reserve space (width/height or `aspect-ratio`) → **CLS < 0.1**.
- [ ] Fonts via `next/font` (`display: swap`), self-hosted → no FOIT, no CLS.
- [ ] Keep client JS small: Server Components by default; `"use client"` only where needed.
- [ ] Lazy-load below-the-fold/heavy widgets (`next/dynamic`, `loading="lazy"`).
- [ ] Virtualize lists with 50+ rows.
- [ ] Debounce/throttle scroll, resize, and input handlers.
- [ ] Animate `transform`/`opacity` only; add `will-change` sparingly.
- [ ] Skeletons/streaming for anything > ~300ms; no long blocking spinners.

## 4. Style & visual consistency — HIGH

- [ ] **One style** across the site (don't mix flat + skeuomorphic randomly).
- [ ] SVG icons only (Lucide / hand-rolled) — **no emoji as structural icons**.
- [ ] One icon set: consistent stroke width and corner style; consistent sizes via tokens.
- [ ] Palette derived from product/industry; semantic tokens, **no raw hex in components**.
- [ ] Elevation/shadow scale is consistent; effects match the chosen style.
- [ ] Light + dark designed **together**; dark = soft slate, desaturated accents — not inverted, not pure black.
- [ ] One **primary CTA** per view; secondary actions visually subordinate.

## 5. Layout & responsive — HIGH

- [ ] `<meta viewport>` present; zoom never disabled.
- [ ] **Mobile-first**; systematic breakpoints (≈375 / 768 / 1024 / 1440).
- [ ] No horizontal scroll on mobile; content fits viewport.
- [ ] Body text ≥ 16px on mobile (avoids iOS auto-zoom).
- [ ] Line length 60–75 chars desktop / 35–60 mobile.
- [ ] 4/8px spacing scale; consistent desktop `max-width` (e.g. `max-w-7xl`).
- [ ] Prefer `min-h-dvh` over `100vh`; respect safe-area insets for fixed bars.
- [ ] Defined z-index scale (e.g. 10 / 20 / 40 / 50); fixed headers reserve content padding.

## 6. Typography & color — MEDIUM

- [ ] Type scale is consistent (e.g. 12 14 16 18 24 32 48); use `clamp()` for fluid headings.
- [ ] Line-height 1.5–1.75 for body; tighter for display.
- [ ] Weight reinforces hierarchy (display 600–700, body 400, labels 500).
- [ ] Heading/body fonts pair intentionally; headline-only display faces never used for body.
- [ ] Tabular figures for data/prices/timers (`tabular-nums`).
- [ ] Dark mode uses tonal/desaturated variants; contrast tested separately.

## 7. Animation — MEDIUM

- [ ] Micro-interactions 150–300ms; complex ≤ 400ms; nothing > 500ms.
- [ ] Easing: ease-out entering, ease-in exiting; prefer spring for natural feel; avoid linear for UI.
- [ ] Exit ≈ 60–70% of enter duration.
- [ ] **Motion conveys meaning** (cause→effect, spatial continuity) — not decoration for its own sake.
- [ ] Animate 1–2 key elements per view; stagger lists 30–50ms/item.
- [ ] Animations are **interruptible**; never block input.
- [ ] One global easing/duration token set — consistent rhythm everywhere.

## 8. Forms & feedback — MEDIUM

- [ ] Visible label per input; required marked; helper text persistent (not placeholder-only).
- [ ] Validate on **blur**, not keystroke; show error below the field; focus first invalid on submit.
- [ ] Submit → loading → success/error; success confirmed (toast/checkmark).
- [ ] Correct `type`/`inputMode`/`autocomplete` for mobile keyboards + autofill.
- [ ] Password show/hide toggle; destructive actions confirmed + use danger color.
- [ ] Errors announced to screen readers (`role="alert"` / `aria-live`).
- [ ] Empty states are helpful (message + next action), not blank.

## 9. Navigation — HIGH

- [ ] Current location highlighted (color/weight/indicator).
- [ ] Nav placement consistent across pages; primary vs secondary nav clearly separated.
- [ ] Mobile bottom nav ≤ 5 items, icon **and** label; desktop ≥1024px may use sidebar.
- [ ] Back behavior predictable; restore scroll/filter/input state.
- [ ] Every key screen reachable via URL (deep-linkable).
- [ ] Modals/sheets have a clear close affordance; not used for primary navigation flows.
- [ ] After route change, move focus to main content for screen-reader users.

## 10. Charts & data — LOW (dashboards)

- [ ] Chart type matches data (trend→line, comparison→bar, proportion→pie ≤5 slices).
- [ ] Legends + tooltips; values reachable by keyboard; not color-alone (add pattern/label).
- [ ] Provide a table alternative + text summary (`aria-label`) for screen readers.
- [ ] Empty + loading + error states for every chart.
- [ ] Responsive reflow on small screens; locale-aware number/date formatting; subtle gridlines.

---

## Anti-patterns to reject on sight

- Pure-black (`#000`) "dark mode" with harsh white text on a 30–60 audience site.
- Emoji used as UI icons; raster icons that blur.
- The cliché "AI" purple→pink gradient slapped on everything; auto-playing carousel-as-hero.
- Removed focus outlines; placeholder-as-label; errors only summarized at the top.
- Tall `position: sticky` pinned sections that hijack/lag scroll.
- Animating `width`/`height`/`top`/`left`; animations with no reduced-motion fallback.
- Hover-only menus on touch; tap targets < 44px; horizontal scroll on mobile.
- Mixing two visual styles randomly; raw hex scattered through components.

## Pre-delivery pass (do this last)

1. Tab through the whole page with the keyboard — can you reach and operate everything?
2. Toggle dark mode — re-check contrast and that nothing disappears (borders, dividers).
3. Enable **Reduce Motion** in the OS — confirm content is readable and nothing is hidden.
4. Test at **375px** and in landscape; check `dvh` and safe-area on mobile.
5. Run `next build` + `next lint` clean; verify LCP image has `priority`, others lazy.
6. Confirm one primary CTA per view and SVG-only icons.
