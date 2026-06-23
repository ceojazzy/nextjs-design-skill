# Forms & Inputs

> Part of the **[component library](README.md)**. These components use the semantic tokens
> from [../../nextjs.md §2](../../nextjs.md) and the `cn()` helper from the
> [index](README.md#conventions). For animated / glow / neon variants, see
> [../animated-components.md](../animated-components.md).

---
## Forms

### Inputs

```tsx
// components/ui/field.tsx
import { cn } from "@/lib/cn";
export function Field({
  label,
  hint,
  error,
  children,
  required,
}: {
  label: string;
  hint?: string;
  error?: string;
  required?: boolean;
  children: React.ReactNode;
}) {
  return (
    <label className="block">
      <span className="mb-1.5 block text-sm font-medium text-ink">
        {label}
        {required && <span className="text-red-500"> *</span>}
      </span>
      {children}
      {hint && !error && (
        <span className="mt-1 block text-xs text-ink-3">{hint}</span>
      )}
      {error && (
        <span className="mt-1 block text-xs text-red-500" role="alert">
          {error}
        </span>
      )}
    </label>
  );
}
export const inputClass = (invalid?: boolean) =>
  cn(
    "h-11 w-full rounded-xl border bg-bg px-3.5 text-sm text-ink placeholder:text-ink-3",
    "transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-accent/40",
    invalid
      ? "border-red-500 focus-visible:border-red-500"
      : "border-line focus-visible:border-accent",
  );
```

### Contact form (client, with validation + states)

```tsx
// components/contact-form.tsx
"use client";
import { useState } from "react";
import { Field, inputClass } from "@/components/ui/field";
import { Button } from "@/components/ui/button";

type Status = "idle" | "loading" | "success" | "error";

export function ContactForm() {
  const [status, setStatus] = useState<Status>("idle");
  const [errors, setErrors] = useState<Record<string, string>>({});

  async function onSubmit(e: React.FormEvent<HTMLFormElement>) {
    e.preventDefault();
    const data = Object.fromEntries(new FormData(e.currentTarget)) as Record<
      string,
      string
    >;
    const next: Record<string, string> = {};
    if (!data.name?.trim()) next.name = "Please enter your name.";
    if (!/^[^@\s]+@[^@\s]+\.[^@\s]+$/.test(data.email ?? ""))
      next.email = "Enter a valid email.";
    if ((data.message ?? "").trim().length < 10)
      next.message = "Tell us a bit more (10+ chars).";
    setErrors(next);
    if (Object.keys(next).length) return;

    setStatus("loading");
    try {
      const res = await fetch("/api/contact", {
        method: "POST",
        body: JSON.stringify(data),
      });
      setStatus(res.ok ? "success" : "error");
    } catch {
      setStatus("error");
    }
  }

  if (status === "success") {
    return (
      <div className="rounded-2xl border border-emerald-500/30 bg-emerald-500/5 p-8 text-center">
        <p className="font-display text-xl font-semibold text-ink">
          Thanks — we'll be in touch.
        </p>
        <p className="mt-1 text-sm text-ink-3">
          We typically reply within one business day.
        </p>
      </div>
    );
  }

  return (
    <form onSubmit={onSubmit} noValidate className="grid gap-5">
      <Field label="Name" required error={errors.name}>
        <input
          name="name"
          className={inputClass(!!errors.name)}
          placeholder="Jane Doe"
        />
      </Field>
      <Field label="Email" required error={errors.email}>
        <input
          name="email"
          type="email"
          inputMode="email"
          className={inputClass(!!errors.email)}
          placeholder="jane@company.com"
        />
      </Field>
      <Field label="Message" required error={errors.message}>
        <textarea
          name="message"
          rows={5}
          className={inputClass(!!errors.message) + " h-auto py-3 resize-y"}
          placeholder="How can we help?"
        />
      </Field>
      {status === "error" && (
        <p className="text-sm text-red-500" role="alert">
          Something went wrong. Please try again.
        </p>
      )}
      <Button type="submit" loading={status === "loading"} size="lg">
        Send message
      </Button>
    </form>
  );
}
```
