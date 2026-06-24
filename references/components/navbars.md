# Navbars & Navigation

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Navbars

### Desktop nav with animated active underline

```tsx
// components/nav-links.tsx
"use client";
import Link from "next/link";
import { usePathname } from "next/navigation";
import { motion } from "motion/react";

const items = [
  { label: "Home", href: "/" },
  { label: "Work", href: "/work" },
  { label: "Services", href: "/services" },
  { label: "Contact", href: "/contact" },
];

export function NavLinks() {
  const pathname = usePathname();
  return (
    <nav className="hidden items-center gap-7 md:flex">
      {items.map((it) => {
        const active = pathname === it.href;
        return (
          <Link
            key={it.href}
            href={it.href}
            className="relative py-1.5 text-sm font-semibold text-ink-2 transition-colors hover:text-ink data-[active=true]:text-ink"
            data-active={active}
          >
            {it.label}
            {active && (
              <motion.span
                layoutId="nav-underline"
                className="absolute inset-x-0 -bottom-0.5 h-[3px] rounded-full bg-accent"
                transition={{ type: "spring", stiffness: 380, damping: 30 }}
              />
            )}
          </Link>
        );
      })}
    </nav>
  );
}
```

### Dropdown & mega-menu

```tsx
// components/mega-menu.tsx
"use client";
import Link from "next/link";
import { useState } from "react";
import { AnimatePresence, motion } from "motion/react";

const menu = {
  Products: [
    {
      label: "Analytics",
      href: "/products/analytics",
      desc: "Real-time insights",
    },
    {
      label: "Automations",
      href: "/products/automations",
      desc: "Workflows on autopilot",
    },
    {
      label: "Integrations",
      href: "/products/integrations",
      desc: "Connect everything",
    },
    { label: "API", href: "/products/api", desc: "Build on our platform" },
  ],
  Solutions: [
    { label: "Startups", href: "/solutions/startups", desc: "Move fast" },
    {
      label: "Enterprise",
      href: "/solutions/enterprise",
      desc: "Scale safely",
    },
  ],
};

export function MegaMenu() {
  const [open, setOpen] = useState<string | null>(null);
  return (
    <nav className="hidden md:flex" onMouseLeave={() => setOpen(null)}>
      {Object.entries(menu).map(([label, links]) => (
        <div
          key={label}
          className="relative"
          onMouseEnter={() => setOpen(label)}
        >
          <button
            className="flex items-center gap-1 px-4 py-2 text-sm font-semibold text-ink-2 hover:text-ink"
            aria-expanded={open === label}
          >
            {label}
            <svg
              className="size-3.5 transition-transform"
              style={{ transform: open === label ? "rotate(180deg)" : "" }}
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              strokeWidth="2"
            >
              <path d="m6 9 6 6 6-6" />
            </svg>
          </button>
          <AnimatePresence>
            {open === label && (
              <motion.div
                initial={{ opacity: 0, y: 8 }}
                animate={{ opacity: 1, y: 0 }}
                exit={{ opacity: 0, y: 8 }}
                transition={{ duration: 0.18, ease: [0.22, 1, 0.36, 1] }}
                className="absolute left-0 top-full z-50 w-[min(92vw,28rem)] rounded-2xl border border-line-2 bg-surface-2 p-2 shadow-2xl"
              >
                <div className="grid gap-1 sm:grid-cols-2">
                  {links.map((l) => (
                    <Link
                      key={l.href}
                      href={l.href}
                      className="rounded-xl p-3 transition-colors hover:bg-surface"
                    >
                      <div className="text-sm font-semibold text-ink">
                        {l.label}
                      </div>
                      <div className="text-xs text-ink-3">{l.desc}</div>
                    </Link>
                  ))}
                </div>
              </motion.div>
            )}
          </AnimatePresence>
        </div>
      ))}
    </nav>
  );
}
```

### Mobile drawer nav

