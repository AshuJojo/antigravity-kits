---
description: Bootstrap a new production-grade Next.js 16 + Tailwind CSS + Prisma + NeonDB project
---

# Create Project Workflow

Use this when starting a **new** Next.js + Tailwind + Prisma + NeonDB project from scratch.

> **Skill reference**: [nextjs-tailwindcss-neon](../skills/nextjs-tailwindcss-neon/SKILL.md)

---

## Phase 1 — Scaffold

// turbo
1. Run `create-next-app` with all required flags:
```bash
npx create-next-app@latest my-app \
  --typescript \
  --tailwind \
  --eslint \
  --app \
  --src-dir \
  --import-alias "@/*"
cd my-app
```

// turbo
2. Install core dependencies:
```bash
npm install prisma @prisma/client
npm install clsx tailwind-merge zod
npm install -D @types/node
npx prisma init
```

---

## Phase 2 — Configure

3. Apply config boilerplate — **copy these files exactly**:
   → [`tailwind.config.ts`, `tsconfig.json`, `next.config.ts`, `src/lib/utils/cn.ts`, `src/config/site.ts`, `src/providers/index.tsx`, `src/proxy.ts`](../skills/nextjs-tailwindcss-neon/examples/project-config.md)

4. Set up folder structure matching the canonical layout:
   → [Project Structure](../skills/nextjs-tailwindcss-neon/resources/project-structure.md)

   Create the top-level directories now:
   ```bash
   mkdir -p src/actions src/services src/hooks src/stores src/types src/config src/providers
   mkdir -p src/lib/validations src/lib/utils
   mkdir -p src/components/ui src/components/layout
   ```

5. Set up root layout with Providers and fonts:
   → [Basic Page example](../skills/nextjs-tailwindcss-neon/examples/basic-page.md) — use `app/layout.tsx` and `app/page.tsx` as starting point.

---

## Phase 3 — Database

6. Set `.env.local` credentials — **get both strings from NeonDB dashboard → Connection Details**:
   → [Prisma + NeonDB Setup](../skills/nextjs-tailwindcss-neon/examples/prisma-neondb-setup.md) — copy `.env.local` and `prisma/schema.prisma` templates.

   ```
   DATABASE_URL=    ← pooled (pgBouncer) — for all app queries
   DIRECT_URL=      ← direct connection — for prisma migrate
   AUTH_SECRET=     ← generate: openssl rand -base64 32
   NEXT_PUBLIC_APP_URL=http://localhost:3000
   NEXT_PUBLIC_APP_NAME=My App
   ```

7. Push schema to NeonDB and generate Prisma client:
   ```bash
   npx prisma db push
   npx prisma generate
   ```
   Verify connection succeeds — if `prepare` errors appear, confirm `?pgbouncer=true` is in `DATABASE_URL`.

8. Create the Prisma singleton (`src/lib/db.ts`):
   → [Prisma + NeonDB Setup](../skills/nextjs-tailwindcss-neon/examples/prisma-neondb-setup.md) — copy `src/lib/db.ts`.

---

## Phase 4 — Verify

// turbo
9. Start the dev server and confirm it runs without errors:
   ```bash
   npm run dev
   ```
   Expected: `✓ Ready on http://localhost:3000`

10. TypeScript check — zero errors required:
    ```bash
    npx tsc --noEmit
    ```

11. Confirm folder structure matches the canonical layout before writing any features:
    → [Project Structure](../skills/nextjs-tailwindcss-neon/resources/project-structure.md)

---

## Checklist Before First Commit

- [ ] `npm run dev` starts without errors or warnings
- [ ] `npx tsc --noEmit` passes
- [ ] `.env.local` is in `.gitignore`
- [ ] `prisma/schema.prisma` has correct `provider = "postgresql"` + both `url`/`directUrl`
- [ ] `src/proxy.ts` is in place (even if empty — rename from `middleware.ts` if it exists)
- [ ] Root layout uses `next/font`, wraps `<Providers>`, renders `<html>` + `<body>`

---

## Next Step

→ Add your first feature: [add-feature workflow](./add-feature.md)
