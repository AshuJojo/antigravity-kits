# Vercel Configuration & Build Hooks

By default, Vercel runs `npm run build` which triggers Next.js to build. However, because our application relies on a strictly-typed Prisma database schema, we must ensure the production database schema matches the deployed code *before* the application boots up.

## The Custom Build Command

Instead of the default command, the Vercel project MUST be configured to use this specific **Build Command**:

```bash
npx prisma generate && npx prisma migrate deploy && next build
```

### Breakdown:
1. `npx prisma generate`: Reads your `schema.prisma` file and generates the TypeScript client so the Next.js build step can type-check correctly.
2. `npx prisma migrate deploy`: Automatically applies any pending SQL migration files (found in `prisma/migrations/`) to your production NeonDB database. *This does not wipe data, it applies safe SQL diffs.*
3. `next build`: Generates the optimized Next.js production build, pre-rendering static Route components.

## `vercel.json` (Optional)

If you wish to codify this in the repository rather than the Vercel dashboard, you can create a `vercel.json` at the root of the project:

```json
{
  "buildCommand": "npx prisma generate && npx prisma migrate deploy && next build"
}
```

## NeonDB URLs during Build

For `npx prisma migrate deploy` to work, Prisma needs a direct connection to the database (it cannot run migrations over a pooled connection).

You MUST ensure both of these exist in your Vercel Environment Variables:
- `DATABASE_URL` (Pooled, for app runtime)
- `DIRECT_URL` (Direct, for build-time migrations)

If `DIRECT_URL` is completely missing, the build will fail.
