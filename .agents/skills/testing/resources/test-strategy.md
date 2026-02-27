# Test Strategy and File Organization

Our testing approach separates logic verification from UI verification to maximize confidence while minimizing test maintenance overhead.

## The Two Tiers of Testing

### 1. Vitest (Unit / Integration)
- **Target**: `src/components/features/*/services.ts`, `src/components/features/*/actions.ts`, and `src/lib/*`.
- **Methodology**: Fast execution, deep mocked dependencies. The primary goal is verifying business logic, edge cases, and data transformation.
- **Avoid**: Testing UI components or routing changes. Do not use React Testing Library.

### 2. Playwright (End-to-End)
- **Target**: The full integrated stack (`src/app/*` + `src/components/features/*`).
- **Methodology**: Spin up the actual application with a real database. Simulate user clicks and inputs.
- **Rule of thumb**: If the test requires verifying that clicking a button opens a modal or navigates to a new page, it belongs in Playwright.

---

## File Colocation Rules

Where you place your test files is just as important as what you test.

### Unit Tests (`*.test.ts`)
Must be colocated next to the source file they test, within a `__tests__` folder.

**Correct:**
```text
src/
  components/
    features/
      users/
        __tests__/
          services.test.ts
        services.ts
  lib/
    utils.ts
    utils.test.ts
```

**❌ Incorrect:**
```text
tests/
  unit/
    features/
      users/
        services.test.ts
```
*Why? Colocation improves maintainability. When you move or delete `services.ts`, the test naturally follows.*

### End-to-End Tests (`*.spec.ts`)
Must be isolated in an `e2e/` folder at the root of the repository.

**Correct:**
```text
e2e/
  auth.spec.ts
  checkout.spec.ts
src/
  app/
```

**❌ Incorrect:**
```text
src/
  app/
    login/
      page.tsx
      login.spec.ts
```
*Why? E2E tests often cross multiple pages/domains. Tying them to a specific route folder creates confusion when user flows span across the app.*
