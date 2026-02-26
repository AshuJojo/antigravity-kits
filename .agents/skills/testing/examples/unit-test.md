# Unit Test Example

If you define business logic tightly within a **Service** function, testing becomes straightforward.

The example below tests a typical Next.js Server Action or Service, using our strictly-mocked Prisma instance.

## Service Under Test (`src/components/features/users/services.ts`)

```ts
import { db } from "@/lib/db"

export async function promoteUserToAdmin(email: string) {
  const user = await db.user.findUnique({ where: { email } })

  if (!user) {
    throw new Error("User not found")
  }

  if (user.role === "ADMIN") {
    return user // Already admin
  }

  return db.user.update({
    where: { email },
    data: { role: "ADMIN" }
  })
}
```

## Unit Test (`src/components/features/users/__tests__/services.test.ts`)

Because `vitest.setup.ts` already hijacks imports to `db`, we import our `prismaMock` to control its behavior during the test.

```ts
import { describe, it, expect } from "vitest"
import { prismaMock } from "@/lib/__mocks__/prisma"
import { promoteUserToAdmin } from "../services"

describe("promoteUserToAdmin Service", () => {

  it("should throw an error if the user does not exist", async () => {
    // 1. Arrange: Tell the mock DB to return null
    prismaMock.user.findUnique.mockResolvedValue(null)

    // 2. Act & Assert: Expect the operation to throw
    await expect(promoteUserToAdmin("ghost@example.com")).rejects.toThrow("User not found")
  })

  it("should return the user without an update call if they are already an ADMIN", async () => {
    // 1. Arrange: Mock the DB returning an existing admin
    const mockUser = { id: "1", email: "admin@example.com", role: "ADMIN" } as any
    prismaMock.user.findUnique.mockResolvedValue(mockUser)

    // 2. Act
    const result = await promoteUserToAdmin("admin@example.com")

    // 3. Assert: Verify the result and ensure `update` was never called
    expect(result.role).toBe("ADMIN")
    expect(prismaMock.user.update).not.toHaveBeenCalled()
  })

  it("should update the user role to ADMIN if they are a standard USER", async () => {
    // 1. Arrange: Mock initial lookup and the update return value
    const mockUser = { id: "1", email: "user@example.com", role: "USER" } as any
    const updatedUser = { ...mockUser, role: "ADMIN" }

    prismaMock.user.findUnique.mockResolvedValue(mockUser)
    prismaMock.user.update.mockResolvedValue(updatedUser)

    // 2. Act
    const result = await promoteUserToAdmin("user@example.com")

    // 3. Assert
    expect(result.role).toBe("ADMIN")
    // Verify Prisma was called with the exact right arguments
    expect(prismaMock.user.update).toHaveBeenCalledWith({
      where: { email: "user@example.com" },
      data: { role: "ADMIN" }
    })
  })

})
```
