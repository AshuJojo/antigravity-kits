---
description: Add a new CRUD feature (entity + service + actions + pages + components) to an existing Next.js project using Feature-Sliced Design
---

# Add Feature Workflow

Use this workflow to add a new business entity (e.g., `Course`, `Team`, `Invoice`) to the application, adhering perfectly to the Feature-Sliced Design modularity architecture.

> **Skill References:**
> - [nextjs-tailwindcss-neon](../skills/nextjs-tailwindcss-neon/SKILL.md)
> - [forms](../skills/forms/SKILL.md)

---

## Phase 1 — Database & Type Foundation

1. Add the new Prisma model to `prisma/schema.prisma`. Include standard fields (`id`, `createdAt`, `updatedAt`).
2. Generate the Prisma client and push to the database:
   // turbo
   ```bash
   npx prisma db push && npx prisma generate
   ```

## Phase 2 — Scaffold the Feature Module

Create a dedicated folder for the entity at `src/components/features/[entity]`. **Do not** place these files in global `src/actions/` or `src/services/` folders.

1. **Create `src/components/features/[entity]/validations.ts`**:
   - Define the Zod schema for creating and updating the entity.
   - Example: `export const create[Entity]Schema = z.object({ ... })`

2. **Create `src/components/features/[entity]/types.ts`**:
   - Export standard TypeScript interfaces (extending Prisma generation if needed) or infer types from the validations.

3. **Create `src/components/features/[entity]/services.ts`**:
   - Implement the business logic and Prisma queries (`findMany`, `create`, `delete`).
   - *Rule: This file must not contain React code or Server Actions.*

4. **Create `src/components/features/[entity]/__tests__/services.test.ts`**:
   - Add colocated Vitest unit tests right inside the feature.

5. **Create `src/components/features/[entity]/actions.ts`**:
   - Implement the Server Actions using the [Safe Action Wrapper](../skills/error-handling/examples/action-wrapper.md).
   - Ensure you `revalidatePath` to clear the cache upon success.
   - *Rule: Validate input using the Zod schema here before calling the service.*

## Phase 3 — UI Implementation (Inside the Feature Module)

1. **Create `src/components/features/[entity]/components/[entity]-form.tsx`**:
   - Build the interactive form component (marked `'use client'`).
   - Use `react-hook-form` and connect it to your Server Action.

2. **Create `src/components/features/[entity]/components/[entity]-list.tsx`** (or card/table):
   - Build the UI components for displaying the entity data. 
   - These can be Server Components or Client Components as needed.

## Phase 4 — Routing & Page Composition

Finally, expose the feature to the user via the Next.js App Router.

1. **Create the Route `src/app/(app)/[entity]/page.tsx`**:
   - This must be a Server Component.
   - Call the `.../services.ts` function directly here to fetch existing data.
   - Compose the page using layout components and the feature components created above.

2. **(Optional) Add to Navigation**:
   - Update `src/config/site.ts` or `src/components/layout/sidebar.tsx` to include a link to the new feature path.

3. **(Optional) Protect the Route**:
   - If this new feature requires authentication and it is not inside an already-protected Route Group, update `src/proxy.ts` to guard the route path.