# Session Strategy

Auth.js supports two distinct session strategies: **JWT** and **Database**. Understanding when to use which is critical.

## Decision Flow

| Strategy | When to use | Pros | Cons |
|---|---|---|---|
| **Database (Default with Adapters)** | Standard OAuth logins (Google, GitHub, etc.) where database-backed session revocation is needed. | Sessions can be securely revoked from the database. | Requires a DB lookup on every `auth()` call. Cannot be used with the Credentials provider. |
| **JWT** | When using the Credentials provider (email/password), or when minimizing DB hits is the absolute highest priority. | Fast, stateless, works with Credentials provider. | Difficult to revoke instantly (requires short expiries + rotation). Payload size must be kept small. |

## Golden Rule for Credentials

> **If you are implementing a custom email/password login (Credentials provider), you MUST set `strategy: "jwt"` in your session config.** Auth.js explicitly prevents using Database sessions with Credentials for security/architectural reasons.

## Session Shape configuration

By default, Auth.js returns a minimal session object (`name`, `email`, `image`).

To expose additional fields like `id` or `role` to the client and server:
1. You must append them in the `jwt` callback (if using JWT strategy).
2. You must append them in the `session` callback (reading from `token` for JWT, or `user` for Database).

Refer to [Auth Configuration](../examples/auth-config.md) for adding the user `id` to the session.
