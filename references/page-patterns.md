# Multi-Page Generation Patterns

**The default rule:** when a brief implies a **collection** of similar things — *services*,
*products*, *projects/portfolio*, *team*, *blog posts*, *case studies*, *locations*, *pricing
tiers* — do **not** dump them all on one page. By default scaffold:

1. A typed **data source** (one file, or CMS/DB later).
2. A **hub / index page** that lists the items as cards.
3. A **dynamic detail route** that renders a **fully-designed page per item**.
4. `generateStaticParams` + `generateMetadata` + `notFound()` for each detail page.
5. Wire it into **nav, footer, and `sitemap.ts`**.

> "Add our services" → a services hub **and** a designed page for *each* service. Same for
> products, projects, team, blog. Every detail page uses the design system (tokens, motion,
> components) — never a bare text dump.

**Contents**
- [When to apply](#when-to-apply)
- [Collection → routes map](#collection--routes-map)
- [Worked example: Services (hub + per-service pages)](#worked-example-services)
  - [1. Data source](#1-data-source) · [2. Hub page](#2-hub-page) · [3. Detail page](#3-detail-page-per-service) · [4. Sitemap & nav](#4-sitemap--nav)
- [Reusing the pattern](#reusing-the-pattern-products-projects-team) (products / projects / team)
- [Blog / case studies (MDX or data)](#blog--case-studies)
- [Per-page design checklist](#per-page-design-checklist)

---

## When to apply

Apply automatically when the user says any of: "add **services**", "list our **products**",
"a **portfolio** / **projects** / **work** section", "**team** / about the people", "a **blog**",
"**case studies**", "our **locations** / **stores**", "**pricing** plans with detail".

If there are **> 2 items** and each item has enough to say (description, features, images), give
each its **own page**. For 1–2 trivial items, a single section may suffice — but default to detail
pages when unsure; it's better SEO and UX.

---

## Collection → routes map

| Collection | Hub route | Detail route | Per-item slug from |
| --- | --- | --- | --- |
| Services | `/services` | `/services/[slug]` | service name |
| Products | `/products` | `/products/[slug]` | product handle |
| Projects / Work | `/work` | `/work/[slug]` | project name |
| Team | `/team` (or in `/about`) | `/team/[slug]` | person name |
| Blog | `/blog` | `/blog/[slug]` | post title |
| Case studies | `/case-studies` | `/case-studies/[slug]` | study title |
| Locations | `/locations` | `/locations/[slug]` | city / branch |

Every detail route gets static params + unique metadata. Keep the **same chrome** (header,
footer, theme) across all of them via the root layout.

---

## Worked example: Services

A complete, copy-adaptable pattern. Swap "service" for any collection above.

### 1. Data source

One typed file is the source of truth (move to a CMS/DB later without changing the pages).

```ts
// app/services/services-data.ts
export type Service = {
  slug: string;
  title: string;
  summary: string;        // 1 line for cards + meta description
  icon: string;           // svg path d=""  (or a Lucide name)
  hero: string;           // /images/services/<slug>.jpg
  overview: string[];     // intro paragraphs
  features: { title: string; body: string }[];
  process: { step: string; body: string }[];
  faqs: { q: string; a: string }[];
  cta?: string;           // override default CTA copy
};

export const services: Service[] = [
  {
    slug: "it-consulting",
    title: "IT Consulting",
    summary: "Strategy, architecture, and roadmaps that de-risk delivery.",
    icon: "M12 2 2 7l10 5 10-5-10-5Zm0 13L2 10v7l10 5 10-5v-7l-10 5Z",
    hero: "/images/services/it-consulting.jpg",
    overview: [
      "We help teams choose the right architecture and ship with confidence.",
      "From audits to delivery, we plug in where you need us.",
    ],
    features: [
      { title: "Architecture reviews", body: "Find risk before it ships." },
      { title: "Cloud strategy", body: "Right-size cost and scale." },
      { title: "Team enablement", body: "Upskill while we build together." },
    ],
    process: [
      { step: "Discover", body: "Audit goals, systems, constraints." },
      { step: "Plan", body: "A pragmatic, sequenced roadmap." },
      { step: "Deliver", body: "Ship in increments with your team." },
    ],
    faqs: [
      { q: "How fast can we start?", a: "Most engagements kick off within a week." },
      { q: "Do you work with our stack?", a: "Yes — we adapt to your tools and process." },
    ],
  },
  // …more services (each becomes its own page automatically)
];

export const getService = (slug: string) => services.find((s) => s.slug === slug);
```

### 2. Hub page

Server Component. Lists every service as a card linking to its detail page. Add motion reveals
from [animated-components.md](animated-components.md).

```tsx
// app/services/page.tsx
import type { Metadata } from "next";
import Link from "next/link";
import { services } from "./services-data";
import { Reveal } from "@/components/motion/reveal";

export const metadata: Metadata = {
  title: "Services",
  description: "What we do and how we help teams ship.",
};

export default function ServicesPage() {
  return (
    <section className="mx-auto max-w-7xl px-6 py-20 md:py-28">
      <header className="max-w-2xl">
        <p className="text-sm font-medium uppercase tracking-wider text-accent">What we do</p>
        <h1 className="mt-3 font-display text-4xl font-bold tracking-tight text-ink sm:text-5xl">Services</h1>
        <p className="mt-4 text-lg text-ink-3">Pick a service to see how we approach it end to end.</p>
      </header>

      <div className="mt-12 grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
        {services.map((s, i) => (
          <Reveal key={s.slug} delay={i * 0.06}>
            <Link href={`/services/${s.slug}`}
              className="group flex h-full flex-col rounded-2xl border border-line bg-surface p-6 transition-[transform,border-color,box-shadow] duration-300 hover:-translate-y-1 hover:border-line-2 hover:shadow-[0_12px_40px_-12px_rgba(0,0,0,.18)]">
              <span className="mb-4 inline-flex size-11 items-center justify-center rounded-xl bg-accent/10 text-accent">
                <svg className="size-5" viewBox="0 0 24 24" fill="currentColor" aria-hidden><path d={s.icon} /></svg>
              </span>
              <h2 className="font-display text-lg font-semibold text-ink">{s.title}</h2>
              <p className="mt-2 flex-1 text-sm leading-relaxed text-ink-3">{s.summary}</p>
              <span className="mt-4 inline-flex items-center gap-1 text-sm font-medium text-accent">
                Explore
                <svg className="size-4 transition-transform group-hover:translate-x-1" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"><path d="M5 12h14M13 6l6 6-6 6" /></svg>
              </span>
            </Link>
          </Reveal>
        ))}
      </div>
    </section>
  );
}
```

### 3. Detail page (per service)

One dynamic route renders a **complete, designed page** for every service. Note the **async
`params`** (Next.js 15/16), `generateStaticParams` (SSG), `generateMetadata` (unique SEO), and
`notFound()`.

```tsx
// app/services/[slug]/page.tsx
import type { Metadata } from "next";
import Link from "next/link";
import Image from "next/image";
import { notFound } from "next/navigation";
import { services, getService } from "../services-data";
import { Reveal } from "@/components/motion/reveal";
import { Button } from "@/components/ui/button";

// 1) Pre-render every service at build time
export function generateStaticParams() {
  return services.map((s) => ({ slug: s.slug }));
}

// 2) Unique title/description/OG per page
export async function generateMetadata({ params }: { params: Promise<{ slug: string }> }): Promise<Metadata> {
  const { slug } = await params;
  const service = getService(slug);
  if (!service) return {};
  return {
    title: service.title,
    description: service.summary,
    openGraph: { title: service.title, description: service.summary, images: [service.hero] },
    alternates: { canonical: `/services/${service.slug}` },
  };
}

export default async function ServiceDetailPage({ params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params;
  const service = getService(slug);
  if (!service) notFound();

  const related = services.filter((s) => s.slug !== service.slug).slice(0, 3);

  return (
    <article>
      {/* Breadcrumb */}
      <nav aria-label="Breadcrumb" className="mx-auto max-w-5xl px-6 pt-10 text-sm text-ink-3">
        <Link href="/services" className="hover:text-ink">Services</Link>
        <span aria-hidden className="px-1.5 text-line-2">/</span>
        <span className="text-ink">{service.title}</span>
      </nav>

      {/* Hero */}
      <header className="mx-auto grid max-w-5xl items-center gap-10 px-6 py-12 md:grid-cols-2">
        <div>
          <h1 className="font-display text-4xl font-bold tracking-tight text-ink sm:text-5xl">{service.title}</h1>
          <p className="mt-4 text-lg text-ink-3">{service.summary}</p>
          <div className="mt-7 flex gap-3">
            <Button>Get a quote</Button>
            <Button variant="outline">Talk to us</Button>
          </div>
        </div>
        <div className="relative aspect-[4/3] overflow-hidden rounded-3xl border border-line bg-surface-2">
          <Image src={service.hero} alt={service.title} fill priority sizes="(max-width:768px) 100vw, 50vw" className="object-cover" />
        </div>
      </header>

      {/* Overview */}
      <section className="mx-auto max-w-3xl px-6 py-10">
        {service.overview.map((p, i) => (
          <Reveal key={i} delay={i * 0.05}>
            <p className="mb-4 text-lg leading-relaxed text-ink-2">{p}</p>
          </Reveal>
        ))}
      </section>

      {/* Features */}
      <section className="mx-auto max-w-5xl px-6 py-10">
        <h2 className="font-display text-2xl font-bold text-ink">What's included</h2>
        <div className="mt-6 grid gap-5 sm:grid-cols-2 lg:grid-cols-3">
          {service.features.map((f, i) => (
            <Reveal key={f.title} delay={i * 0.06}>
              <div className="h-full rounded-2xl border border-line bg-surface p-6">
                <h3 className="font-display text-lg font-semibold text-ink">{f.title}</h3>
                <p className="mt-2 text-sm leading-relaxed text-ink-3">{f.body}</p>
              </div>
            </Reveal>
          ))}
        </div>
      </section>

      {/* Process */}
      <section className="mx-auto max-w-5xl px-6 py-10">
        <h2 className="font-display text-2xl font-bold text-ink">How it works</h2>
        <ol className="mt-6 grid gap-6 sm:grid-cols-3">
          {service.process.map((p, i) => (
            <li key={p.step} className="relative rounded-2xl border border-line bg-surface p-6">
              <span className="font-display text-sm font-bold text-accent">0{i + 1}</span>
              <h3 className="mt-2 font-display text-lg font-semibold text-ink">{p.step}</h3>
              <p className="mt-1 text-sm text-ink-3">{p.body}</p>
            </li>
          ))}
        </ol>
      </section>

      {/* FAQ */}
      {service.faqs.length > 0 && (
        <section className="mx-auto max-w-3xl px-6 py-10">
          <h2 className="font-display text-2xl font-bold text-ink">FAQs</h2>
          <dl className="mt-6 divide-y divide-line rounded-2xl border border-line bg-surface">
            {service.faqs.map((f) => (
              <div key={f.q} className="px-6 py-5">
                <dt className="font-medium text-ink">{f.q}</dt>
                <dd className="mt-1.5 text-sm text-ink-3">{f.a}</dd>
              </div>
            ))}
          </dl>
        </section>
      )}

      {/* CTA */}
      <section className="mx-auto max-w-5xl px-6 py-14">
        <div className="rounded-3xl border border-line bg-surface px-8 py-12 text-center">
          <h2 className="font-display text-2xl font-bold text-ink sm:text-3xl">{service.cta ?? `Ready to start with ${service.title}?`}</h2>
          <div className="mt-6 flex justify-center gap-3">
            <Button size="lg">Get started</Button>
            <Button size="lg" variant="outline">Contact us</Button>
          </div>
        </div>
      </section>

      {/* Related */}
      <section className="mx-auto max-w-5xl px-6 pb-20">
        <h2 className="font-display text-xl font-bold text-ink">Other services</h2>
        <div className="mt-5 grid gap-5 sm:grid-cols-3">
          {related.map((r) => (
            <Link key={r.slug} href={`/services/${r.slug}`}
              className="rounded-2xl border border-line bg-surface p-5 transition-colors hover:border-line-2">
              <h3 className="font-display font-semibold text-ink">{r.title}</h3>
              <p className="mt-1 text-sm text-ink-3">{r.summary}</p>
            </Link>
          ))}
        </div>
      </section>
    </article>
  );
}
```

> Add **structured data** (`Service` JSON-LD) and an FAQ schema in the detail page for richer
> search results — see [../nextjs.md §15](../nextjs.md). For a `FAQPage` schema, serialize
> `service.faqs` into JSON-LD.

### 4. Sitemap & nav

Generate sitemap entries from the same data so new items are indexed automatically.

```ts
// app/sitemap.ts
import type { MetadataRoute } from "next";
import { services } from "./services/services-data";

const base = "https://example.com";

export default function sitemap(): MetadataRoute.Sitemap {
  const staticPages = ["", "/services", "/about", "/contact"].map((p) => ({
    url: `${base}${p}`, lastModified: new Date(),
  }));
  const servicePages = services.map((s) => ({
    url: `${base}/services/${s.slug}`, lastModified: new Date(), changeFrequency: "monthly" as const, priority: 0.8,
  }));
  return [...staticPages, ...servicePages];
}
```

Add the hub to the header nav (and optionally a dropdown of services) per
[components/navbars.md › Navbars](components/navbars.md#navbars), and a "Services" column in the footer.

---

## Reusing the pattern (products, projects, team)

Same four steps — only the **data shape** and **detail layout** change:

| Collection | Data fields (besides slug/title/summary) | Detail page emphasis |
| --- | --- | --- |
| **Products** | price, images[], variants, specs, inStock | gallery + buy box + specs table + reviews |
| **Projects / Work** | client, year, role, cover, gallery[], results | case-study story: problem → approach → result + visuals |
| **Team** | role, photo, bio, links | portrait + bio + role + social, link from `/about` |
| **Locations** | address, hours, map, phone | map embed + hours + contact + directions |

Each still gets `generateStaticParams`, `generateMetadata`, `notFound()`, breadcrumbs, related
items, and a CTA. **Reuse the same components** (cards, reveals, buttons) so the collection looks
native to the site.

---

## Blog / case studies

Two storage options:

- **Data/CMS:** posts in a typed array or headless CMS → identical pattern to Services, plus
  `author`, `publishedAt`, `tags[]`, `readingTime`, `cover`.
- **MDX:** put `*.mdx` in `content/blog/`, read with `fs` in a Server Component (or
  `@next/mdx` / `next-mdx-remote`). Derive `slug` from the filename.

Blog detail must add: author block, published date (`<time dateTime>`), reading time, tags,
prev/next links, and `article` + `BlogPosting` JSON-LD with an OG image (use a dynamic OG route —
see [backend.md](backend.md)).

```ts
// app/blog/posts.ts (data option)
export type Post = {
  slug: string; title: string; excerpt: string; cover: string;
  author: string; publishedAt: string; tags: string[]; body: string;
};
export const posts: Post[] = [/* … */];
export const getPost = (slug: string) => posts.find((p) => p.slug === slug);
export const readingTime = (body: string) => `${Math.max(1, Math.round(body.split(/\s+/).length / 200))} min read`;
```

Then `app/blog/page.tsx` (list) and `app/blog/[slug]/page.tsx` (detail) mirror the Services hub +
detail above.

---

## Per-page design checklist

For **every** generated detail page:

- [ ] Uses the design tokens + the same header/footer chrome (no orphan styling).
- [ ] `generateStaticParams` (SSG) + `generateMetadata` (unique title, description, canonical, OG).
- [ ] `notFound()` for unknown slugs; a real `app/not-found.tsx` exists.
- [ ] Breadcrumb back to the hub; "related items" block; one clear CTA.
- [ ] Hero image via `next/image` with `priority`; other media lazy.
- [ ] Scroll reveals (reduced-motion safe) — not a flat wall of text.
- [ ] Added to `sitemap.ts`, header nav, and footer.
- [ ] Optional JSON-LD (`Service` / `Product` / `BlogPosting` / `FAQPage`) for rich results.
- [ ] Forms on the page wired to a server action / route handler — see [backend.md](backend.md).
