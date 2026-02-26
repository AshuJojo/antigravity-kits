---
description: Vercel + NeonDB Production Deployments covering environment variables, build hooks, preview branches, and Core Web Vitals monitoring.
---

# Deployment Strategy

This skill outlines the process, constraints, and standard operating procedures for deploying our Feature-Sliced Next.js 16 applications to Vercel, using NeonDB for our serverless Postgres data layer.

## Key Constraints

- **Never Commit Secrets** — `.env.local` must remain in `.gitignore`. Production and Preview secrets are stored exclusively in the Vercel dashboard.
- **Run Migrations on Build** — You must *never* run `npx prisma db push` against production. Use the Vercel Build Command to run `npx prisma migrate deploy` safely before `next build`.
- **NeonDB Branching** — Every Vercel Preview Deploy should correspond to a unique NeonDB ephemeral branch to prevent polluting the production database.
- **Direct vs Pooled URLs** — Prisma *requires* two database variables: a pooled connection (`DATABASE_URL`) for general app queries, and a direct connection (`DIRECT_URL`) specifically for running migrations during the build step. 

## Strategy: Continuous Git Deployment

We rely entirely on Vercel's Git integration.

1. **Production**: Pushing or merging to the `main` branch immediately triggers a Production deployment on Vercel, migrating the `main` NeonDB database and updating the live URL.
2. **Preview**: Pushing to any non-main branch (e.g., `feature/user-profiles`) triggers a Preview deployment, providing an isolated, shareable URL to test the code against a separate development/branch database.

> **Note**: For complex database schema changes, you *must* generate a migration file locally (`npx prisma migrate dev --name <description>`) and commit that `.sql` file to Git so Vercel can run `migrate deploy` during the build.

## Resources

- [Vercel Environment Setup](./resources/vercel-config.md): Setting up the Build Command and configuring `DATABASE_URL` / `DIRECT_URL`.
- [Environment Tiers](./resources/env-tiers.md): Managing variables across Local, Preview, and Production.
- [NeonDB Branching](./resources/neondb-branching.md): How to deal with preview database instances.

## Workflows

When it is time to deploy an application for the first time, execute the steps in:
[Deploy Workflow](../../workflows/deploy.md)
