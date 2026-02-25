---
description: Add a new CRUD feature (entity + service + actions + pages + components) to an existing Next.js project
---

# Add Feature Workflow

Use this when adding a new data entity with full CRUD to an existing project (e.g. Posts, Products, Orders).

> **Skill reference**: [nextjs-tailwindcss-neon](../skills/nextjs-tailwindcss-neon/SKILL.md)
> **Prerequisite**: Project already bootstrapped via [create-project workflow](./create-project.md)

---

## Phase 1 — Data Layer

1. Add the Prisma model to `prisma/schema.prisma`.
   → Schema patterns: [Prisma + NeonDB Setup](../skills/nextjs-tailwindcss-neon/examples/prisma-neondb-setup.md)

   Rules:
   - Always add `@@index` on foreign keys and frequently-queried fields
   - Use `cuid()` for IDs, `@default(now())` + `@updatedAt` for timestamps
   - Never import `@prisma/client` types directly in app code — re-export from `src/types/`

// turbo
2. Push schema and regenerate the Prisma client:
   ```bash
   npx prisma db push
   npx prisma generate
   ```

3. Add shared TypeScript types to `src/types/[entity].types.ts`. Re-export from `src/types/index.ts`.

4. Create the Zod validation schema at `src/lib/validations/[entity].schema.ts`.
   → Pattern: [Service + Action Pattern](../skills/nextjs-tailwindcss-neon/examples/service-action-pattern.md) — copy the Zod schema section.

---

## Phase 2 — Service Layer

5. Create `src/services/[entity].service.ts` with all DB query functions (no React, no actions).
   → [Service + Action Pattern](../skills/nextjs-tailwindcss-neon/examples/service-action-pattern.md) — copy the service template.

   Rules:
   - One service file per entity. Never mix entities.
   - All Prisma calls live here — never in actions, pages, or components.
   - Use `db.$transaction([])` for any write that touches multiple tables.
   - Add `use cache` + `cacheTag` on all read functions. See [Data Fetching](../skills/nextjs-tailwindcss-neon/resources/data-fetching.md).

---

## Phase 3 — Actions Layer

6. Create `src/actions/[entity].actions.ts`.
   → [Service + Action Pattern](../skills/nextjs-tailwindcss-neon/examples/service-action-pattern.md) — copy the Server Actions section.

   Rules (each action must follow this exact pattern):
   ```
   'use server'
   1. Parse + validate with Zod → return { error } on failure
   2. Call service function
   3. revalidateTag('[entity]') or revalidatePath('/path')
   4. Return { success: true, data }
   ```
   - Never put business logic in actions — delegate to the service.
   - Never call `db` directly inside an action.

---

## Phase 4 — Pages

7. Create the list page at `src/app/(app)/[feature]/page.tsx` (Server Component):
   - `await` params if dynamic
   - Call service function directly (or via `use cache` wrapper)
   - Pass fetched data as props to components — no fetching inside components
   - Export `metadata` or `generateMetadata`. See [Metadata & SEO](../skills/nextjs-tailwindcss-neon/resources/metadata.md).

8. Create the detail page at `src/app/(app)/[feature]/[id]/page.tsx` if needed:
   - Call `notFound()` if entity not found
   - Add `generateStaticParams()` if the entity list is bounded and known at build time

9. Protect routes in `src/proxy.ts` if authentication is required:
   ```ts
   '/[feature]/:path*': async ({ request, next }) => { ... }
   ```

---

## Phase 5 — Components

10. Create Server Components in `src/components/[feature]/`:
    - One component per file, kebab-case filename
    - Receive all data as props — never fetch inside components
    - Render the UI shell, tables, cards, etc.

11. Extract interactive leaves as Client Components (`*-client.tsx` or `*-button.tsx`):
    - Add `'use client'` only here
    - Pass only serializable props (no functions, class instances)
    - Keep client components focused — one interaction per file

    → See [RSC Patterns](../skills/nextjs-tailwindcss-neon/examples/rsc-patterns.md) for composition examples.

12. If interactive forms are needed, wire action to form:
    ```tsx
    <form action={createEntityAction}>...</form>
    ```
    → [Service + Action Pattern](../skills/nextjs-tailwindcss-neon/examples/service-action-pattern.md) — form integration section.

---

## Phase 6 — Verify

// turbo
13. Run TypeScript check — must pass with zero errors:
    ```bash
    npx tsc --noEmit
    ```

// turbo
14. Start dev server and manually test the feature:
    ```bash
    npm run dev
    ```
    - List page loads data
    - Create action inserts a record (check `npx prisma studio`)
    - Update/delete actions reflect immediately (cache invalidation working)
    - Protected routes redirect unauthenticated users

---

## Checklist Before Committing

- [ ] No TypeScript errors (`npx tsc --noEmit`)
- [ ] Service functions have `cacheTag` + `cacheLife` on reads
- [ ] Actions call `revalidateTag`/`revalidatePath` after every mutation
- [ ] No `db` calls outside `src/services/`
- [ ] No `'use client'` on any `page.tsx`
- [ ] All new routes protected in `proxy.ts` if behind auth
- [ ] Types added to `src/types/` — nothing imported directly from `@prisma/client`

---

## Next Steps

- Add authentication to protect the feature → [add-auth workflow](./add-auth.md)
- Add client-side global state if needed → [add-global-state workflow](./add-global-state.md)