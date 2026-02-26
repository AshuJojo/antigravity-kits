# Vitest Configuration & Prisma Mocking

To run unit tests quickly without hitting a physical database, we configure Vitest with a global setup file that deeply mocks the Prisma Client.

## 1. Installation

```bash
npm install -D vitest @vitejs/plugin-react jsdom vitest-mock-extended
```

## 2. Context File (`src/lib/__mocks__/prisma.ts`)

Create a dedicated mock instance of your database.

```ts
import { PrismaClient } from '@prisma/client'
import { mockDeep, DeepMockProxy } from 'vitest-mock-extended'

import { db } from '@/lib/db'

// Tells Vitest wherever `db` is imported, substitute it.
vi.mock('@/lib/db', () => ({
  __esModule: true,
  db: mockDeep<PrismaClient>(),
}))

export const prismaMock = db as unknown as DeepMockProxy<PrismaClient>
```

## 3. Setup File (`vitest.setup.ts`)

Create this in the root of your project. It initializes your mock context before every test.

```ts
import { beforeEach, vi } from 'vitest'
import { mockReset } from 'vitest-mock-extended'
import { prismaMock } from './src/lib/__mocks__/prisma'

beforeEach(() => {
  // Clear any resolved data from previous test runs
  mockReset(prismaMock)
})
```

## 4. Config (`vitest.config.ts`)

Ensure Aliases `(@/*)` resolve correctly.

```ts
import { defineConfig } from 'vitest/config'
import react from '@vitejs/plugin-react'
import path from 'path'

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom', // Optional: Use 'node' if strictly testing services
    setupFiles: ['./vitest.setup.ts'],
    globals: true, // Enables `describe`, `it`, `expect` without importing
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
    include: ['src/**/*.test.{ts,tsx}'], // Enforces colocated test files
  },
})
```

## 5. Script (`package.json`)

```json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest"
  }
}
```
