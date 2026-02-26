---
description: Deploy a Next.js 16 + NeonDB application to Vercel
---

# Deploy Workflow

Follow these steps exactly when taking an Antigravity Next.js application to production on Vercel for the first time.

> **Skill Reference:** [Deployment Skill](../skills/deployment/SKILL.md)

---

## Phase 1 — Database Preparation

1. Open your terminal and ensure all local testing data and schemas are committed.
2. If you have been using `npx prisma db push` locally, you must generate a migration file for production:
   // turbo
   ```bash
   npx prisma migrate dev --name init_production
   ```
3. Commit the new `prisma/migrations/` file to your Git repository.
4. Push your `main` branch to GitHub:
   // turbo
   ```bash
   git push origin main
   ```

## Phase 2 — Vercel Setup

1. Log in to Vercel and click **Add New... → Project**.
2. Select your repository from GitHub.
3. In the **Build and Output Settings** section, override the default "Build Command" with the following exactly:
   ```bash
   npx prisma generate && npx prisma migrate deploy && next build
   ```
   *(Why? Because Prisma requires the production DB to be migrated before Next.js compiles the server routes).*

## Phase 3 — Environment Variables

1. Expand the **Environment Variables** section before clicking deploy.
2. Add your **Production** Environment Variables from `.env.local`:
   - `DATABASE_URL` (From NeonDB → Pooled connection string)
   - `DIRECT_URL` (From NeonDB → Direct connection string)
   - `AUTH_SECRET` (Generate a secure random string)
   - `NEXT_PUBLIC_APP_URL` (Your final Vercel production domain, e.g., `https://my-app.vercel.app`)
3. Click **Deploy**.

## Phase 4 — Verification

1. Once the build finishes successfully, Vercel will provide a live URL.
2. Open the URL. Run a quick manual smoke test:
   - Does navigation work?
   - Can you trigger a Server Action or submit a form without a 500 error?
3. (Optional) Check the **Vercel Analytics** tab to ensure the route is reporting healthy Core Web Vitals.
