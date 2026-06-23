# Buttons, Badges & Cards

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Buttons

A single `Button` with variants + sizes, plus an async loading state.

```tsx
// components/ui/button.tsx
import { cn } from "@/lib/cn";
import { forwardRef } from "react";

type Variant = "primary" | "secondary" | "ghost" | "outline" | "danger";
type Size = "sm" | "md" | "lg";

const variants: Record<Variant, string> = {
  primary: "bg-accent text-accent-ink hover:brightness-110 shadow-sm",
  secondary: "bg-surface-2 text-ink hover:bg-surface-2/70 border border-line",
  ghost: "text-ink hover:bg-surface-2",
  outline:
    "border border-line-2 text-ink hover:border-accent hover:text-accent",
  danger: "bg-red-500 text-white hover:bg-red-600",
};
const sizes: Record<Size, string> = {
  sm: "h-9 px-3.5 text-sm rounded-lg",
  md: "h-11 px-5 text-[0.95rem] rounded-xl",
  lg: "h-13 px-7 text-base rounded-xl",
};

export interface ButtonProps extends React.ButtonHTMLAttributes<HTMLButtonElement> {
  variant?: Variant;
  size?: Size;
  loading?: boolean;
}

export const Button = forwardRef<HTMLButtonElement, ButtonProps>(
  (
    {
      className,
      variant = "primary",
      size = "md",
      loading,
      children,
      disabled,
      ...props
    },
    ref,
  ) => (
    <button
      ref={ref}
      disabled={disabled || loading}
      className={cn(
        "inline-flex items-center justify-center gap-2 font-medium cursor-pointer select-none",
        "transition-[transform,filter,background-color,border-color] duration-200 ease-out",
        "focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-accent focus-visible:ring-offset-2 focus-visible:ring-offset-bg",
        "active:scale-[.97] disabled:opacity-50 disabled:pointer-events-none",
        variants[variant],
        sizes[size],
        className,
      )}
      {...props}
    >
      {loading && (
        <svg
          className="size-4 animate-spin"
          viewBox="0 0 24 24"
          fill="none"
          aria-hidden
        >
          <circle
            cx="12"
            cy="12"
            r="10"
            stroke="currentColor"
            strokeWidth="3"
            className="opacity-25"
          />
          <path
            d="M22 12a10 10 0 0 1-10 10"
            stroke="currentColor"
            strokeWidth="3"
            strokeLinecap="round"
          />
        </svg>
      )}
      {children}
    </button>
  ),
);
Button.displayName = "Button";
```

Usage: `<Button>Get started</Button>` · `<Button variant="outline" size="lg">Learn more</Button>`
· `<Button loading>Saving…</Button>`. For a **shimmer/neon** button see [animated-components.md](../animated-components.md).

---

## Badges & pills

```tsx
// components/ui/badge.tsx
import { cn } from "@/lib/cn";
const tones = {
  neutral: "bg-surface-2 text-ink-2 border-line",
  accent: "bg-accent/10 text-accent border-accent/20",
  success:
    "bg-emerald-500/10 text-emerald-600 dark:text-emerald-400 border-emerald-500/20",
  warning:
    "bg-amber-500/10 text-amber-600 dark:text-amber-400 border-amber-500/20",
  danger: "bg-red-500/10 text-red-600 dark:text-red-400 border-red-500/20",
} as const;
export function Badge({
  tone = "neutral",
  className,
  children,
}: {
  tone?: keyof typeof tones;
  className?: string;
  children: React.ReactNode;
}) {
  return (
    <span
      className={cn(
        "inline-flex items-center gap-1.5 rounded-full border px-2.5 py-0.5 text-xs font-medium",
        tones[tone],
        className,
      )}
    >
      {children}
    </span>
  );
}
```

A common "eyebrow" pill above headings: `<Badge tone="accent"><Dot/> EU-Registered</Badge>`.

---

## Cards

Base card + a feature card with icon + an image card. All token-driven.

```tsx
// components/ui/card.tsx
import { cn } from "@/lib/cn";
export function Card({
  className,
  children,
}: {
  className?: string;
  children: React.ReactNode;
}) {
  return (
    <div
      className={cn(
        "rounded-2xl border border-line bg-surface p-6",
        "shadow-[0_1px_2px_rgba(0,0,0,.04)] transition-[transform,box-shadow,border-color] duration-300 ease-out",
        "hover:-translate-y-1 hover:border-line-2 hover:shadow-[0_12px_40px_-12px_rgba(0,0,0,.18)]",
        className,
      )}
    >
      {children}
    </div>
  );
}
```

```tsx
// components/feature-card.tsx
import { Card } from "@/components/ui/card";
export function FeatureCard({
  icon,
  title,
  children,
}: {
  icon: React.ReactNode;
  title: string;
  children: React.ReactNode;
}) {
  return (
    <Card>
      <div className="mb-4 inline-flex size-11 items-center justify-center rounded-xl bg-accent/10 text-accent">
        {icon}
      </div>
      <h3 className="font-display text-lg font-semibold text-ink">{title}</h3>
      <p className="mt-2 text-sm leading-relaxed text-ink-3">{children}</p>
    </Card>
  );
}
```

```tsx
// components/image-card.tsx
import Image from "next/image";
import Link from "next/link";
export function ImageCard({
  href,
  src,
  alt,
  kicker,
  title,
}: {
  href: string;
  src: string;
  alt: string;
  kicker: string;
  title: string;
}) {
  return (
    <Link
      href={href}
      className="group block overflow-hidden rounded-2xl border border-line bg-surface"
    >
      <div className="relative aspect-[4/3] overflow-hidden">
        <Image
          src={src}
          alt={alt}
          fill
          sizes="(max-width:768px) 100vw, 33vw"
          className="object-cover transition-transform duration-500 ease-out group-hover:scale-105"
        />
        <div className="absolute inset-0 bg-gradient-to-t from-black/50 to-transparent opacity-0 transition-opacity duration-300 group-hover:opacity-100" />
      </div>
      <div className="p-5">
        <p className="text-xs font-medium uppercase tracking-wider text-accent">
          {kicker}
        </p>
        <h3 className="mt-1 font-display text-lg font-semibold text-ink">
          {title}
        </h3>
      </div>
    </Link>
  );
}
```

For **glass, neon, spotlight, gradient-border, and tilt** cards → [animated-components.md](../animated-components.md).
