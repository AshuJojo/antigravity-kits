# Project Structure: Next.js + Tailwind CSS + Prisma + NeonDB (Scalable)

A production-ready, `src/`-based folder structure for a large-scale Next.js 16 application using Tailwind CSS, Prisma ORM, and NeonDB (serverless Postgres).

## Full Project Tree

```
my-app/
│
├── src/
│   │
│   ├── proxy.ts                                    ← Auth guard / network layer (v16)
│   ├── app/                                    ← App Router root
│   │   ├── layout.tsx                          ← Root layout (html, body, providers)
│   │   ├── page.tsx                            ← Home page /
│   │   ├── globals.css                         ← Tailwind base styles
│   │   ├── loading.tsx                         ← Root loading UI
│   │   ├── error.tsx                           ← Root error boundary ('use client')
│   │   ├── not-found.tsx                       ← 404 page
│   │   │
│   │   ├── (auth)/                             ← Route group — unauthenticated pages
│   │   │   ├── layout.tsx                      ← Auth shell layout
│   │   │   ├── login/page.tsx                  ← /login
│   │   │   ├── register/page.tsx               ← /register
│   │   │   └── forgot-password/page.tsx        ← /forgot-password
│   │   │
│   │   ├── (app)/                              ← Route group — authenticated app
│   │   │   ├── layout.tsx                      ← App shell (sidebar, nav, providers)
│   │   │   ├── dashboard/
│   │   │   │   ├── page.tsx                    ← /dashboard
│   │   │   │   └── loading.tsx
│   │   │   ├── users/
│   │   │   │   ├── page.tsx                    ← /users
│   │   │   │   └── [id]/
│   │   │   │       ├── page.tsx                ← /users/:id
│   │   │   │       └── edit/page.tsx           ← /users/:id/edit
│   │   │   └── settings/
│   │   │       └── page.tsx                    ← /settings
│   │   │
│   │   └── api/                                ← API Route Handlers
│   │       ├── auth/
│   │       │   └── [...nextauth]/route.ts      ← Auth.js / NextAuth
│   │       └── webhooks/
│   │           └── stripe/route.ts             ← External webhooks
│   │
│   ├── components/                             ← All React components
│   │   ├── ui/                                 ← Primitive design-system atoms
│   │   │   ├── button.tsx
│   │   │   ├── input.tsx
│   │   │   ├── card.tsx
│   │   │   ├── badge.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── dropdown.tsx
│   │   │   └── index.ts                        ← Re-exports all UI components
│   │   ├── layout/                             ← Structural/page-level components
│   │   │   ├── header.tsx
│   │   │   ├── sidebar.tsx
│   │   │   ├── footer.tsx
│   │   │   └── page-header.tsx
│   │   └── [feature]/                          ← Feature-scoped components
│   │       ├── users/
│   │       │   ├── user-card.tsx
│   │       │   ├── user-table.tsx
│   │       │   └── user-form.tsx
│   │       └── dashboard/
│   │           ├── stats-card.tsx
│   │           └── activity-feed.tsx
│   │
│   ├── lib/                                    ← Core server-side utilities
│   │   ├── db.ts                               ← Prisma client singleton
│   │   ├── auth.ts                             ← Auth config & session helpers
│   │   ├── validations/                        ← Zod schemas
│   │   │   ├── user.schema.ts
│   │   │   └── post.schema.ts
│   │   └── utils/
│   │       ├── format.ts                       ← Date, number, string formatters
│   │       ├── cn.ts                           ← clsx + twMerge helper
│   │       └── api.ts                          ← Server fetch helpers
│   │
│   ├── actions/                                ← Server Actions ('use server')
│   │   ├── user.actions.ts
│   │   ├── post.actions.ts
│   │   └── auth.actions.ts
│   │
│   ├── services/                               ← Business logic layer (pure functions)
│   │   ├── user.service.ts                     ← DB queries for users
│   │   ├── post.service.ts
│   │   └── email.service.ts                    ← Email sending logic
│   │
│   ├── hooks/                                  ← Custom React hooks (client-only)
│   │   ├── use-debounce.ts
│   │   ├── use-media-query.ts
│   │   └── use-toast.ts
│   │
│   ├── stores/                                 ← Client-side state (Zustand, Jotai, etc.)
│   │   ├── ui.store.ts                         ← Modal open/close, sidebar state
│   │   └── user.store.ts
│   │
│   ├── providers/                              ← React context providers
│   │   ├── index.tsx                           ← Composes all providers into one
│   │   ├── theme-provider.tsx
│   │   └── query-provider.tsx                  ← React Query client provider
│   │
│   ├── types/                                  ← Global TypeScript types & interfaces
│   │   ├── index.ts                            ← Re-exports everything
│   │   ├── api.types.ts                        ← API request/response shapes
│   │   ├── user.types.ts
│   │   └── next.types.ts                       ← Augmented Next.js types
│   │
│   └── config/                                 ← App-level constants
│       ├── site.ts                             ← Site name, URL, nav links
│       └── constants.ts                        ← Enums, magic strings/numbers
│
├── prisma/
│   ├── schema.prisma                           ← Prisma schema (single source of truth)
│   ├── seed.ts                                 ← DB seed script
│   └── migrations/                             ← Auto-generated by prisma migrate
│
├── public/
│   ├── images/
│   ├── icons/
│   └── fonts/                                  ← Self-hosted fonts (fallback)
│
├── tests/                                      ← Test suite (mirrors src/ structure)
│   ├── unit/
│   │   └── services/
│   │       └── user.service.test.ts
│   ├── integration/
│   │   └── api/
│   │       └── users.test.ts
│   └── e2e/
│       └── auth.spec.ts                        ← Playwright tests
│
├── next.config.ts
├── tailwind.config.ts
├── postcss.config.mjs
├── tsconfig.json
├── .env
├── .env.local                                  ← Secrets — never commit
└── package.json
```

