# Auth.js Configuration Template

This is the standard `auth.ts` configuration for NextAuth v5 applying the JWT session strategy and appending the user ID to the session object.

## `src/lib/auth.ts`

```ts
import NextAuth, { type DefaultSession } from "next-auth"
import GitHub from "next-auth/providers/github"
import Google from "next-auth/providers/google"
import Credentials from "next-auth/providers/credentials"
import { PrismaAdapter } from "@auth/prisma-adapter"
import { db } from "@/lib/db"
import { z } from "zod"

// Extend NextAuth session types to include user ID and role
declare module "next-auth" {
  interface Session {
    user: {
      id: string
      role: "USER" | "ADMIN"
    } & DefaultSession["user"]
  }
}

export const { handlers, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(db),
  session: { strategy: "jwt" },
  providers: [
    GitHub,
    Google,
    Credentials({
      credentials: {
        email: { label: "Email", type: "email" },
        password: { label: "Password", type: "password" },
      },
      async authorize(credentials) {
        const parsedCredentials = z
          .object({ email: z.string().email(), password: z.string().min(6) })
          .safeParse(credentials)

        if (!parsedCredentials.success) return null

        const { email, password } = parsedCredentials.data

        const user = await db.user.findUnique({ where: { email } })
        if (!user || user.password !== password) {
          // Note: In production, use bcrypt.compareSync or similar!
          return null
        }
        
        return user
      },
    }),
  ],
  callbacks: {
    jwt({ token, user }) {
      // User is available during sign-in
      if (user) {
        token.id = user.id
        token.role = user.role
      }
      return token
    },
    session({ session, token }) {
      if (typeof token.id === "string") {
        session.user.id = token.id
      }
      if (typeof token.role === "string") {
        session.user.role = token.role as "USER" | "ADMIN"
      }
      return session
    },
  },
})
```

## `src/app/api/auth/[...nextauth]/route.ts`

This file mounts the Auth.js API routes (sign-in, callback, session check).

```ts
import { handlers } from "@/lib/auth"

export const { GET, POST } = handlers
```