```tsx
// components/mobile-nav.tsx
"use client";
import Link from "next/link";
import { useState } from "react";
import { AnimatePresence, motion } from "motion/react";

const links = [
  { label: "Home", href: "/" },
  { label: "Work", href: "/work" },
  { label: "Services", href: "/services" },
  { label: "About", href: "/about" },
  { label: "Contact", href: "/contact" },
];

export function MobileNav() {
  const [open, setOpen] = useState(false);
  return (
    <div className="md:hidden">
      <button
        onClick={() => setOpen(true)}
        aria-label="Open menu"
        className="grid size-10 place-items-center rounded-lg text-ink hover:bg-surface-2"
      >
        <svg
          className="size-6"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          strokeWidth="2"
        >
          <path d="M4 6h16M4 12h16M4 18h16" />
        </svg>
      </button>
      <AnimatePresence>
        {open && (
          <>
            <motion.div
              initial={{ opacity: 0 }}
              animate={{ opacity: 1 }}
              exit={{ opacity: 0 }}
              onClick={() => setOpen(false)}
              className="fixed inset-0 z-50 bg-black/50 backdrop-blur-sm"
            />
            <motion.aside
              initial={{ x: "100%" }}
              animate={{ x: 0 }}
              exit={{ x: "100%" }}
              transition={{ type: "spring", stiffness: 320, damping: 34 }}
              className="fixed inset-y-0 right-0 z-50 flex w-80 max-w-[85vw] flex-col gap-1 border-l border-line bg-bg p-6"
            >
              <button
                onClick={() => setOpen(false)}
                aria-label="Close menu"
                className="mb-6 ml-auto grid size-10 place-items-center rounded-lg text-ink hover:bg-surface-2"
              >
                <svg
                  className="size-6"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  strokeWidth="2"
                >
                  <path d="M18 6 6 18M6 6l12 12" />
                </svg>
              </button>
              {links.map((l, i) => (
                <motion.div
                  key={l.href}
                  initial={{ opacity: 0, x: 20 }}
                  animate={{ opacity: 1, x: 0 }}
                  transition={{ delay: 0.05 + i * 0.05 }}
                >
                  <Link
                    href={l.href}
                    onClick={() => setOpen(false)}
                    className="block rounded-xl px-4 py-3 text-lg font-semibold text-ink hover:bg-surface-2"
                  >
                    {l.label}
                  </Link>
                </motion.div>
              ))}
            </motion.aside>
          </>
        )}
      </AnimatePresence>
    </div>
  );
}
```

### Sidebar nav (dashboard/admin)

```tsx
// components/sidebar.tsx
"use client";
import Link from "next/link";
import { usePathname } from "next/navigation";
import { cn } from "@/lib/cn";

const sections = [
  {
    title: "Overview",
    items: [
      { label: "Dashboard", href: "/app" },
      { label: "Analytics", href: "/app/analytics" },
    ],
  },
  {
    title: "Manage",
    items: [
      { label: "Projects", href: "/app/projects" },
      { label: "Team", href: "/app/team" },
      { label: "Settings", href: "/app/settings" },
    ],
  },
];

export function Sidebar() {
  const pathname = usePathname();
  return (
    <aside className="flex h-dvh w-64 flex-col gap-6 border-r border-line bg-surface p-4">
      <div className="px-2 font-display text-lg font-bold text-ink">
        Acme<span className="text-accent">.</span>
      </div>
      {sections.map((s) => (
        <div key={s.title}>
          <p className="px-3 pb-2 text-xs font-semibold uppercase tracking-wider text-ink-3">
            {s.title}
          </p>
          <nav className="flex flex-col gap-0.5">
            {s.items.map((it) => {
              const active = pathname === it.href;
              return (
                <Link
                  key={it.href}
                  href={it.href}
                  className={cn(
                    "rounded-lg px-3 py-2 text-sm font-medium transition-colors",
                    active
                      ? "bg-accent/10 text-accent"
                      : "text-ink-2 hover:bg-surface-2 hover:text-ink",
                  )}
                >
                  {it.label}
                </Link>
              );
            })}
          </nav>
        </div>
      ))}
    </aside>
  );
}
```

### Collapsible sidebar with collapse button

A sidebar that can collapse to icon-only mode. The collapse button is positioned on the right edge, centered on the border line between the logo section and the nav menu.

**Button specs:**
- Size: `size-10` (40px) with `size-5` icons
- Position: `absolute -right-4 top-16 -translate-y-1/2` (centered on the border between h-16 logo and nav)
- Shadow: `shadow-lg`
- Border: `border-line`
- Background: `bg-surface` with `hover:bg-surface-2`

