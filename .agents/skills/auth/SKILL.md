---
name: auth
description: Authentication with Auth.js (NextAuth v5) including session strategy, providers (GitHub, Google, Credentials), auth() helper usage, and Prisma adapter schema additions.
---

# Auth.js (NextAuth v5) Skill

Structured guidance for implementing authentication in Next.js applications using Auth.js v5.

**Triggers**: Adding authentication · configuring OAuth providers · Credentials login · session management · JWT vs database sessions · Prisma adapter.

## Skill Structure

- **Resources** — concept & rules (no code).
  - [Session Strategy](./resources/session-strategy.md): Choosing between JWT and database sessions, and session shape.
  - [Providers & Auth Helper](./resources/providers-and-helpers.md): Configuring providers (GitHub/Google/Credentials) and using `auth()` vs `useSession()`.
- **Examples** — copy-paste code.
  - [Auth Configuration](./examples/auth-config.md): `src/lib/auth.ts` setup with providers and callbacks.
  - [Prisma Adapter Schema](./examples/prisma-adapter-schema.md): Required models to append to `schema.prisma`.
  - [Session Usage](./examples/session-usage.md): Server Component, Client Component, and Server Action examples.

## Strategy

1. **New auth setup** → Ensure Prisma is set up first via `nextjs-tailwindcss-neon` skill, then follow `add-auth.md` workflow.
2. **Provider configuration** → See [Auth Configuration](./examples/auth-config.md).
3. **Protecting routes** → Refer to the `nextjs-tailwindcss-neon` skill for `proxy.ts` configuration.
4. **Accessing session data** → Prefer the server-side `auth()` helper over the client-side `useSession()` whenever possible.

## Key Constraints & Rules

> **Authoritative.** These constraints govern how authentication is implemented.

- **Opt-in Feature** — Authentication is not required for every project. Only use this skill if the user explicitly asks to add authentication, or if the project already has Auth.js installed.
- **NextAuth v5 (Auth.js) only** — do not use v4 (`next-auth`) patterns. The import is `next-auth@beta`, but the config lives in `src/lib/auth.ts`.
- **Server-Side Preferred** — Use the `auth()` helper in Server Components and Server Actions. Avoid wrapping the entire application in `<SessionProvider>` unless strictly necessary for extensive client-side session usage.
- **Route Protection Context** — Route protection happens in `src/proxy.ts`, which is handled by the `nextjs-tailwindcss-neon` skill. This skill handles generating the session object, not the middleware guarding.
- **Credentials Provider Constraint** — When using the Credentials provider, always use JWT session strategy. Database sessions do not support the Credentials provider by design in Auth.js.
- **Prisma Adapter** — Store user sessions in the database using `@auth/prisma-adapter` unless the project's architecture dictates pure JWTs.
