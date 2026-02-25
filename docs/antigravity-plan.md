# Antigravity: Production App Plan

> **Goal**: A complete AI agent toolkit for building production-grade Next.js 16 + Tailwind CSS + Prisma + NeonDB applications. This document maps out everything to build inside `.agent/`.

---

## Current State

| Type | Name | Status |
|---|---|---|
| Skill | `nextjs-tailwindcss-neon` | ✅ Done |
| Workflows | — | ❌ None yet |
| KI (Knowledge Items) | — | ❌ None yet |
| Agent Memory / Rules | — | ❌ None yet |

---

## ⚠️ Cross-Layer Ownership Map (Read First)

Each layer has a single responsibility. **If content could belong to two layers, this table decides.**

| Layer | Owns | Must NOT contain |
|---|---|---|
| **`rules.md`** | Universal code style + architecture rules that apply to every task, in every skill | Tech-specific rules, step-by-step procedures, code examples |
| **Skill `SKILL.md`** | Entry point, file index, strategy, authoritative constraints for that tech domain | Detailed how-to steps (→ workflow), working code (→ examples), facts from past bugs (→ KI) |
| **Skill `resources/*.md`** | Concepts, mental models, decision tables, rules for one topic within the skill | Code blocks, step-by-step instructions, anything already in `SKILL.md` constraints |
| **Skill `examples/*.md`** | Copy-paste working code only | Explanations of *why* (→ resource), generic advice, rules |
| **Workflow `*.md`** | Numbered step-by-step procedure for a specific task | Concept explanations (→ skill resource), code snippets longer than 3 lines (→ example), universal rules (→ rules.md) |
| **KI** | Distilled facts from past solved problems — gotchas, version quirks, non-obvious patterns discovered in real usage | Anything already well-documented in a skill; prescriptive rules (→ skill/rules) |

### Conflict Resolution Rules

1. **Skill vs Workflow**: Skills explain the *what/why*. Workflows explain the *how/when in sequence*. Workflows reference skills — never copy from them.
2. **Skill vs KI**: Skills = current best practices (maintained). KIs = historical facts from real bugs (append-only). A KI updates a skill resource if it reveals a *rule* — it stays in KI if it's a *gotcha*.
3. **Skill vs Skill**: Each skill owns one technology domain. Cross-domain patterns (e.g. `auth` + `nextjs`) live in the skill whose workflow triggers it — `auth` owns session config; `nextjs-tailwindcss-neon` owns `proxy.ts` auth guard usage.
4. **Rules.md vs Skill constraints**: `rules.md` = universal rules (TypeScript always, kebab-case files). Skill constraints = tech-specific rules (`params` are async, no `middleware.ts`). No rule appears in both.
5. **Between skills**: If two skills share a concept, one references the other — never both define it. The **canonical owner** is the skill whose `SKILL.md` lists it as a primary concern.

### Canonical Ownership for Shared Concepts

| Concept | Canonical Owner | Others do... |
|---|---|---|
| `proxy.ts` / route protection | `nextjs-tailwindcss-neon` → routing | `auth` references it, doesn't redefine |
| Session / user identity | `auth` | `nextjs-tailwindcss-neon` references how to consume `auth()` in Server Components |
| Prisma schema + `db.ts` | `nextjs-tailwindcss-neon` | `auth` uses its own adapter model, doesn't redefine Prisma setup |
| Tailwind `cn()` / design tokens | `ui-system` | `nextjs-tailwindcss-neon` references `ui-system` for component styling |
| Zod validation schemas | `nextjs-tailwindcss-neon` → data layer | `forms` owns form-specific Zod integration; `auth` owns auth-specific schemas |
| Error boundaries (`error.tsx`) | `nextjs-tailwindcss-neon` → app-router | `error-handling` owns the *logging + response shaping* layer |
| Test setup (Vitest, Playwright) | `testing` | Other skills never include test configuration |
| Vercel env vars / deploy config | `deployment` | Other skills never include deployment steps |

---

## 1. Skills to Build

Skills are passive knowledge bases — the agent reads them when needed. Each skill owns **one domain**; cross-domain content lives in references, not both files.

### ✅ Already Done

- **`nextjs-tailwindcss-neon`** — App Router, RSC, routing, data fetching, metadata, Prisma/NeonDB, state management.

### 🔲 To Build

#### `auth` — Authentication with Auth.js (NextAuth v5)
**Owns**: Auth.js setup, providers, session shape, `auth()` helper, `useSession`, JWT vs DB sessions, Prisma adapter schema additions.
**Does NOT cover**: `proxy.ts` route protection patterns (→ `nextjs-tailwindcss-neon`), general Prisma setup (→ `nextjs-tailwindcss-neon`).

