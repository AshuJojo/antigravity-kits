# Testing Pure Utilities

Pure utility functions (functions that take an input and return an output without external side effects like DB calls or network requests) are the easiest and most important things to test. They should have near 100% coverage.

## The Utility (`src/lib/utils/format.ts`)

```ts
export function formatCurrency(amount: number, currency: string = "USD"): string {
  if (isNaN(amount)) {
    throw new Error("Invalid amount provided to formatCurrency")
  }

  return new Intl.NumberFormat("en-US", {
    style: "currency",
    currency: currency,
  }).format(amount)
}
```

## The Test (`src/lib/utils/__tests__/format.test.ts`)

Place the `__tests__` folder directly next to the utilities files for immediate colocation.

```ts
import { describe, it, expect } from "vitest"
import { formatCurrency } from "../format"

describe("formatCurrency", () => {
  it("formats standard USD correctly", () => {
    expect(formatCurrency(1200)).toBe("$1,200.00")
  })

  it("formats zero amounts correctly", () => {
    expect(formatCurrency(0)).toBe("$0.00")
  })

  it("handles negative amounts correctly", () => {
    expect(formatCurrency(-50.25)).toBe("-$50.25")
  })

  it("formats alternative currencies correctly", () => {
    expect(formatCurrency(1200, "EUR")).toBe("€1,200.00")
    expect(formatCurrency(1200, "GBP")).toBe("£1,200.00")
  })

  it("throws an error if NaN is provided", () => {
    // When asserting that a function throws, you must wrap the execution in a callback
    expect(() => formatCurrency(NaN)).toThrow("Invalid amount provided to formatCurrency")
  })
})
```
