# Backend Components & Patterns (Next.js)

Modern **server-side** building blocks for the App Router so the UI actually *works*: Server
Actions, Route Handlers (APIs), validation, email, data fetching/caching, database, auth, rate
limiting, webhooks, and dynamic OG images. Pairs with the frontend in
[components/](components/README.md) and the pages in [page-patterns.md](page-patterns.md).

> **Targets Next.js 15/16 + React 19.** Dynamic APIs are **async**: `await cookies()`,
> `await headers()`, and route/page `params` are **Promises**. Keep secrets server-only; validate
> every input; never trust the client.

**Setup (only what you use)**
```bash
npm i zod              # validation (recommended everywhere)
npm i resend           # transactional email (or: nodemailer)
npm i drizzle-orm      # typed SQL (or: prisma) — optional
```

**Contents**
- [When to use](#when-to-use) · [Mental model](#mental-model-actions-vs-route-handlers)
- [Validation (Zod)](#validation-zod)
- [Server Actions](#server-actions) · [Wire a form to an action](#wire-a-form-to-an-action-react-19)
- [Route Handlers (API)](#route-handlers-api) · [JSON response helper](#json-response-helper)
- [Email](#sending-email) · [Newsletter](#newsletter-subscribe)
- [Data layer & caching](#data-layer--caching) · [Database quickstart](#database-quickstart-drizzle)
- [Auth-lite + middleware](#auth-lite-session--middleware)
- [Rate limiting](#rate-limiting) · [Webhooks](#webhooks)
- [Dynamic OG image](#dynamic-og-image)
- [Env & secrets](#env--secrets) · [Security checklist](#security-checklist)

---

## When to use

Reach for this file when the UI needs to **do** something: submit a contact form, subscribe to a
newsletter, search/filter server data, load a collection for [page-patterns.md](page-patterns.md),
gate a dashboard, accept a webhook, or expose an API.

---

## Mental model: Actions vs Route Handlers

| Use a **Server Action** when… | Use a **Route Handler** when… |
| --- | --- |
| A form/button mutates data from **your own** UI | You need a public/REST **endpoint** (mobile, third-party, webhooks) |
| You want progressive enhancement (works without JS) | You return non-HTML (JSON, files, streams, OG images) |
| Co-located with the component/page | You need full control of `Request`/`Response`, methods, headers |
| `"use server"` function | `export async function GET/POST(req: Request)` in `route.ts` |

Both must **validate input** and run server-only secrets. Default to **Server Actions** for app
forms; add **Route Handlers** for APIs/integrations.

---

## Validation (Zod)

Define schemas once, reuse on server (and optionally client). Never skip server-side validation.

```ts
// lib/schemas.ts
import { z } from "zod";

export const contactSchema = z.object({
  name: z.string().min(1, "Please enter your name.").max(80),
  email: z.string().email("Enter a valid email."),
  message: z.string().min(10, "Tell us a bit more (10+ chars).").max(2000),
  // honeypot: must stay empty (simple bot trap)
  company: z.string().max(0).optional(),
});
export type ContactInput = z.infer<typeof contactSchema>;

export const subscribeSchema = z.object({ email: z.string().email() });
```

---

## Server Actions

A typed action that validates, sends email, and returns a serializable state for the UI. Designed
for React 19 `useActionState`.

```ts
// app/actions/contact.ts
"use server";

import { contactSchema } from "@/lib/schemas";
import { sendContactEmail } from "@/lib/email";
import { ratelimit } from "@/lib/ratelimit";
import { headers } from "next/headers";

export type ContactState = {
  ok: boolean;
  errors?: Record<string, string>;
  message?: string;
};

export async function submitContact(_prev: ContactState, formData: FormData): Promise<ContactState> {
  // 1) basic rate limit by IP
  const ip = (await headers()).get("x-forwarded-for")?.split(",")[0] ?? "unknown";
  if (!(await ratelimit(ip))) return { ok: false, message: "Too many requests — try again shortly." };

  // 2) validate
  const parsed = contactSchema.safeParse(Object.fromEntries(formData));
  if (!parsed.success) {
    const errors: Record<string, string> = {};
    for (const issue of parsed.error.issues) errors[issue.path[0] as string] = issue.message;
    return { ok: false, errors };
  }
  if (parsed.data.company) return { ok: true, message: "Thanks!" }; // honeypot tripped → silently pass

  // 3) do the work (never trust client; this runs only on the server)
  try {
    await sendContactEmail(parsed.data);
    return { ok: true, message: "Thanks — we'll be in touch." };
  } catch {
    return { ok: false, message: "Something went wrong. Please try again." };
  }
}
```

### Wire a form to an action (React 19)

`useActionState` gives `[state, formAction, isPending]`; `useFormStatus` reads pending inside the
button. Works without JS (progressive enhancement); enhances when hydrated.

```tsx
// components/contact-form.tsx
"use client";
import { useActionState } from "react";
import { useFormStatus } from "react-dom";
import { submitContact, type ContactState } from "@/app/actions/contact";
import { Field, inputClass } from "@/components/ui/field";

const initial: ContactState = { ok: false };

function SubmitButton() {
  const { pending } = useFormStatus();
  return (
    <button type="submit" disabled={pending}
      className="inline-flex h-11 items-center justify-center rounded-xl bg-accent px-6 text-sm font-semibold text-accent-ink transition-[filter] hover:brightness-110 disabled:opacity-50">
      {pending ? "Sending…" : "Send message"}
    </button>
  );
}

export function ContactForm() {
  const [state, formAction] = useActionState(submitContact, initial);
  if (state.ok && state.message) {
    return (
      <div className="rounded-2xl border border-emerald-500/30 bg-emerald-500/5 p-8 text-center">
        <p className="font-display text-xl font-semibold text-ink">{state.message}</p>
      </div>
    );
  }
  return (
    <form action={formAction} className="grid gap-5" noValidate>
      <input type="text" name="company" tabIndex={-1} autoComplete="off" className="hidden" aria-hidden />
      <Field label="Name" required error={state.errors?.name}>
        <input name="name" className={inputClass(!!state.errors?.name)} placeholder="Jane Doe" />
      </Field>
      <Field label="Email" required error={state.errors?.email}>
        <input name="email" type="email" inputMode="email" autoComplete="email" className={inputClass(!!state.errors?.email)} placeholder="jane@company.com" />
      </Field>
      <Field label="Message" required error={state.errors?.message}>
        <textarea name="message" rows={5} className={inputClass(!!state.errors?.message) + " h-auto resize-y py-3"} placeholder="How can we help?" />
      </Field>
      {state.message && !state.ok && <p className="text-sm text-red-500" role="alert">{state.message}</p>}
      <SubmitButton />
    </form>
  );
}
```

> For mutations that change displayed data, call `revalidatePath("/path")` or
> `revalidateTag("tag")` from `next/cache` at the end of the action.

---

## Route Handlers (API)

A typed POST endpoint (e.g. for external clients or when you prefer fetch over actions).

```ts
// app/api/contact/route.ts
import { contactSchema } from "@/lib/schemas";
import { sendContactEmail } from "@/lib/email";
import { json, fail } from "@/lib/api";

export async function POST(req: Request) {
  let body: unknown;
  try { body = await req.json(); } catch { return fail("Invalid JSON", 400); }

  const parsed = contactSchema.safeParse(body);
  if (!parsed.success) return fail("Validation failed", 422, parsed.error.flatten().fieldErrors);

  try {
    await sendContactEmail(parsed.data);
    return json({ ok: true }, 201);
  } catch {
    return fail("Failed to send", 500);
  }
}
```

```ts
// app/api/services/route.ts — a simple GET with caching headers
import { services } from "@/app/services/services-data";
export async function GET() {
  return Response.json(services, {
    headers: { "Cache-Control": "public, s-maxage=3600, stale-while-revalidate=86400" },
  });
}
```

Dynamic segment + async params:
```ts
// app/api/services/[slug]/route.ts
import { getService } from "@/app/services/services-data";
export async function GET(_req: Request, { params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params;
  const service = getService(slug);
  return service ? Response.json(service) : Response.json({ error: "Not found" }, { status: 404 });
}
```

### JSON response helper

A consistent success/error envelope.

```ts
// lib/api.ts
export function json<T>(data: T, status = 200) {
  return Response.json(data, { status });
}
export function fail(error: string, status = 400, fields?: unknown) {
  return Response.json({ ok: false, error, fields }, { status });
}
```

---

## Sending email

Resend (modern, simple). Keep the API key server-only.

```ts
// lib/email.ts
import "server-only";
import { Resend } from "resend";
import type { ContactInput } from "@/lib/schemas";

const resend = new Resend(process.env.RESEND_API_KEY);

export async function sendContactEmail(input: ContactInput) {
  const { error } = await resend.emails.send({
    from: "Website <hello@yourdomain.com>",
    to: process.env.CONTACT_INBOX!,
    replyTo: input.email,
    subject: `New message from ${input.name}`,
    text: `From: ${input.name} <${input.email}>\n\n${input.message}`,
    // html: render a React email template for branded mail
  });
  if (error) throw new Error(error.message);
}
```

> **nodemailer** alternative (SMTP): create a transport with `nodemailer.createTransport({...})`
> and `transport.sendMail(...)`. Same server-only rule. For branded HTML, use `@react-email/components`.

---

## Newsletter subscribe

```ts
// app/actions/subscribe.ts
"use server";
import { subscribeSchema } from "@/lib/schemas";
import "server-only";

export async function subscribe(_prev: { ok: boolean; message?: string }, formData: FormData) {
  const parsed = subscribeSchema.safeParse(Object.fromEntries(formData));
  if (!parsed.success) return { ok: false, message: "Enter a valid email." };
  try {
    // POST to your ESP (Resend Audiences, Mailchimp, Buttondown, ConvertKit…)
    await fetch("https://api.your-esp.com/subscribers", {
      method: "POST",
      headers: { Authorization: `Bearer ${process.env.ESP_KEY}`, "Content-Type": "application/json" },
      body: JSON.stringify({ email: parsed.data.email }),
    });
    return { ok: true, message: "You're in — check your inbox to confirm." };
  } catch {
    return { ok: false, message: "Could not subscribe. Try again." };
  }
}
```

Use double opt-in where required (GDPR/CAN-SPAM). The newsletter form in
[components/footers.md › Mega footer](components/footers.md#footers) can `action={subscribe}` the same way as the
contact form.

---

## Data layer & caching

Centralize reads so pages stay clean and caching is consistent.

```ts
// lib/data/services.ts
import "server-only";
import { unstable_cache } from "next/cache";

async function fetchServices() {
  // replace with DB/CMS/fetch; example with an external API:
  const res = await fetch("https://cms.example.com/services", { next: { tags: ["services"] } });
  if (!res.ok) throw new Error("Failed to load services");
  return res.json();
}

// Cache the result; bust it with revalidateTag("services")
export const getServices = unstable_cache(fetchServices, ["services"], { tags: ["services"], revalidate: 3600 });
```

```ts
// revalidate after a mutation (in a Server Action or webhook)
import { revalidateTag, revalidatePath } from "next/cache";
revalidateTag("services");        // refresh anything tagged "services"
revalidatePath("/services");      // or a specific route
```

**Fetching rules of thumb**
- Fetch in **Server Components** (no client round-trips, no exposed keys).
- `fetch(url, { next: { revalidate: N } })` for ISR; `{ cache: "no-store" }` for always-fresh.
- Co-locate per-feature in `lib/data/*`; return typed data; throw on failure (let `error.tsx` catch).
- Stream slow data with `<Suspense fallback={<CardSkeleton/>}>` from [components/ui-elements.md](components/ui-elements.md#skeleton-loader).

---

## Database quickstart (Drizzle)

Typed SQL with minimal overhead. (Prisma is an equally good alternative.)

```ts
// db/schema.ts
import { pgTable, serial, text, timestamp } from "drizzle-orm/pg-core";
export const messages = pgTable("messages", {
  id: serial("id").primaryKey(),
  name: text("name").notNull(),
  email: text("email").notNull(),
  body: text("body").notNull(),
  createdAt: timestamp("created_at").defaultNow().notNull(),
});
```

```ts
// db/index.ts
import "server-only";
import { drizzle } from "drizzle-orm/node-postgres";
import { Pool } from "pg";
const pool = new Pool({ connectionString: process.env.DATABASE_URL });
export const db = drizzle(pool);
```

```ts
// usage inside a Server Action
import { db } from "@/db";
import { messages } from "@/db/schema";
await db.insert(messages).values({ name, email, body });
const recent = await db.select().from(messages).limit(20);
```

---

## Auth-lite (session + middleware)

A minimal cookie session for gating areas. For production auth (OAuth, magic links, RBAC) use
**Auth.js (NextAuth)** or **Clerk** — but the cookie + middleware shape is the same.

```ts
// lib/session.ts
import "server-only";
import { cookies } from "next/headers";

const COOKIE = "session";

export async function createSession(userId: string) {
  // sign a real JWT in production (e.g. `jose`); demo stores the id
  (await cookies()).set(COOKIE, userId, {
    httpOnly: true, secure: process.env.NODE_ENV === "production",
    sameSite: "lax", path: "/", maxAge: 60 * 60 * 24 * 7,
  });
}
export async function getSession() {
  return (await cookies()).get(COOKIE)?.value ?? null;
}
export async function destroySession() {
  (await cookies()).delete(COOKIE);
}
```

```ts
// middleware.ts — protect /app/*
import { NextResponse, type NextRequest } from "next/server";
export function middleware(req: NextRequest) {
  const session = req.cookies.get("session")?.value;
  if (!session) {
    const url = new URL("/login", req.url);
    url.searchParams.set("next", req.nextUrl.pathname);
    return NextResponse.redirect(url);
  }
  return NextResponse.next();
}
export const config = { matcher: ["/app/:path*"] };
```

```ts
// app/actions/auth.ts — sign in / out actions
"use server";
import { createSession, destroySession } from "@/lib/session";
import { redirect } from "next/navigation";
export async function login(formData: FormData) {
  const email = String(formData.get("email"));
  // verify credentials against your DB here…
  await createSession(email);
  redirect("/app");
}
export async function logout() {
  await destroySession();
  redirect("/login");
}
```

---

## Rate limiting

A tiny in-memory limiter (single instance / dev). For production/serverless use **Upstash
Ratelimit** (durable across instances).

```ts
// lib/ratelimit.ts
const hits = new Map<string, { count: number; reset: number }>();
export async function ratelimit(key: string, limit = 5, windowMs = 60_000) {
  const now = Date.now();
  const entry = hits.get(key);
  if (!entry || now > entry.reset) { hits.set(key, { count: 1, reset: now + windowMs }); return true; }
  if (entry.count >= limit) return false;
  entry.count++;
  return true;
}
```
```ts
// production: Upstash (works on edge/serverless)
// import { Ratelimit } from "@upstash/ratelimit";
// import { Redis } from "@upstash/redis";
// export const limiter = new Ratelimit({ redis: Redis.fromEnv(), limiter: Ratelimit.slidingWindow(5, "1 m") });
// const { success } = await limiter.limit(ip);
```

---

## Webhooks

Verify the signature against the **raw** body before trusting a webhook.

```ts
// app/api/webhooks/stripe/route.ts
import Stripe from "stripe";
const stripe = new Stripe(process.env.STRIPE_SECRET_KEY!);

export async function POST(req: Request) {
  const sig = req.headers.get("stripe-signature");
  const raw = await req.text(); // raw body required for verification
  let event: Stripe.Event;
  try {
    event = stripe.webhooks.constructEvent(raw, sig!, process.env.STRIPE_WEBHOOK_SECRET!);
  } catch {
    return new Response("Invalid signature", { status: 400 });
  }
  if (event.type === "checkout.session.completed") {
    // fulfill the order… then revalidate any affected pages
  }
  return Response.json({ received: true });
}
```

---

## Dynamic OG image

Generate per-page social images at the edge with `next/og` (great for blog/services detail).

```tsx
// app/services/[slug]/opengraph-image.tsx
import { ImageResponse } from "next/og";
import { getService } from "../services-data";
export const size = { width: 1200, height: 630 };
export const contentType = "image/png";

export default async function OG({ params }: { params: Promise<{ slug: string }> }) {
  const { slug } = await params;
  const service = getService(slug);
  return new ImageResponse(
    (
      <div style={{ width: "100%", height: "100%", display: "flex", flexDirection: "column", justifyContent: "center",
        padding: 80, background: "#0b0d12", color: "#fff", fontFamily: "sans-serif" }}>
        <div style={{ fontSize: 30, color: "#74a6f2" }}>Acme · Services</div>
        <div style={{ fontSize: 72, fontWeight: 700, marginTop: 16 }}>{service?.title ?? "Service"}</div>
        <div style={{ fontSize: 32, color: "#979eb1", marginTop: 12 }}>{service?.summary}</div>
      </div>
    ),
    size,
  );
}
```

---

## Env & secrets

- Put secrets in `.env.local` (git-ignored). Only `NEXT_PUBLIC_*` vars reach the browser.
- Mark server-only modules with `import "server-only";` so they can't be imported into client code.
- Read secrets **inside** server functions; never pass them as props to client components.

```ts
// lib/env.ts — validate env at boot (fail fast)
import { z } from "zod";
const schema = z.object({
  RESEND_API_KEY: z.string().min(1),
  CONTACT_INBOX: z.string().email(),
  DATABASE_URL: z.string().url().optional(),
});
export const env = schema.parse(process.env);
```

---

## Security checklist

- [ ] **Validate every input** server-side with Zod (actions *and* route handlers).
- [ ] Secrets are server-only (`import "server-only"`, no `NEXT_PUBLIC_` for keys).
- [ ] **Rate limit** public endpoints (contact, subscribe, login, search).
- [ ] Add a **honeypot** and/or CAPTCHA to public forms; never email raw unescaped input as HTML.
- [ ] Verify **webhook signatures** against the raw body before acting.
- [ ] Auth cookies: `httpOnly`, `secure` (prod), `sameSite: "lax"`; sign tokens (JWT via `jose`).
- [ ] Authorize on the server for every protected action — don't rely on hidden UI.
- [ ] Use parameterized queries / an ORM (no string-concatenated SQL).
- [ ] `revalidateTag` / `revalidatePath` after mutations so the UI stays correct.
- [ ] Return safe error messages to the client; log details server-side only.