#### `ui-system` — Design System with shadcn/ui + Tailwind
**Owns**: `shadcn/ui` setup + component installation, Tailwind v4 design tokens (`@theme`), dark mode, `cn()` utility, form components with `react-hook-form`, accessible UI patterns.
**Does NOT cover**: general Zod schemas (→ `nextjs-tailwindcss-neon`), form submission/Server Actions (→ `nextjs-tailwindcss-neon`), page-level layout (→ `nextjs-tailwindcss-neon`).

#### `testing` — Testing Strategy
**Owns**: Vitest setup + unit test patterns, API route integration tests, Playwright E2E, mocking Prisma, CI test commands, test file colocating.
**Does NOT cover**: project setup (→ `nextjs-tailwindcss-neon`), deployment CI steps (→ `deployment`).

#### `deployment` — Vercel + NeonDB Production Deploy
**Owns**: Vercel project config, environment variable tiers, NeonDB branch strategy (dev/preview/prod), `prisma migrate deploy` in build, Core Web Vitals review.
**Does NOT cover**: `DATABASE_URL`/`DIRECT_URL` explanation (→ `nextjs-tailwindcss-neon` → prisma-neondb-setup example), general Next.js build config (→ `nextjs-tailwindcss-neon`).

#### `error-handling` — Structured Error Handling
**Owns**: Server Action error return shapes, Sentry/logging integration, API route error responses, Zod error formatting for form field errors.
**Does NOT cover**: `error.tsx` file structure (→ `nextjs-tailwindcss-neon` → app-router), `not-found.tsx` (→ `nextjs-tailwindcss-neon`).

#### `forms` — Form Patterns
**Owns**: `react-hook-form` + Zod + Server Actions integration pattern, `useOptimistic`, file uploads, multi-step forms, form state persistence.
**Does NOT cover**: Zod schema definition patterns (→ `nextjs-tailwindcss-neon`), UI form components (→ `ui-system`), Server Action implementation (→ `nextjs-tailwindcss-neon`).

---

## 2. Workflows to Build

Workflows are numbered step-by-step checklists — the agent executes them. They **reference** skills and examples; they never redefine concepts or embed long code blocks.

> **Workflow rule**: If a step needs explanation → link to a skill resource. If a step needs code → link to an example file. Keep each step to one imperative sentence.

### `create-project.md` — Bootstrap a Production Project

```
1. npx create-next-app@latest with --typescript --tailwind --eslint --app --src-dir
2. Install deps: prisma @prisma/client clsx tailwind-merge zod
3. Set up folder structure → [Project Structure](../skills/nextjs-tailwindcss-neon/resources/project-structure.md)
4. Copy config files → [Project Config Boilerplate](../skills/nextjs-tailwindcss-neon/examples/project-config.md)
5. npx prisma init → set DATABASE_URL + DIRECT_URL in .env.local
6. npx prisma db push && npx prisma generate
7. Set up root layout + Providers → [Basic Page](../skills/nextjs-tailwindcss-neon/examples/basic-page.md)
8. npm run dev — verify no errors
```

### `add-feature.md` — Add a New CRUD Feature

```
1. Add Prisma model to schema.prisma → npx prisma db push && npx prisma generate
2. Create Zod schema in src/lib/validations/[entity].schema.ts
3. Create service in src/services/[entity].service.ts → [Service + Action Pattern](../skills/nextjs-tailwindcss-neon/examples/service-action-pattern.md)
4. Create Server Actions in src/actions/[entity].actions.ts
5. Create page(s) in src/app/(app)/[feature]/page.tsx — Server Components only
6. Create Server Component children in src/components/[feature]/
7. Extract interactive parts into *-client.tsx files ('use client')
8. Add revalidateTag()/revalidatePath() calls in actions after mutations
9. Protect route in src/proxy.ts if auth required
```

### `add-auth.md` — Add Authentication

```
1. Install: next-auth@beta @auth/prisma-adapter
2. Configure src/lib/auth.ts with providers + Prisma adapter → [auth skill](../skills/auth/)
3. Add app/api/auth/[...nextauth]/route.ts
4. Add User/Account/Session models to schema.prisma → npx prisma db push
5. Protect routes in src/proxy.ts using session cookie check
6. Access session server-side: const session = await auth()
7. Add login/logout UI using Client Components
8. Set AUTH_SECRET + provider keys in .env.local
```

### `add-global-state.md` — Add a Zustand Store

```
1. Check state-management rules first → confirm global state is justified
2. Create src/stores/[domain].store.ts → [Zustand Store](../skills/nextjs-tailwindcss-neon/examples/zustand-store.md)
3. If SSR init needed: use createStore inside a Provider (see hydration-safe pattern)
4. Import store ONLY inside 'use client' components — never in Server Components
```

