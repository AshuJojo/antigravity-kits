---
description: Add Auth.js (NextAuth v5) authentication to an existing Next.js + Prisma project
---

# Add Authentication Workflow

Use this to implement robust authentication using NextAuth v5 (Auth.js) and protect routes within your application.

> **Skill reference**: [Auth.js Skill](../skills/auth/SKILL.md)
> **Prerequisite**: Project bootstrapped via [create-project](./create-project.md)

---

## Phase 1 — Installation & Setup

// turbo
1. Install Auth.js beta (v5) and Prisma adapter dependencies:
   ```bash
   npm install next-auth@beta @auth/prisma-adapter
   ```
   *If using Credentials provider, also install hashing library like bcryptjs: `npm install bcryptjs` and `npm install -D @types/bcryptjs`.*

2. Append the required Auth models (`User`, `Account`, `Session`, `VerificationToken`) to `prisma/schema.prisma`.
   → [Prisma Adapter Schema](../skills/auth/examples/prisma-adapter-schema.md) — copy and append to the file.

// turbo
3. Push to NeonDB and regenerate the client:
   ```bash
   npx prisma db push
   npx prisma generate
   ```

---

## Phase 2 — Configuration

4. Generate an `AUTH_SECRET` and add OAuth keys to your `.env.local`:
   ```bash
   openssl rand -base64 32
   ```
   Add to `.env.local`:
   ```
   AUTH_SECRET="<generated-secret>"
   AUTH_GITHUB_ID="..."
   AUTH_GITHUB_SECRET="..."
   ```

5. Create the central auth configuration file at `src/lib/auth.ts`.
   → [Auth Configuration](../skills/auth/examples/auth-config.md) — copy the template and configure your required providers (GitHub, Google, Credentials).

6. Create the API catch-all route at `src/app/api/auth/[...nextauth]/route.ts`.
   → [Auth Configuration](../skills/auth/examples/auth-config.md) — copy the snippet.

---

## Phase 3 — Protecting Routes

7. Secure your routes in `src/proxy.ts` (handled by `nextjs-tailwindcss-neon` skill).
   Example for protecting everything under `/dashboard` and `/admin`:
   ```ts
   // src/proxy.ts
   import { auth } from "@/lib/auth"

   // Inside your proxy logic:
   const session = await auth()

   if (request.nextUrl.pathname.startsWith('/dashboard')) {
     if (!session?.user) {
       return Response.redirect(new URL('/api/auth/signin', request.url))
     }
   }

   if (request.nextUrl.pathname.startsWith('/admin')) {
     if (session?.user?.role !== "ADMIN") {
       return Response.redirect(new URL('/dashboard', request.url)) // Or 404
     }
   }
   ```
   *(Note: The exact routing mechanism depends on your `proxy.ts` setup.)*

---

## Phase 4 — UI and Usage

8. Access session data in Server Components or protect Server Actions.
   → [Session Usage](../skills/auth/examples/session-usage.md) 
   - **Rule**: Pass `session.user.id` or necessary fields down directly as props to Client Components instead of `useSession()` when possible.

9. Add Login/Logout buttons where necessary.
   - Use plain forms referencing the Server Actions `signIn` / `signOut` imported from `@/lib/auth`.

---

## Phase 5 — Verify

// turbo
10. Check TypeScript compliance:
    ```bash
    npx tsc --noEmit
    ```

11. Start dev server and manually verify:
    ```bash
    npm run dev
    ```
    - Visit `http://localhost:3000/api/auth/signin`
    - Verify sign in creates a new User in NeonDB (`npx prisma studio`)
    - Verify protected routes successfully block non-authenticated access.

---

## Checklist Before Committing

- [ ] `npx tsc --noEmit` passes with 0 errors
- [ ] `AUTH_SECRET` is securely added to `.env.local` (and NOT committed)
- [ ] Prisma models pushed to database
- [ ] Required routes protected in `src/proxy.ts`
- [ ] Session fetched via `auth()` in server components (no `<SessionProvider>` globally wrapper)
