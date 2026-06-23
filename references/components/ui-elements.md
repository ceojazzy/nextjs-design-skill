# UI Elements

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Tabs

```tsx
// components/ui/tabs.tsx
"use client";
import { useState } from "react";
import { motion } from "motion/react";
import { cn } from "@/lib/cn";

export function Tabs({
  tabs,
}: {
  tabs: { label: string; content: React.ReactNode }[];
}) {
  const [active, setActive] = useState(0);
  return (
    <div>
      <div
        role="tablist"
        className="inline-flex gap-1 rounded-xl border border-line bg-surface p-1"
      >
        {tabs.map((t, i) => (
          <button
            key={t.label}
            role="tab"
            aria-selected={active === i}
            onClick={() => setActive(i)}
            className={cn(
              "relative rounded-lg px-4 py-2 text-sm font-medium transition-colors",
              active === i ? "text-ink" : "text-ink-3 hover:text-ink",
            )}
          >
            {active === i && (
              <motion.span
                layoutId="tab-pill"
                className="absolute inset-0 rounded-lg bg-surface-2"
                transition={{ type: "spring", stiffness: 380, damping: 30 }}
              />
            )}
            <span className="relative z-10">{t.label}</span>
          </button>
        ))}
      </div>
      <div role="tabpanel" className="mt-5 text-ink-2">
        {tabs[active].content}
      </div>
    </div>
  );
}
```

---

## Floating action button

A persistent contact/chat/quick-action button. Set `href` to any channel (`https://wa.me/…`,
`mailto:`, `tel:`, or an `onClick` to open a chat widget). For a **glowing/pulsing** FAB, see
[animated-components.md](../animated-components.md).

```tsx
// components/fab.tsx
import Link from "next/link";
export function Fab({
  href,
  label = "Chat with us",
}: {
  href: string;
  label?: string;
}) {
  return (
    <Link
      href={href}
      target="_blank"
      rel="noopener noreferrer"
      aria-label={label}
      className="group fixed bottom-6 right-6 z-40 inline-flex h-14 items-center gap-2 rounded-full bg-accent pl-4 pr-5 text-accent-ink shadow-lg shadow-accent/30 transition-transform duration-300 hover:scale-105 active:scale-95"
    >
      <svg
        className="size-6"
        viewBox="0 0 24 24"
        fill="none"
        stroke="currentColor"
        strokeWidth="2"
      >
        <path d="M21 11.5a8.38 8.38 0 0 1-.9 3.8 8.5 8.5 0 0 1-7.6 4.7 8.38 8.38 0 0 1-3.8-.9L3 21l1.9-5.7a8.38 8.38 0 0 1-.9-3.8 8.5 8.5 0 0 1 4.7-7.6 8.38 8.38 0 0 1 3.8-.9h.5a8.48 8.48 0 0 1 8 8v.5Z" />
      </svg>
      <span className="max-w-0 overflow-hidden whitespace-nowrap text-sm font-semibold transition-all duration-300 group-hover:max-w-[10rem]">
        {label}
      </span>
    </Link>
  );
}
```

---

## Announcement bar

Dismissible top-of-page strip for launches / promos. Render above the header.

```tsx
// components/announcement-bar.tsx
"use client";
import { useState } from "react";
import Link from "next/link";
export function AnnouncementBar({ href = "#", children }: { href?: string; children: React.ReactNode }) {
  const [show, setShow] = useState(true);
  if (!show) return null;
  return (
    <div className="relative bg-ink text-bg">
      <div className="mx-auto flex max-w-7xl items-center justify-center gap-2 px-10 py-2.5 text-center text-sm">
        <Link href={href} className="font-medium hover:underline">{children}</Link>
        <button onClick={() => setShow(false)} aria-label="Dismiss announcement"
          className="absolute right-3 grid size-7 place-items-center rounded-md hover:bg-bg/15">
          <svg className="size-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"><path d="M18 6 6 18M6 6l12 12" /></svg>
        </button>
      </div>
    </div>
  );
}
```

---

## Alert / inline message

```tsx
// components/ui/alert.tsx
import { cn } from "@/lib/cn";
const tones = {
  info: { box: "border-accent/25 bg-accent/5", dot: "bg-accent" },
  success: { box: "border-emerald-500/25 bg-emerald-500/5", dot: "bg-emerald-500" },
  warning: { box: "border-amber-500/25 bg-amber-500/5", dot: "bg-amber-500" },
  danger: { box: "border-red-500/25 bg-red-500/5", dot: "bg-red-500" },
} as const;
export function Alert({ tone = "info", title, children }:
  { tone?: keyof typeof tones; title?: string; children: React.ReactNode }) {
  return (
    <div role="alert" className={cn("flex gap-3 rounded-xl border p-4", tones[tone].box)}>
      <span aria-hidden className={cn("mt-1.5 size-2 shrink-0 rounded-full", tones[tone].dot)} />
      <div>
        {title && <p className="text-sm font-semibold text-ink">{title}</p>}
        <div className="text-sm text-ink-3">{children}</div>
      </div>
    </div>
  );
}
```