### `deploy.md` — Deploy to Vercel + NeonDB

```
1. Push to GitHub
2. Import repo in Vercel dashboard
3. Set env vars: DATABASE_URL, DIRECT_URL, AUTH_SECRET, NEXT_PUBLIC_APP_URL
4. Set build command: prisma generate && next build
5. Set up NeonDB branches: main → prod, preview branches → preview deploys
6. Trigger deploy — check build logs for errors
7. Run smoke test on production URL
8. Check Vercel analytics for Core Web Vitals
```

### `debug-issue.md` — Systematic Debugging

```
1. Classify: server error (terminal) or client error (browser console)?
2. Server: read full stack trace, check action return value
3. Client: React DevTools → check props passed from server
4. Data: npx prisma studio → verify DB state
5. Cache: revalidateTag('all') to rule out stale cache
6. Routing: verify params are awaited, check proxy.ts rule order
7. Types: npx tsc --noEmit → fix all type errors
```

---

## 3. Knowledge Items (KIs) to Create

KIs are distilled reference knowledge the agent uses across conversations.

| KI Name | What it captures |
|---|---|
| `neondb-connection-patterns` | Pooled vs direct URL, pgBouncer gotchas, connection limits, troubleshooting `prepared statement` errors |
| `nextjs-v16-breaking-changes` | `params` as Promise, `middleware.ts` → `proxy.ts`, `viewport` split from metadata, React Compiler defaults |
| `tailwind-v4-migration` | `@import "tailwindcss"` instead of directives, `@theme` instead of `theme.extend`, v4 breaking changes |
| `prisma-patterns` | Singleton pattern, `$transaction`, `include` vs `select`, avoiding N+1, soft deletes, cursor pagination |
| `auth-js-v5-patterns` | `auth()` vs `getServerSession()`, middleware vs proxy.ts, session shape, JWT strategy |
| `vercel-deployment-patterns` | Env var tiers, preview branching, build hooks, edge vs serverless function trade-offs |

---

## 4. Agent Memory / `.agent/rules.md`

A global rules file the agent always reads — keeps agents consistent across conversations.

```md
# Global Agent Rules

## Code Style
- Always TypeScript, strict mode
- Prefer named exports over default exports (except pages/layouts)
- File names: kebab-case. Component names: PascalCase. Functions: camelCase.
- Use absolute imports with @/ prefix

## Architecture
- SSR by default, CSR only at interactive leaf components
- Never put 'use client' on page.tsx
- Data flow: Page → Actions → Services → lib/db
- One component per file, one concern per module

## Quality Gates (always run before finishing)
- No TypeScript errors (tsc --noEmit)
- No ESLint errors
- Dev server starts without warnings
```

---

## 5. Priority Build Order

```
Phase 1 — Foundation (do first)
├── workflows/create-project.md     ← most used, highest ROI
├── workflows/add-feature.md        ← core loop of app development
└── .agent/rules.md                 ← ensures consistency everywhere

Phase 2 — Auth & UI
├── skills/auth                     ← every real app needs auth
├── skills/ui-system                ← shadcn/ui + Tailwind v4
└── workflows/add-auth.md

Phase 3 — Quality & Production
├── skills/testing                  ← unit + E2E
├── skills/deployment               ← Vercel + NeonDB prod
├── skills/error-handling
└── workflows/deploy.md

Phase 4 — KIs (build as you go)
└── Create KIs after solving a novel problem in a conversation
```

---

## 6. Folder Layout (End State)

```
.agent/
├── skills/
│   ├── nextjs-tailwindcss-neon/   ✅ Done
│   ├── auth/                       Phase 2
│   ├── ui-system/                  Phase 2
│   ├── testing/                    Phase 3
│   ├── deployment/                 Phase 3
│   ├── error-handling/             Phase 3
│   └── forms/                      Phase 3
├── workflows/
│   ├── create-project.md           Phase 1
│   ├── add-feature.md              Phase 1
│   ├── add-auth.md                 Phase 2
│   ├── add-global-state.md         Phase 2
│   ├── deploy.md                   Phase 3
│   └── debug-issue.md              Phase 3
└── rules.md                        Phase 1
```

---

## 7. Quick Start: What to Build Next

| Action | Effort | Impact |
|---|---|---|
| `workflows/create-project.md` | 30 min | 🔥 High — used every new project |
| `workflows/add-feature.md` | 30 min | 🔥 High — used multiple times per project |
| `.agent/rules.md` | 15 min | 🔥 High — agent consistency baseline |
| `skills/auth` | 2h | ⚡ Medium-High — every production app needs it |
| `skills/ui-system` | 2h | ⚡ Medium-High — every UI task benefits |
| `skills/testing` | 3h | 📦 Medium — critical for production quality |
