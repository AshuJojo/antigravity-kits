---
trigger: always_on
glob:
description: Universal code style, architecture, state management, and quality rules for all Next.js + Tailwind + Prisma + NeonDB projects.
---

# Global Agent Rules

These rules apply to **every task** across every skill and workflow. Skills reference this file — they do not restate these rules.

## Code Style

- **TypeScript always** — never generate plain `.js` files for app code.
- **Strict mode** — `tsconfig.json` must have `"strict": true`.
- **Named exports** — except for Next.js pages and layouts (which require default exports).
- **File names**: `kebab-case` (e.g. `user-card.tsx`, `user.service.ts`).
- **Component names**: `PascalCase`. **Function/variable names**: `camelCase`.
- **No `any`** — use `unknown` and narrow it, or define a proper type.

## Authentication (Optional)

- **Authentication is NOT required by default.**
- **If auth is requested or already exists in the project**, you MUST use **Auth.js (NextAuth v5)** and follow the `auth` skill. Do not use Clerk, Supabase Auth, or Firebase Auth unless the user explicitly requests them.

## UI & Styling

- **Tailwind CSS by default** — all UI is styled with Tailwind. Do not install shadcn/ui, Chakra UI, MUI, Ant Design, or any other component library unless the user **explicitly requests it**.
- **Hand-craft all components** — buttons, inputs, cards, dialogs, modals etc. are built from scratch using Tailwind utility classes, unless the user explicitly asks for shadcn/ui.
- **`cn()` helper always** — merge conditional classes using `clsx` + `tailwind-merge` via `src/lib/utils/cn.ts`. Never concatenate class strings manually.
- **Tailwind v4 tokens** — define design tokens (colors, spacing, typography) in `@theme` inside `globals.css`. Avoid hardcoding values outside the theme.
- **Dark mode** — use the `dark:` variant. Implement class-based dark mode (`darkMode: 'class'` in config).
- **No inline styles** — use Tailwind classes. Exception: dynamic values that cannot be expressed as Tailwind classes (e.g. `style={{ height: dynamicPx }}`).

## Architecture

- **SSR by default** — every page and component is a Server Component unless it explicitly needs client interactivity.
- **`'use client'` at the leaf only** — never on `page.tsx`, `layout.tsx`, or any component that doesn't directly use hooks or browser APIs.
- **No data fetching in Client Components** — all `fetch`, DB calls, and service calls are server-side. Client Components receive data as props.
- **Data flow is strict**: `Page → Actions → Services → lib/db`. Nothing skips a layer.
- **One component per file** — never mix server and client logic in the same file.
- **Pages are thin** — `page.tsx` only: fetch data + compose components + export metadata.
- **No `middleware.ts`** — use `src/proxy.ts` for all network-level logic (auth guards, redirects).
- **No raw `<img>`** — always `next/image`. **No raw `<a>` for internal links** — always `next/link`.

## Layer Ownership

| Layer | Folder | Rule |
|---|---|---|
| Pages & layouts | `src/app/` | Server Components only |
| Reusable UI | `src/components/` | Server by default; `'use client'` only at interactive leaves |
| Server mutations | `src/actions/` | Thin: validate → service → revalidate cache |
| Business logic + DB | `src/services/` | Pure functions, no React — all Prisma calls live here |
| Utilities & config | `src/lib/` | No side effects, no DB |
| Global UI state | `src/stores/` | Client-only, UI state only (no DB-backed data) |
| Types | `src/types/` | Re-export here — never import directly from `@prisma/client` |

## State Management

- **Default**: props and Server Components.
- **Escalation**: props → lift to parent → React Context → Zustand/Jotai.
- **Add global state only when**: the same client UI state is needed in 3+ unrelated components.
- **Stores hold UI state only** — `isModalOpen`, `sidebarCollapsed`, `theme`. Never DB records.
- **No store access in Server Components** — ever.
- **Selector subscriptions**: `useStore((s) => s.value)` — never destructure the whole store.

## Quality Gates

Run before finishing **every** task:

1. `npx tsc --noEmit` — zero TypeScript errors.
2. `npm run dev` — dev server starts without errors or warnings.
3. ESLint — no errors (pre-existing warnings acceptable).

## Cross-Layer Conflict Resolution

No rule is stated in two places. When content could belong to multiple layers:

| Content type | Canonical location |
|---|---|
| Universal rules (this file) | `.agents/rules/global-rule.md` |
| Tech-specific constraints | Skill `SKILL.md` → Key Constraints |
| Concept explanations | Skill `resources/*.md` |
| Working code examples | Skill `examples/*.md` |
| Step-by-step procedures | `.agents/workflows/*.md` |
| Real-usage gotchas from past bugs | Knowledge Items (KI) |