---

## Skeleton loader

Pair with React Suspense / streaming for anything slower than ~300ms.

```tsx
// components/ui/skeleton.tsx
import { cn } from "@/lib/cn";
export function Skeleton({ className }: { className?: string }) {
  return <div className={cn("animate-pulse rounded-md bg-surface-2", className)} />;
}
export function CardSkeleton() {
  return (
    <div className="rounded-2xl border border-line bg-surface p-6">
      <Skeleton className="size-11 rounded-xl" />
      <Skeleton className="mt-4 h-5 w-2/3" />
      <Skeleton className="mt-2 h-4 w-full" />
      <Skeleton className="mt-1.5 h-4 w-4/5" />
    </div>
  );
}
```

---

## Steps / process indicator

```tsx
// components/steps.tsx
import { cn } from "@/lib/cn";
export function Steps({ steps, current }: { steps: string[]; current: number }) {
  return (
    <ol className="flex items-center gap-2">
      {steps.map((s, i) => {
        const done = i < current, active = i === current;
        return (
          <li key={s} className="flex flex-1 items-center gap-2">
            <span className={cn("grid size-8 shrink-0 place-items-center rounded-full border text-sm font-semibold",
              done ? "border-accent bg-accent text-accent-ink"
                   : active ? "border-accent text-accent" : "border-line text-ink-3")}>
              {done
                ? <svg className="size-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="3"><path d="m5 13 4 4L19 7" /></svg>
                : i + 1}
            </span>
            <span className={cn("hidden text-sm sm:block", active ? "font-medium text-ink" : "text-ink-3")}>{s}</span>
            {i < steps.length - 1 && <span className={cn("h-px flex-1", done ? "bg-accent" : "bg-line")} />}
          </li>
        );
      })}
    </ol>
  );
}
```

---

## Empty state

```tsx
// components/empty-state.tsx
import { Button } from "@/components/ui/button";
export function EmptyState({ title, description, actionLabel }:
  { title: string; description: string; actionLabel?: string }) {
  return (
    <div className="flex flex-col items-center justify-center rounded-2xl border border-dashed border-line-2 bg-surface px-6 py-16 text-center">
      <div className="grid size-12 place-items-center rounded-2xl bg-surface-2 text-ink-3">
        <svg className="size-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="1.8"><path d="M3 7l9-4 9 4-9 4-9-4Zm0 0v10l9 4 9-4V7" /></svg>
      </div>
      <h3 className="mt-4 font-display text-lg font-semibold text-ink">{title}</h3>
      <p className="mt-1 max-w-sm text-sm text-ink-3">{description}</p>
      {actionLabel && <Button className="mt-5">{actionLabel}</Button>}
    </div>
  );
}
```

---

## Pagination

```tsx
// components/ui/pagination.tsx
import Link from "next/link";
import { cn } from "@/lib/cn";
export function Pagination({ page, totalPages, hrefFor }:
  { page: number; totalPages: number; hrefFor: (p: number) => string }) {
  const pages = Array.from({ length: totalPages }, (_, i) => i + 1)
    .filter((p) => p === 1 || p === totalPages || Math.abs(p - page) <= 1);
  const arrow = "grid size-9 place-items-center rounded-lg border border-line text-ink-2 hover:bg-surface-2";
  return (
    <nav aria-label="Pagination" className="flex items-center justify-center gap-1">
      <Link href={hrefFor(Math.max(1, page - 1))} aria-label="Previous page"
        className={cn(arrow, page === 1 && "pointer-events-none opacity-40")}>
        <svg className="size-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"><path d="m15 18-6-6 6-6" /></svg>
      </Link>
      {pages.map((p, i) => (
        <span key={p} className="flex items-center gap-1">
          {i > 0 && p - pages[i - 1] > 1 && <span className="px-1 text-ink-3">…</span>}
          <Link href={hrefFor(p)} aria-current={p === page ? "page" : undefined}
            className={cn("grid size-9 place-items-center rounded-lg border text-sm font-medium tabular-nums",
              p === page ? "border-accent bg-accent/10 text-accent" : "border-line text-ink-2 hover:bg-surface-2")}>
            {p}
          </Link>
        </span>
      ))}
      <Link href={hrefFor(Math.min(totalPages, page + 1))} aria-label="Next page"
        className={cn(arrow, page === totalPages && "pointer-events-none opacity-40")}>
        <svg className="size-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"><path d="m9 18 6-6-6-6" /></svg>
      </Link>
    </nav>
  );
}
```

---

## Avatar & avatar group