## Architecture Principles

| Layer | Folder | Responsibility |
|---|---|---|
| Routing & UI | `app/` | Pages, layouts, loading/error states — all **Server Components** |
| Components | `components/` | Reusable UI — Server by default, `'use client'` only at interactive leaves |
| Actions | `actions/` | Server Actions — thin: validate → call service → revalidate |
| Services | `services/` | Business logic & all Prisma DB queries — pure functions, no React |
| Library | `lib/` | Low-level utilities, auth config, Zod schemas |
| State | `stores/` + `providers/` | Client-side global state only |
| Types | `types/` | Shared interfaces — never import from `@prisma/client` directly |
| Config | `config/` | Constants, site metadata, nav links |

> **Data flow rule**: Page → **Actions** → **Services** → **`lib/db`**. Nothing skips a layer.

## Modularization Rules

- **One component per file** — no barrel files that mix unrelated components.
- **One concern per module** — a service file only contains DB queries for one entity; a utility file only contains one category of helpers.
- **Pages are thin** — a `page.tsx` should only: fetch data (via service or `use cache`), pass it to components, and define the page's `metadata`. No UI logic inline.
- **Split Client Components from Server Components** — if a component has both server logic and interactive parts, extract the interactive part into a separate `*-client.tsx` file marked `'use client'`.
- **Colocate feature components** — components used only in one feature live in `components/[feature]/`; globally shared components live in `components/ui/`.
- **No cross-layer imports** — `services/` never imports from `actions/`; `components/` never imports from `services/` directly (go through actions or props).

## Key Files — Examples

Rather than duplicating code here, refer to the dedicated example files:

| File(s) | Example |
|---|---|
| `src/lib/db.ts`, `prisma/schema.prisma`, `.env.local` | [Prisma + NeonDB Setup](../examples/prisma-neondb-setup.md) |
| `src/services/*.ts`, `src/actions/*.ts`, `src/lib/validations/*.ts` | [Service + Action Pattern](../examples/service-action-pattern.md) |
| `tailwind.config.ts`, `tsconfig.json`, `next.config.ts`, `src/lib/utils/cn.ts`, `src/config/site.ts`, `src/providers/`, `src/proxy.ts` | [Project Config Boilerplate](../examples/project-config.md) |
| `src/app/layout.tsx`, `src/app/page.tsx`, `src/components/like-button.tsx` | [Basic Page](../examples/basic-page.md) |

## Setup Commands

```bash
# 1. Create project (src/ layout, TypeScript, Tailwind, ESLint, App Router)
npx create-next-app@latest my-app --typescript --tailwind --eslint --app --src-dir

# 2. Install Prisma
npm install prisma @prisma/client
npx prisma init

# 3. Install helpers
npm install clsx tailwind-merge zod

# 4. Set DATABASE_URL + DIRECT_URL in .env.local, then push schema to NeonDB
npx prisma db push

# 5. Generate Prisma client
npx prisma generate

# 6. Seed (optional)
npx tsx prisma/seed.ts
```

