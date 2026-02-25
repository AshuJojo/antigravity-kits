# Providers & Auth Helpers

Auth.js v5 standardizes authentication setup using `src/lib/auth.ts` to export the main `auth()` helper, handlers, and specific signin/signout methods.

## Providers

A provider defines the authentication mechanism (e.g., Google, GitHub, Email/Password). You configure them in your Auth.js setup.

- **OAuth Providers** (GitHub, Google, Discord, etc.): Best for most applications. Users authorize via a pop-up or redirect.
- **Credentials Provider**: Used for traditional email/password login. Requires you to handle hashing passwords (e.g., using `bcryptjs`) and explicitly verifying credentials in the `authorize` callback.
   - **Crucial Rule**: When using Credentials, the session strategy *must* be set to JWT.

## Auth Helpers: `auth()` vs `useSession()`

| Context | Helper to Use | How it Works |
|---|---|---|
| **Server Components** | `await auth()` | Direct, high-performance read of the cookies to establish session state. Does not require a wrap Provider. |
| **Server Actions** | `await auth()` | Used to secure mutations. If the session is invalid, you can easily throw an error or return a structured `{ error: "Unauthorized" }`. |
| **Client Components** | `useSession()` or props | Use the `useSession` hook to access reactive auth state on the client. **However**, it is heavily preferred to fetch the session via `auth()` in a Server Component parent and pass the required fields down as props to the Client Component. This avoids unnecessary client-side loading states. |

## Why avoid `<SessionProvider>` globally?

Wrapping the entire `app/layout.tsx` in `<SessionProvider>` forces the framework to load the session context into every client component tree. Instead, wrap *only* the specific client subtree that requires reactive session updates, or pass session data as direct props.