```tsx
// components/ui/avatar.tsx
import Image from "next/image";
export function Avatar({ src, name, size = 40 }: { src?: string; name: string; size?: number }) {
  const initials = name.split(" ").map((n) => n[0]).slice(0, 2).join("").toUpperCase();
  const ring = "rounded-full object-cover ring-2 ring-bg";
  return src ? (
    <Image src={src} alt={name} width={size} height={size} className={ring} style={{ width: size, height: size }} />
  ) : (
    <span className={"grid place-items-center bg-surface-2 font-medium text-ink-2 " + ring}
      style={{ width: size, height: size, fontSize: size * 0.38 }} aria-label={name}>{initials}</span>
  );
}
export function AvatarGroup({ people }: { people: { src?: string; name: string }[] }) {
  return <div className="flex -space-x-3">{people.map((p) => <Avatar key={p.name} {...p} />)}</div>;
}
```

---

## Progress bar

```tsx
// components/ui/progress.tsx
export function Progress({ value, label }: { value: number; label?: string }) {
  return (
    <div>
      {label && (
        <div className="mb-1.5 flex justify-between text-sm">
          <span className="text-ink-2">{label}</span>
          <span className="text-ink-3 tabular-nums">{value}%</span>
        </div>
      )}
      <div role="progressbar" aria-valuenow={value} aria-valuemin={0} aria-valuemax={100}
        className="h-2 overflow-hidden rounded-full bg-surface-2">
        <div className="h-full rounded-full bg-accent transition-[width] duration-500 ease-out" style={{ width: `${value}%` }} />
      </div>
    </div>
  );
}
```

---

## Rating stars

```tsx
// components/ui/rating.tsx
export function Rating({ value, max = 5 }: { value: number; max?: number }) {
  return (
    <div className="inline-flex items-center gap-0.5" aria-label={`${value} out of ${max} stars`}>
      {Array.from({ length: max }, (_, i) => (
        <svg key={i} viewBox="0 0 24 24" fill="currentColor" aria-hidden
          className={i < Math.round(value) ? "size-4 text-amber-400" : "size-4 text-line-2"}>
          <path d="m12 2 2.9 6.3 6.9.7-5.1 4.6 1.4 6.8L12 17.8 5.9 20.4l1.4-6.8L2.2 9l6.9-.7L12 2Z" />
        </svg>
      ))}
    </div>
  );
}
```

---

## Stat card with trend

```tsx
// components/stat-card.tsx
import { cn } from "@/lib/cn";
export function StatCard({ label, value, delta }: { label: string; value: string; delta?: number }) {
  const up = (delta ?? 0) >= 0;
  return (
    <div className="rounded-2xl border border-line bg-surface p-5">
      <p className="text-sm text-ink-3">{label}</p>
      <div className="mt-1 flex items-end justify-between">
        <span className="font-display text-3xl font-bold tabular-nums text-ink">{value}</span>
        {delta !== undefined && (
          <span className={cn("flex items-center gap-1 text-sm font-medium", up ? "text-emerald-500" : "text-red-500")}>
            <svg className="size-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"
              style={{ transform: up ? "" : "rotate(180deg)" }}><path d="M12 19V5M5 12l7-7 7 7" /></svg>
            {Math.abs(delta)}%
          </span>
        )}
      </div>
    </div>
  );
}
```

---

## Data table (sortable, responsive)

Client-sortable table with accessible `aria-sort`. For 50+ rows, virtualize (see [ux-rules.md](../ux-rules.md)).

```tsx
// components/ui/data-table.tsx
"use client";
import { useState } from "react";
import { cn } from "@/lib/cn";
type Row = Record<string, string | number>;
export function DataTable({ columns, rows }:
  { columns: { key: string; label: string }[]; rows: Row[] }) {
  const [sort, setSort] = useState<{ key: string; dir: 1 | -1 } | null>(null);
  const sorted = sort ? [...rows].sort((a, b) => (a[sort.key] > b[sort.key] ? 1 : -1) * sort.dir) : rows;
  return (
    <div className="overflow-x-auto rounded-2xl border border-line">
      <table className="w-full text-left text-sm">
        <thead className="border-b border-line bg-surface-2 text-ink-2">
          <tr>
            {columns.map((c) => (
              <th key={c.key} scope="col" className="px-4 py-3 font-semibold"
                aria-sort={sort?.key === c.key ? (sort.dir === 1 ? "ascending" : "descending") : "none"}>
                <button onClick={() => setSort((s) => ({ key: c.key, dir: s?.key === c.key && s.dir === 1 ? -1 : 1 }))}
                  className="inline-flex items-center gap-1 hover:text-ink">
                  {c.label}
                  <svg viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"
                    className={cn("size-3.5 transition-opacity", sort?.key === c.key ? "opacity-100" : "opacity-30")}
                    style={{ transform: sort?.key === c.key && sort.dir === -1 ? "rotate(180deg)" : "" }}><path d="m18 15-6-6-6 6" /></svg>
                </button>
              </th>
            ))}
          </tr>
        </thead>
        <tbody className="divide-y divide-line">
          {sorted.map((r, i) => (
            <tr key={i} className="transition-colors hover:bg-surface-2/50">
              {columns.map((c) => <td key={c.key} className="px-4 py-3 tabular-nums text-ink-2">{r[c.key]}</td>)}
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  );
}
```
