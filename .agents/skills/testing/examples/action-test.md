# Testing Server Actions

Server Actions act as the bridge between your Client Components and your Business Logic (Services). When testing an Action, the goal is to verify that it correctly handles validation (Zod) and correctly maps the Service layer's success/failure states into your standard `ActionState` format.

Since we already cover the actual database logic in the Service tests (see `unit-test.md`), we should **mock the service layer** entirely when testing the Action layer.

## The Action (`src/components/features/users/actions.ts`)

```ts
"use server"

import { revalidatePath } from "next/cache"
import { createUserSchema } from "./validations"
import { createUserService } from "./services"
import { safeAction } from "@/lib/utils/action-wrapper" // Our standard error wrapper

export const createUserAction = safeAction(createUserSchema, async (data) => {
  const user = await createUserService(data)
  revalidatePath("/users")
  return { success: true, message: "User created!", data: user }
})
```

## The Action Test (`src/components/features/users/__tests__/actions.test.ts`)

```ts
import { describe, it, expect, vi } from "vitest"
import { createUserAction } from "../actions"
import * as services from "../services"
import * as cache from "next/cache"

// 1. Mock the next/cache module to prevent real revalidation during tests
vi.mock("next/cache", () => ({
  revalidatePath: vi.fn(),
}))

// 2. Mock our feature's service layer to isolate the action logic
vi.mock("../services", () => ({
  createUserService: vi.fn(),
}))

describe("createUserAction Server Action", () => {
  it("returns validation errors if the input data violates the Zod schema", async () => {
    // Act: Pass an empty object representing bad FormData
    const result = await createUserAction({ email: "not-an-email" })

    // Assert: Action should catch the error and return fieldErrors
    expect(result.success).toBe(false)
    expect(result.error).toBeDefined()
    expect(result.error?.email).toStrictEqual(["Invalid email address"])
    
    // Service should never have been called because validation failed first
    expect(services.createUserService).not.toHaveBeenCalled()
  })

  it("calls the service, revalidates cache, and returns success on valid input", async () => {
    // Arrange: Mock the service to resolve successfully
    const mockUser = { id: "123", email: "test@test.com", name: "Test User" }
    vi.mocked(services.createUserService).mockResolvedValue(mockUser as any)

    // Act
    const result = await createUserAction({ email: "test@test.com", name: "Test User" })

    // Assert
    expect(services.createUserService).toHaveBeenCalledWith({ email: "test@test.com", name: "Test User" })
    expect(cache.revalidatePath).toHaveBeenCalledWith("/users")
    
    expect(result.success).toBe(true)
    // TypeScript knows `result` has `data` when success is true based on our ActionState shape
    if (result.success) {
      expect(result.data).toStrictEqual(mockUser)
    }
  })

  it("returns a generic error message if the service throws an unexpected error", async () => {
    // Arrange: Mock the service to simulate a catastrophic DB failure
    vi.mocked(services.createUserService).mockRejectedValue(new Error("Database connection lost"))

    // Act
    const result = await createUserAction({ email: "test@test.com", name: "Test User" })

    // Assert: safeAction should catch the generic error and safely format it
    expect(result.success).toBe(false)
    expect(result.message).toBe("An unexpected error occurred.")
  })
})
```
