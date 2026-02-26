# Playwright Example

Playwright spins up the actual application and a real browser, allowing you to test critical user flows (Authentication, Checkout) from end-to-end.

## Installation

```bash
npm init playwright@latest
```
*(Choose TypeScript, and put tests in the `e2e` folder at the project root.)*

## `playwright.config.ts` 

Ensure Playwright starts your Next.js dev server before running tests.

```ts
import { defineConfig, devices } from '@playwright/test'

export default defineConfig({
  testDir: './e2e',
  fullyParallel: true,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  projects: [
    { name: 'chromium', use: { ...devices['Desktop Chrome'] } },
    // Add Firefox, WebKit, Mobile Safari etc. as needed
  ],
  // Spin up Next.js automatically
  webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
})
```

## E2E Test (`e2e/auth-flow.spec.ts`)

End-to-end tests should be isolated to the root `e2e/` folder, testing the full stack across page boundaries.

```ts
import { test, expect } from '@playwright/test'

test.describe('Authentication Flow', () => {

  test('User can log in and view dashboard', async ({ page }) => {
    // 1. Navigate to home
    await page.goto('/')
    
    // 2. Click login link
    await page.click('text=Log In')
    
    // 3. Verify URL changed
    await expect(page).toHaveURL(/.*\/login/)
    
    // 4. Fill in credentials
    await page.fill('input[name="email"]', 'test@example.com')
    await page.fill('input[name="password"]', 'password123')
    
    // 5. Submit form
    await page.click('button[type="submit"]')
    
    // 6. Verify successful redirect to protected dashboard
    await expect(page).toHaveURL(/.*\/dashboard/)
    
    // 7. Verify user avatar or name appears
    await expect(page.locator('text=Welcome back, test!')).toBeVisible()
  })

})
```
