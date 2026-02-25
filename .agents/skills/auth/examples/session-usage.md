# Session Usage Examples

How to access authentication state across Server Components, Client Components, and Server Actions.

## In a Server Component (Preferred)

Server Components should read the session cookie directly using `auth()`. This triggers no client-side loading states.

```tsx
// src/components/layout/user-nav.tsx
import { auth, signOut } from "@/lib/auth"
import Image from "next/image"

export async function UserNav() {
  const session = await auth()

  if (!session?.user) {
    return <a href="/api/auth/signin">Log in</a>
  }

  return (
    <div className="flex items-center gap-4">
      <Image
        src={session.user.image ?? "/default-avatar.png"}
        alt="Avatar"
        width={32}
        height={32}
        className="rounded-full"
      />
      <span>{session.user.name}</span>
      <form action={async () => {
        "use server"
        await signOut()
      }}>
        <button type="submit">Sign Out</button>
      </form>
    </div>
  )
}
```

## In a Server Action (Mutation guard)

Protect mutations by checking the user's ID or Role immediately.

```ts
// src/actions/post.actions.ts
"use server"

import { auth } from "@/lib/auth"
import { db } from "@/lib/db"

export async function createPost(formData: FormData) {
  const session = await auth()
  
  if (!session?.user?.id) {
    return { error: "You must be logged in." }
  }

  const title = formData.get("title") as string

  await db.post.create({
    data: {
      title,
      authorId: session.user.id, // Safe, bound to authenticated user
    }
  })

  return { success: true }
}

export async function deleteAnyPost(postId: string) {
  const session = await auth()
  
  if (session?.user?.role !== "ADMIN") {
    return { error: "Forbidden. Admin access required." }
  }

  await db.post.delete({ where: { id: postId } })
  return { success: true }
}
```

## In a Client Component (Passed as Props - Recommended)

```tsx
// src/app/(app)/dashboard/page.tsx (Server Component)
import { auth } from "@/lib/auth"
import { DashboardClient } from "./dashboard-client"

export default async function DashboardPage() {
  const session = await auth()
  return <DashboardClient userId={session?.user?.id} />
}
```

```tsx
// src/app/(app)/dashboard/dashboard-client.tsx (Client Component)
"use client"

export function DashboardClient({ userId }: { userId?: string }) {
  if (!userId) return <p>Please log in.</p>
  return <div>Welcome, your ID is: {userId}</div>
}
```
