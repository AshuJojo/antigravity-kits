---
name: testing
description: Testing strategy for Next.js 16 projects, covering Vitest for fast unit/integration tests (with Prisma mocking) and Playwright for End-to-End (E2E) testing.
---

# Testing Skill

This skill defines the testing boundaries and automation strategy for Next.js 16 applications using the App Router.

**Triggers**: Writing unit tests for services · mocking the database · setting up E2E tests · configuring CI test commands.

## Skill Structure

- **Resources**
  - [Test Strategy](./resources/test-strategy.md): The separation of concerns between Unit/Integration testing (Vitest) and End-to-End testing (Playwright), including rules for file colocation.
- **Examples**
  - [Vitest Setup](./examples/vitest-setup.md): Configuration for Vitest, including paths and the deep-mocking strategy for the Prisma Client.
  - [Service Unit Test Example](./examples/unit-test.md): How to test a Service layer function linked directly to a mock DB.
  - [Action Unit Test Example](./examples/action-test.md): How to strictly test a Server Action by mocking its underlying Service.
  - [Utility Unit Test Example](./examples/utility-test.md): How to test a pure mathematical/formatting utility.
  - [Playwright E2E Example](./examples/playwright-e2e.md): A standard Playwright spec for testing a critical user flow.

## Strategy

1. **Unit & Integration (Vitest)** → Testing the `Service` and `Action` layers. These tests run fast in a Node/JSDOM environment. The database (`PrismaClient`) is fully mocked to prevent flaky state and to keep execution times under a few seconds. Focus on business logic and validation.
2. **End-to-End (Playwright)** → Testing the `Page` and `Component` layers. Playwright spins up a real browser against a running instance of the application (usually against a separate test database). Focus on critical paths (Authentication, Checkout, Core CRUD).

## Key Constraints & Rules

> **Authoritative.** These constraints must be followed.

- **No Component Unit Tests** — Do not use React Testing Library to unit test React Server Components or generic UI components unless absolutely necessary for complex client-side interactions. The return on investment is low. Test the UI behavior via Playwright instead.
- **Mock the DB in Vitest** — Unit tests must never hit a real database. Always mock the `PrismaClient` using `vitest-mock-extended`.
- **Colocate Unit Tests** — Unit tests must live directly next to the file they are testing in a `__tests__` folder (e.g., `services.ts` and `__tests__/services.test.ts` inside `src/components/features/users/`). Do *not* put them in a separate global `/tests` folder.
- **Isolate E2E Tests** — Playwright tests *must* live in a dedicated `e2e/` folder at the root of the project, completely separate from application code.
