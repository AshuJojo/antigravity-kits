# NeonDB Branching Strategy

One of the largest benefits of NeonDB (Serverless Postgres) is database *branching*. Just like Git allows you to branch code without affecting the `main` branch, NeonDB allows you to instantly branch your database (schema + data) in less than a second.

This perfectly compliments Next.js architecture and Vercel's Preview deployments.

## The Strategy

1. **`main` Branch (Production)**
   - Corresponds strictly to the Vercel **Production** environment.
   - Holds your live user data.
   - Only receives schema migrations via Vercel's `prisma migrate deploy` step during a `main` code merge.

2. **Developer Branches (`dev/ashu`, `dev/jojo`)**
   - Each developer clicks "New Branch" in the NeonDB dashboard, branching off the `main` database.
   - You drop these connection strings into your local `.env.local` file.
   - **Why?** You can freely run `npx prisma db push` and `npx prisma studio` to wipe/seed/mutate your local database schema without affecting staging or production.

3. **Preview/Staging Branches (`staging`)**
   - A dedicated NeonDB branch used purely to catch schema migration errors before they hit production.
   - Corresponds to Vercel's **Preview** environments.
   - When reviewing a PR, reviewers can interact with the Vercel Preview URL and confidentally generate test data in the `staging` database without touching production.

## Generating Migrations

If you use `npx prisma db push` during local development, your `main` database *does not* know about those changes.

Before opening a Pull Request to `main`, you must generate a formal migration diff:

```bash
# Compare your local schema to the dev db branch, and generate a .sql file
npx prisma migrate dev --name summarize_my_changes
```

You then **commit this `.sql` file** to Git. When Vercel builds the `main` branch, it reads the `.sql` file and safely updates the real production NeonDB database via the `npx prisma migrate deploy` command.
