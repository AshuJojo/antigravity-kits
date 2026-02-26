---
description: Systematic Debugging for Next.js 16 + Tailwind + Prisma applications
---

# Debug Issue Workflow

Use this workflow to systematically diagnose and fix issues before attempting random code changes.

> **Skill reference**: [nextjs-tailwindcss-neon](../skills/nextjs-tailwindcss-neon/SKILL.md)

---

## Step 1 — Classify the Error

Where is the error originating? 

- **Server-side error**: Appears in the terminal running `npm run dev` or Vercel build logs. Typically Prisma, routing, or Server Component render errors.
- **Client-side error**: Appears in the browser console. Typically React hydration, hook rules, or event handler crashes.
- **Type error**: Appears only in the IDE or when compiling.

## Step 2 — Check Server Action / API Output (If Network Issue)

If a form or mutation is failing:
1. Open Browser Network Tab.
2. Inspect the request payload (ensure data is structured correctly).
3. Inspect the response payload.
   - Did the Server Action return a `success: false` object with validation errors?
   - Or did it return a 500 error (unhandled server crash)? Check the terminal.

## Step 3 — Hydration & Client State (If UI Issue)

If the UI is breaking or flickering:
1. Open React DevTools.
2. Check the props passed from Server Components to Client Components. Are they what you expect?
3. Check hydration errors in the browser console.
   - Are you using `window` or `document` directly in early render cycles without `useEffect`?
   - Ensure you are not putting browser-only APIs in Server Components.

## Step 4 — Database State (If Logic Issue)

If the app isn't behaving based on correct data:
1. Isolate the data query.
2. Run database investigation:
   ```bash
   npx prisma studio
   ```
3. Inspect the raw state in `Prisma Studio` to verify the DB matches your expectations.

## Step 5 — Cache Invalidation (If Stale Data)

If the screen isn't updating after a successful mutation:
1. Is the Server Action calling `revalidatePath` or `revalidateTag`?
2. Test a forceful cache reset: 
   - Temporarily add `export const revalidate = 0` to the top of `page.tsx`.
   - If the data fixes itself, you have a caching bug. Remove the `revalidate = 0` and correctly implement `revalidatePath` in your mutation.

## Step 6 — Routing & Proxy Rules (If 404/Redirect Loop)

If the user is being incorrectly routed:
1. Check `src/proxy.ts`. Route matchers execute in order. 
2. Add console logs inside `proxy.ts` to see what route is being evaluated and what session state it sees.
3. Remember that `params` and `searchParams` in Next.js 16 layouts and pages are now **Promises** and must be `await`ed.

## Step 7 — Strict Type Verification

Before concluding a fix, verify you haven't introduced silent type errors. Run:
// turbo
```bash
npx tsc --noEmit
```
All errors must be resolved.
