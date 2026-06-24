# How to use the Next.js Modern Design skill

A self-contained design skill for building modern Next.js websites with OpenCode. It is **pure
Markdown** — no scripts, no CLI, no Python, no dependencies. The agent reads the files on demand.

---

## 1. What this is

```
nextjs-design-skill/
├── SKILL.md                          ← entry point (OpenCode reads this)
├── HOW-TO-USE.md                     ← this file
├── nextjs.md                         ← master handbook (foundations, motion, categories)
└── references/
    ├── design-systems.md             ← 30+ styles + color palettes + font pairings
    ├── components/                   ← component library (split into files)
    │   ├── README.md                 ←   index + cn() helper + map
    │   ├── primitives.md             ←   buttons, badges, cards
    │   ├── heroes.md                 ←   hero sections
    │   ├── headers.md                ←   sticky header shell
    │   ├── navbars.md                ←   nav, dropdown/mega, drawer, sidebar, tabs
    │   ├── footers.md                ←   minimal + mega footers
    │   ├── sections.md               ←   features, pricing, testimonials, FAQ, CTA…
    │   ├── forms.md                  ←   inputs + contact form
    │   ├── overlays.md               ←   modal, toast, tooltip, ⌘K palette
    │   └── ui-elements.md            ←   tabs, alerts, skeleton, pagination, table…
    ├── animated-components.md        ← animated / glowing / neon components
    ├── page-patterns.md              ← collections → hub + a designed page per item
    ├── backend.md                    ← server actions, APIs, validation, email, data, auth
    └── ux-rules.md                   ← accessibility / performance / motion checklist
```

`SKILL.md` carries YAML frontmatter (`name`, `description`). OpenCode uses the `description` to
**auto-activate** the skill when your request matches (building, designing, styling, or animating
a Next.js UI). The body tells the agent which reference file to open for the task at hand.

---

## 2. Install into OpenCode

OpenCode discovers skills in a `skills/` directory inside its config folder. Pick one scope:

### Option A — Global (available in every project)

Copy this folder so the skill lives at `~/.config/opencode/skills/nextjs-design-skill/`:

**macOS / Linux**

```bash
mkdir -p ~/.config/opencode/skills/nextjs-design-skill
cp -r ./nextjs-design-skill/. ~/.config/opencode/skills/nextjs-design-skill/
```

**Windows (PowerShell)**

```powershell
$dest = "$HOME\.config\opencode\skills\nextjs-design-skill"
New-Item -ItemType Directory -Force -Path $dest | Out-Null
Copy-Item -Recurse -Force ".\nextjs-design-skill\*" $dest
```

### Option B — Per-project (only this repo)

Copy it to `.opencode/skills/nextjs-design-skill/` in your project root:

**macOS / Linux**

```bash
mkdir -p .opencode/skills/nextjs-design-skill
cp -r ./nextjs-design-skill/. .opencode/skills/nextjs-design-skill/
```

**Windows (PowerShell)**

```powershell
$dest = ".\.opencode\skills\nextjs-design-skill"
New-Item -ItemType Directory -Force -Path $dest | Out-Null
Copy-Item -Recurse -Force ".\nextjs-design-skill\*" $dest
```

> The skill folder name must contain `SKILL.md` at its root. Keep the `references/` subfolder
> next to it so the in-skill links resolve.

### Verify it loaded

Start OpenCode and list skills:

```bash
opencode
/skills
```

You should see **nextjs-design-skill** in the list. (If your OpenCode build doesn't expose
`/skills`, just confirm the files exist at the path above — the agent still reads them.)

---

## 3. How to invoke it

### Auto-activation (recommended)

Just describe the work naturally. The skill activates on matching requests:

```
Build a landing page for an AI analytics SaaS, dark mode, with a neon hero.
Create an animated sticky header with a glowing underline and a mobile drawer.
Design a portfolio homepage — minimalist, big type, scroll reveals.
Add a mega-menu navbar and a multi-column footer with a newsletter.
Make these cards neon with a glowing gradient border on hover.
Review this page for accessibility and dark-mode contrast.
```

### Explicit invocation

If you want to force it, name it:

```
Use the nextjs-design skill to build a pricing page with three tiers.
```

---

## 4. What the agent does with it

1. **Classifies** the site type and reads the matching playbook in `references/categories.md`.
2. **Chooses** a style + palette + font pairing from `references/design-systems.md`.
3. **Lays the foundation** — tokens, light/dark theming, type & spacing scales (`nextjs.md §2–§4`).
4. **Composes** the UI from `references/components/` using the pattern catalog in `references/patterns.md`,
   and adds effects from `references/animated-components.md`.
5. **Layers motion** (ambient → scroll → interaction) per `nextjs.md §5`.
6. **Verifies** against `references/ux-rules.md` (a11y, performance, responsive, reduced-motion).
7. **For a collection** (services / products / blog…), scaffolds a hub **and a designed page per
   item** from `references/page-patterns.md`.
8. **For working forms / APIs / data / auth**, implements server actions or route handlers from
   `references/backend.md`.

---

## 5. Example prompts that work well

| Goal            | Prompt                                                                                                                                      |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Full site       | "Build a modern marketing site for a fintech startup: hero, features, pricing, FAQ, CTA, footer. Soft-slate dark mode + light mode toggle." |
| Animated header | "Add a header that hides on scroll down, shows on scroll up, turns to glass when scrolled, with an animated active-link underline."         |
| Neon style      | "Restyle the hero in a retro-futuristic neon look — neon border on the CTA, glowing card grid, subtle aurora background."                   |
| Navigation      | "Replace the navbar with a desktop mega-menu + a mobile full-screen drawer, both animated."                                                 |
| Footer          | "Create a four-column mega-footer with newsletter signup, social links, and a big gradient CTA band above it."                              |
| Components      | "Give me a bento grid of feature cards with spotlight-on-hover and staggered scroll reveals."                                               |
| Services pages  | "Add our services — a services hub plus a separate, fully-designed page for each service."                                                  |
| Working form    | "Make the contact form actually send an email, with validation and a success state (server action)."                                       |
| Review          | "Audit this page against the skill's UX rules and fix any contrast or reduced-motion issues."                                               |

---

## 6. Customizing the skill

- **Change the default look:** edit the token block in `nextjs.md §2` (or pick a different style
  in `references/design-systems.md`) and the agent will follow it.
- **Add your own components:** append them to a file in `references/components/` or
  `references/animated-components.md` under a clear `## Heading` — the agent reads new sections
  automatically.
- **Tighten the rules:** edit `references/ux-rules.md`; it's the pre-ship checklist.
- **Keep `SKILL.md`'s `description` keyword-rich** — that's what triggers auto-activation. If the
  skill isn't activating, add the missing trigger words there.

---

## 7. Notes & compatibility

- Built for **Next.js App Router + React 19 + Tailwind CSS v4 + Motion + Lenis**. The patterns
  also adapt to Tailwind v3 (move `@theme` tokens into `tailwind.config`) and to Pages Router.
- All code respects `prefers-reduced-motion` and targets **WCAG AA** in both themes.
- This is an OpenCode-style **Agent Skill** (folder + `SKILL.md` frontmatter). The same folder
  also works in other tools that read the open Agent Skills format, but it is written and tested
  for **OpenCode**.
