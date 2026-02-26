---
description: Add or update Zustand global state for a feature in an existing Next.js project
---

# Add Global State Workflow

Use this when a feature introduces UI state that needs to be shared across 3+ unrelated client components.

> **Skill reference**: [nextjs-tailwindcss-neon](../skills/nextjs-tailwindcss-neon/SKILL.md)
> **Code reference**: [Zustand Store](../skills/nextjs-tailwindcss-neon/examples/zustand-store.md)
> **Prerequisite**: Project bootstrapped via [create-project](./create-project.md)

---

## Step 0 — Justify First

Before writing any store code, answer these questions:

| Question | If Yes → |
|---|---|
| Can state live in one component with `useState`? | Use `useState`. **Stop here.** |
| Is prop-drilling ≤ 2 levels? | Lift state to common parent. **Stop here.** |
| Is state shared across a small, bounded subtree? | Use React Context. **Stop here.** |
| Is state UI-only, shared across 3+ unrelated components? | **Continue below — Zustand.** |
| Is state async / server-synced? | Use TanStack Query instead of Zustand. |

> Never put server-fetched data (DB records, API responses) in a Zustand store. That belongs in `use cache` or React Query.

---

## Phase 1 — Create or Extend a Store

1. Check `src/stores/` — does a relevant store for this domain already exist?
   - **Yes** → extend the existing store with new state slices. Do **not** create a new file for the same domain.
   - **No** → create `src/stores/[domain].store.ts`.

2. Write the store following the standard pattern:
   → [Zustand Store example](../skills/nextjs-tailwindcss-neon/examples/zustand-store.md) — copy the `ui.store.ts` template and adapt it.

   Rules:
   - One store per domain (`ui.store.ts`, `user.store.ts`, `cart.store.ts`)
   - Store values = ephemeral UI state only (`isOpen`, `selected`, `activeTab`, `theme`)
   - Each action mutates only the state it owns — no side effects, no API calls

3. If the store needs server-passed initial values (to avoid hydration mismatch), use the `createStore` + Provider pattern:
   → [Zustand Store example — Hydration-Safe Pattern](../skills/nextjs-tailwindcss-neon/examples/zustand-store.md)

   Add the Provider to `src/providers/index.tsx` so it wraps the app automatically.

---

## Phase 2 — Wire Into Components

4. Import the store **only inside `'use client'` components**:
   ```ts
   import { useUIStore } from '@/stores/ui.store'
   ```
   Never import a store in a Server Component or `page.tsx`.

5. Use **selector subscriptions** to avoid unnecessary re-renders:
   ```ts
   // ✅ Good — subscribes to only this value
   const isOpen = useUIStore((s) => s.isOpen)

   // ❌ Bad — re-renders on any store change
   const store = useUIStore()
   ```

6. Keep Server Components clean — pass only what client components need as props. The store handles the rest client-side.

---

## Phase 3 — Verify

// turbo
7. Run TypeScript check — must pass with zero errors:
   ```bash
   npx tsc --noEmit
   ```

8. Manually verify in dev:
   - State initialises correctly on first render
   - State persists across navigation within the app (no reset on route change)
   - Hard refresh resets to default (expected for ephemeral UI state)
   - No hydration mismatch errors in the browser console

---

## Checklist Before Committing

- [ ] Justification passed Step 0 — global state is genuinely required
- [ ] Store is in `src/stores/[domain].store.ts` — one file per domain
- [ ] Store holds only UI state — no DB-backed data
- [ ] Store is imported only inside `'use client'` components
- [ ] Selector subscriptions used (`useStore((s) => s.value)`) — not full store destructuring
- [ ] Hydration-safe `createStore` pattern used if store needs server-passed initial values
- [ ] No TypeScript errors
