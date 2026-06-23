# Overlays & Command Palette

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Overlays

### Modal / dialog

```tsx
// components/ui/modal.tsx
"use client";
import { useEffect } from "react";
import { AnimatePresence, motion } from "motion/react";

export function Modal({
  open,
  onClose,
  title,
  children,
}: {
  open: boolean;
  onClose: () => void;
  title?: string;
  children: React.ReactNode;
}) {
  useEffect(() => {
    if (!open) return;
    const onKey = (e: KeyboardEvent) => e.key === "Escape" && onClose();
    document.addEventListener("keydown", onKey);
    document.body.style.overflow = "hidden";
    return () => {
      document.removeEventListener("keydown", onKey);
      document.body.style.overflow = "";
    };
  }, [open, onClose]);

  return (
    <AnimatePresence>
      {open && (
        <div
          className="fixed inset-0 z-50 grid place-items-center p-4"
          role="dialog"
          aria-modal="true"
        >
          <motion.div
            initial={{ opacity: 0 }}
            animate={{ opacity: 1 }}
            exit={{ opacity: 0 }}
            onClick={onClose}
            className="absolute inset-0 bg-black/55 backdrop-blur-sm"
          />
          <motion.div
            initial={{ opacity: 0, scale: 0.96, y: 12 }}
            animate={{ opacity: 1, scale: 1, y: 0 }}
            exit={{ opacity: 0, scale: 0.96, y: 12 }}
            transition={{ duration: 0.2, ease: [0.22, 1, 0.36, 1] }}
            className="relative z-10 w-full max-w-md rounded-2xl border border-line-2 bg-surface p-6 shadow-2xl"
          >
            {title && (
              <h2 className="font-display text-lg font-semibold text-ink">
                {title}
              </h2>
            )}
            <div className="mt-3 text-sm text-ink-3">{children}</div>
          </motion.div>
        </div>
      )}
    </AnimatePresence>
  );
}
```

### Toast

```tsx
// components/ui/toast.tsx
"use client";
import { AnimatePresence, motion } from "motion/react";
export function Toast({ show, message }: { show: boolean; message: string }) {
  return (
    <AnimatePresence>
      {show && (
        <motion.div
          role="status"
          aria-live="polite"
          initial={{ opacity: 0, y: 20, scale: 0.95 }}
          animate={{ opacity: 1, y: 0, scale: 1 }}
          exit={{ opacity: 0, y: 20, scale: 0.95 }}
          transition={{ type: "spring", stiffness: 350, damping: 28 }}
          className="fixed bottom-6 left-1/2 z-50 -translate-x-1/2 rounded-full border border-line-2 bg-surface px-5 py-2.5 text-sm font-medium text-ink shadow-xl"
        >
          {message}
        </motion.div>
      )}
    </AnimatePresence>
  );
}
```

### Tooltip

```tsx
// components/ui/tooltip.tsx — CSS-only, no JS
export function Tooltip({
  label,
  children,
}: {
  label: string;
  children: React.ReactNode;
}) {
  return (
    <span className="group relative inline-flex">
      {children}
      <span
        role="tooltip"
        className="pointer-events-none absolute -top-2 left-1/2 -translate-x-1/2 -translate-y-full whitespace-nowrap rounded-lg bg-ink px-2.5 py-1 text-xs font-medium text-bg opacity-0 transition-opacity duration-150 group-hover:opacity-100 group-focus-within:opacity-100"
      >
        {label}
      </span>
    </span>
  );
}
```

---

## Command palette (⌘K)

```tsx
// components/command-palette.tsx
"use client";
import { useEffect, useState } from "react";
import { useRouter } from "next/navigation";
import { AnimatePresence, motion } from "motion/react";

const commands = [
  { label: "Home", href: "/" }, { label: "Pricing", href: "/pricing" },
  { label: "Docs", href: "/docs" }, { label: "Contact", href: "/contact" },
];

export function CommandPalette() {
  const [open, setOpen] = useState(false);
  const [q, setQ] = useState("");
  const router = useRouter();
  useEffect(() => {
    const onKey = (e: KeyboardEvent) => {
      if ((e.metaKey || e.ctrlKey) && e.key.toLowerCase() === "k") { e.preventDefault(); setOpen((v) => !v); }
      if (e.key === "Escape") setOpen(false);
    };
    document.addEventListener("keydown", onKey);
    return () => document.removeEventListener("keydown", onKey);
  }, []);
  const results = commands.filter((c) => c.label.toLowerCase().includes(q.toLowerCase()));
  return (
    <AnimatePresence>
      {open && (
        <div className="fixed inset-0 z-50 flex items-start justify-center p-4 pt-[20vh]" role="dialog" aria-modal="true">
          <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} exit={{ opacity: 0 }}
            onClick={() => setOpen(false)} className="absolute inset-0 bg-black/50 backdrop-blur-sm" />
          <motion.div initial={{ opacity: 0, scale: 0.97, y: -8 }} animate={{ opacity: 1, scale: 1, y: 0 }} exit={{ opacity: 0, scale: 0.97 }}
            className="relative z-10 w-full max-w-lg overflow-hidden rounded-2xl border border-line-2 bg-surface shadow-2xl">
            <input autoFocus value={q} onChange={(e) => setQ(e.target.value)} placeholder="Type a command or search…"
              aria-label="Command search"
              className="w-full border-b border-line bg-transparent px-5 py-4 text-ink placeholder:text-ink-3 focus:outline-none" />
            <ul className="max-h-72 overflow-y-auto p-2">
              {results.length === 0 && <li className="px-3 py-6 text-center text-sm text-ink-3">No results</li>}
              {results.map((c) => (
                <li key={c.href}>
                  <button onClick={() => { router.push(c.href); setOpen(false); }}
                    className="flex w-full items-center justify-between rounded-lg px-3 py-2.5 text-left text-sm text-ink hover:bg-surface-2">
                    {c.label}
                    <svg className="size-3.5 text-ink-3" viewBox="0 0 24 24" fill="none" stroke="currentColor" strokeWidth="2"><path d="M9 10 4 15l5 5M20 4v7a4 4 0 0 1-4 4H4" /></svg>
                  </button>
                </li>
              ))}
            </ul>
          </motion.div>
        </div>
      )}
    </AnimatePresence>
  );
}
```
