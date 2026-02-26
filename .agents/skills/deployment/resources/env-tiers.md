# Environment Variable Tiers

In a production Next.js 16 + NeonDB application, environment variables follow a strict 3-tier hierarchy. You must **never** hardcode these values or store production secrets in the git repository.

## 1. Local Development (`.env.local`)

This file stays on your machine and is specifically excluded via `.gitignore`. It connects your `npm run dev` environment to your dev database.

```env
# Example .env.local
DATABASE_URL="postgres://user:pass@ep-local-db.neon.tech/neondb?sslmode=require"
DIRECT_URL="postgres://user:pass@ep-local-db.neon.tech/neondb"
AUTH_SECRET="your-local-secret"
NEXT_PUBLIC_APP_URL="http://localhost:3000"
```

## 2. Preview Environments (Vercel)

When a developer opens a Pull Request or pushes to a branch (e.g. `feature/auth`), Vercel builds a "Preview" deployment.

**Goal**: Avoid mutating the production database while testing new features.

In your Vercel Project Settings → Environment Variables:
1. Select the **Preview** environment checkbox.
2. Provide a **branch-specific** `DATABASE_URL` and `DIRECT_URL`.
3. Provide mocked or non-production API keys (e.g., Stripe Test Secret Key instead of Live).

## 3. Production Environment (Vercel)

When code merges into `main`, Vercel builds the "Production" deployment.

In your Vercel Project Settings → Environment Variables:
1. Select the **Production** environment checkbox.
2. Provide your **primary Live** `DATABASE_URL` and `DIRECT_URL`.
3. Ensure `NEXT_PUBLIC_APP_URL` points to the final live domain (e.g., `https://my-app.com`).
4. Provide live API keys (e.g., Stripe Live Secret Key, real Auth.js provider Client Secret).

## Security Rule

Any variable prefixed with `NEXT_PUBLIC_` is inherently unsafe—it is injected directly into the client-side JavaScript bundle. 

**Never** prefix database connection strings, Stripe Secret Keys, or Auth secrets with `NEXT_PUBLIC_`.
