# Website Categories Playbook

Pick **one** category per site. Each lists goal, blueprint, component set, motion level, and
recommended libraries. Category decides layout density and _how much_ motion is appropriate.

Pair with [design-systems.md](design-systems.md) for style/palette/fonts and
[components/](components/README.md) for the actual building blocks.

---

## Portfolio / Agency / Marketing

- **Goal:** story + trust → one primary action (contact/quote).
- **Blueprint:** hide-on-scroll nav → animated hero (word reveal + signature visual) → trust
  strip / market marquee → "what we do" pillars → work/case highlights w/ imagery → why-us
  bento → stats counters → process → testimonial/quote → CTA band → rich footer. Add a
  **FAB** for quick contact.
- **Motion:** _high_ — ambient bg, parallax, reveals, magnetic CTAs, spotlight cards, cursor.
- **Libs:** Motion, Lenis, [Aceternity](https://ui.aceternity.com),
  [Magic UI](https://magicui.design), [React Bits](https://reactbits.dev), Lucide, Unsplash.

---

## Dashboard / Admin / Analytics

- **Goal:** dense info, fast scanning, task completion. **Restrained, functional motion only.**
- **Blueprint:** collapsible **sidebar/rail** → top bar (search, ⌘K, theme, avatar) → **KPI/stat
  card** row → **charts** grid → **data table** (sort/filter/paginate/select) → detail
  drawers/modals. Bento grid + resizable panels; sticky table headers; skeleton loaders.
- **Welcome motion:** count-up KPIs, chart draw-in, row enter/exit (`AnimatePresence`+`layout`),
  drawer slide, sidebar collapse, `layoutId` tab indicator, toasts. Keep ≤300ms. **No
  decorative parallax/particles/cursor.**

| Need                          | Pick                                                                                                                                                                                                                                                  | Link                                                                                 |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Charts (copy-paste, Recharts) | **shadcn Charts**                                                                                                                                                                                                                                     | <https://ui.shadcn.com/charts> · [docs](https://ui.shadcn.com/docs/components/chart) |
| Charts (React)                | **Recharts**                                                                                                                                                                                                                                          | <https://recharts.org>                                                               |
| KPI cards / chart kit         | **Tremor**                                                                                                                                                                                                                                            | <https://tremor.so>                                                                  |
| Rich charts                   | **Nivo** <https://nivo.rocks> · **visx** <https://airbnb.io/visx> · **ECharts** <https://echarts.apache.org> · **ApexCharts** <https://apexcharts.com> · **Chart.js** <https://www.chartjs.org> · **Observable Plot** <https://observablehq.com/plot> |                                                                                      |
| Headless table                | **TanStack Table**                                                                                                                                                                                                                                    | <https://tanstack.com/table>                                                         |
| Ready data table              | **shadcn Data Table**                                                                                                                                                                                                                                 | <https://ui.shadcn.com/docs/components/data-table>                                   |
| Enterprise grid               | **AG Grid** <https://www.ag-grid.com> · **MUI X Data Grid** <https://mui.com/x/react-data-grid> · **Material React Table** <https://www.material-react-table.com> · **Glide Data Grid** <https://grid.glideapps.com>                                  |                                                                                      |
| Virtualize rows               | **TanStack Virtual**                                                                                                                                                                                                                                  | <https://tanstack.com/virtual>                                                       |
| Data fetching                 | **TanStack Query**                                                                                                                                                                                                                                    | <https://tanstack.com/query>                                                         |
| Resizable panels              | **react-resizable-panels**                                                                                                                                                                                                                            | <https://github.com/bvaughn/react-resizable-panels>                                  |
| Drag & drop / kanban          | **dnd-kit**                                                                                                                                                                                                                                           | <https://dndkit.com>                                                                 |
| Command palette ⌘K            | **cmdk**                                                                                                                                                                                                                                              | <https://cmdk.paco.me>                                                               |
| Date range                    | **react-day-picker**                                                                                                                                                                                                                                  | <https://daypicker.dev>                                                              |
| Admin framework               | **Refine** <https://refine.dev> · **React Admin** <https://marmelab.com/react-admin>                                                                                                                                                                  |                                                                                      |
| Component systems             | **shadcn/ui** <https://ui.shadcn.com> · **Mantine** <https://mantine.dev> · **MUI** <https://mui.com> · **Ant Design** <https://ant.design> · **Chakra** <https://chakra-ui.com>                                                                      |                                                                                      |
| Tailwind admin blocks         | **Tailwind Plus** <https://tailwindcss.com/plus> · **Preline** <https://preline.co> · **Flowbite** <https://flowbite.com> · **Tremor blocks**                                                                                                         |                                                                                      |

---

## SaaS Landing / Product Page

- **Blueprint:** nav → hero w/ product shot/loop → social-proof logos → feature splits
  (alternating) → interactive/bento features → pricing table → testimonials → FAQ (accordion) →
  CTA → footer.
- **Motion:** medium — scroll reveals, product loop (Lottie/Rive), hover.
- **Libs:** [Vaul](https://vaul.emilkowal.ski), [Sonner](https://sonner.emilkowal.ski),
  Aceternity/Magic UI hero effects, Recharts mini-charts.

---

## E-commerce / Storefront

- **Blueprint:** announcement bar → nav w/ mega-menu + cart → hero/collection → product grid
  (filters, quick-view) → PDP (gallery, variants, sticky add-to-cart) → **cart drawer** →
  checkout.
- **Motion:** subtle — hover lift, add-to-cart fly, drawer slide.
- **Libs:** [Embla](https://www.embla-carousel.com) galleries, Vaul cart drawer, Sonner toasts,
  skeletons. Platforms: Shopify Hydrogen, Medusa.

---

## Blog / Editorial / Docs

- **Blueprint:** reading column ~65ch, strong type scale, TOC, anchored headings, code blocks
  (Shiki), MDX. Reading-progress bar; search ([cmdk]/Algolia DocSearch).
- **Motion:** minimal — reveal, progress, view-transition between articles.

---

## Local Business / Restaurant / Services

- **Blueprint:** hero w/ strong photo + clear value + **call/book FAB** → services/menu →
  gallery → reviews → map + hours → contact form.
- **Motion:** light. Emphasize **NAP** (name/address/phone), `LocalBusiness` JSON-LD, click-to-call.

---

## Event / Launch / One-pager

- **Blueprint:** countdown hero → agenda → speakers/lineup → tickets/CTA → venue → FAQ.
- **Motion:** medium-high; countdown, sticky ticket CTA.

---

## Personal / Résumé

- **Blueprint:** intro hero → selected work → about → experience timeline → contact.
- **Motion:** tasteful reveals; keep fast and accessible.