```tsx
// components/collapsible-sidebar.tsx
"use client";
import Link from "next/link";
import { usePathname } from "next/navigation";
import { useState } from "react";
import { cn } from "@/lib/cn";
import { PanelLeftClose, PanelLeft } from "lucide-react";

interface NavSection {
  title: string;
  items: { label: string; href: string; icon: React.ReactNode }[];
  defaultOpen?: boolean;
}

export function CollapsibleSidebar({ sections }: { sections: NavSection[] }) {
  const pathname = usePathname();
  const [collapsed, setCollapsed] = useState(false);

  return (
    <aside
      className={cn(
        "hidden lg:fixed lg:inset-y-0 lg:left-0 lg:z-30 lg:flex lg:flex-col border-r border-line bg-surface transition-all duration-300",
        collapsed ? "lg:w-[68px]" : "lg:w-64"
      )}
    >
      {/* Logo - h-16 */}
      <div className={cn(
        "flex h-16 items-center border-b border-line px-4",
        collapsed ? "justify-center" : "gap-3"
      )}>
        <div className="grid size-9 shrink-0 place-items-center rounded-xl bg-accent text-on-accent">
          {/* Logo icon */}
        </div>
        {!collapsed && (
          <span className="font-display text-lg font-bold text-ink">Acme</span>
        )}
      </div>

      {/* Nav */}
      <nav className="flex-1 overflow-y-auto px-3 py-4">
        {/* Nav items here */}
      </nav>

      {/* Collapse toggle - centered on border between logo and nav */}
      <button
        onClick={() => setCollapsed(!collapsed)}
        aria-label={collapsed ? "Expand sidebar" : "Collapse sidebar"}
        className="absolute -right-4 top-16 z-30 grid size-10 -translate-y-1/2 place-items-center rounded-full border border-line bg-surface text-ink-3 shadow-lg transition-colors hover:bg-surface-2 hover:text-ink"
      >
        {collapsed ? <PanelLeft className="size-5" /> : <PanelLeftClose className="size-5" />}
      </button>
    </aside>
  );
}
```

**Layout integration:** Use a context to share collapsed state with the main content area:

```tsx
// Use SidebarContext to dynamically set padding
const { collapsed } = useSidebar();
<div className={cn("transition-all duration-300", collapsed ? "lg:pl-[68px]" : "lg:pl-64")}>
  {/* Main content */}
</div>
```

### Bottom tab bar (mobile app-style)

```tsx
// components/bottom-tabs.tsx
"use client";
import Link from "next/link";
import { usePathname } from "next/navigation";
import { cn } from "@/lib/cn";

const tabs = [
  { label: "Home", href: "/", icon: "M3 12 12 3l9 9M5 10v10h14V10" },
  {
    label: "Search",
    href: "/search",
    icon: "M21 21l-4.3-4.3M11 18a7 7 0 1 0 0-14 7 7 0 0 0 0 14Z",
  },
  {
    label: "Saved",
    href: "/saved",
    icon: "M19 21l-7-5-7 5V5a2 2 0 0 1 2-2h10a2 2 0 0 1 2 2Z",
  },
  {
    label: "Profile",
    href: "/profile",
    icon: "M20 21a8 8 0 1 0-16 0M12 11a4 4 0 1 0 0-8 4 4 0 0 0 0 8Z",
  },
];

export function BottomTabs() {
  const pathname = usePathname();
  return (
    <nav className="fixed inset-x-0 bottom-0 z-40 flex items-center justify-around border-t border-line bg-bg/90 pb-[env(safe-area-inset-bottom)] backdrop-blur-md md:hidden">
      {tabs.map((t) => {
        const active = pathname === t.href;
        return (
          <Link
            key={t.href}
            href={t.href}
            aria-current={active ? "page" : undefined}
            className={cn(
              "flex flex-1 flex-col items-center gap-1 py-2.5 text-[0.7rem] font-medium",
              active ? "text-accent" : "text-ink-3",
            )}
          >
            <svg
              className="size-6"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              strokeWidth="1.8"
            >
              <path d={t.icon} />
            </svg>
            {t.label}
          </Link>
        );
      })}
    </nav>
  );
}
```

### Breadcrumbs

```tsx
// components/breadcrumbs.tsx
import Link from "next/link";
export function Breadcrumbs({
  items,
}: {
  items: { label: string; href?: string }[];
}) {
  return (
    <nav
      aria-label="Breadcrumb"
      className="flex items-center gap-1.5 text-sm text-ink-3"
    >
      {items.map((it, i) => (
        <span key={i} className="flex items-center gap-1.5">
          {it.href ? (
            <Link href={it.href} className="hover:text-ink">
              {it.label}
            </Link>
          ) : (
            <span className="text-ink" aria-current="page">
              {it.label}
            </span>
          )}
          {i < items.length - 1 && (
            <span aria-hidden className="text-line-2">
              /
            </span>
          )}
        </span>
      ))}
    </nav>
  );
}
```
