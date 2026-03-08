## Refactor Summary
<!-- One sentence explaining what is refactored -->

Example: Refactor payment processing to use Result<T, E> pattern

---

## Motivation

### Why refactor?
<!-- Explain the problem with current code -->
- Current code uses exceptions for error handling
- Difficult to test error paths
- No type safety on error cases

### What problem does it solve?
<!-- Benefits of this refactor -->
- Explicit error handling with Result<T, E>
- Better testability
- Type-safe error propagation

---

## Type of Change

- [ ] **Refactor** (no behavior change)
- [ ] **Architecture** (structural change)

### Behavior Contract
- [ ] **No behavior changes** - Output is identical
- [ ] **No API changes** - Public signatures unchanged
- [ ] **Tests updated** - Tests verify same behavior

---

## Changes Made

### Refactoring Approach
<!-- Explain the refactoring technique used -->
- Martin Fowler's "Replace Error Code with Exception" in reverse
- Using Result<T, E> monad
- Progressive migration: internal functions first, then public API

### Before & After Comparison

#### Before (Code with Issues)
```typescript
// Uses exceptions
async function processPayment(
  amount: number,
  method: PaymentMethod
): Promise<Payment> {
  if (amount <= 0) {
    throw new Error('Invalid amount')
  }

  if (method.expired) {
    throw new Error('Payment method expired')
  }

  // ... processing
  return payment
}

// Usage: error-prone
try {
  const payment = await processPayment(100, method)
} catch (e) {
  // What errors can occur? Unknown!
}
```

#### After (Refactored Code)
```typescript
// Explicit errors
type PaymentError =
  | { type: 'invalid_amount'; amount: number }
  | { type: 'payment_expired'; methodId: string }
  | { type: 'gateway_error'; message: string }

async function processPayment(
  amount: number,
  method: PaymentMethod
): Promise<Result<Payment, PaymentError>> {
  if (amount <= 0) {
    return Result.err({
      type: 'invalid_amount',
      amount
    })
  }

  if (method.expired) {
    return Result.err({
      type: 'payment_expired',
      methodId: method.id
    })
  }

  // ... processing
  return Result.ok(payment)
}

// Usage: type-safe
const result = await processPayment(100, method)

if (!result.ok) {
  switch (result.error.type) {
    case 'invalid_amount':
      // Handle specific error
      break
    case 'payment_expired':
      // Handle specific error
      break
    case 'gateway_error':
      // Handle specific error
      break
  }
}
```

### Files Refactored
```bash
src/payment/processor.ts     # Main refactor
src/payment/types.ts         # New error types
src/payment/api.ts           # Updated to use Result
tests/payment/processor.test.ts  # Updated tests
```

---

## Test Strategy

### Refactoring Tests
- [ ] All existing tests still pass
- [ ] No test behavior changed
- [ ] Tests verify same outputs

### New Tests
- [ ] Tests for new error types
- [ ] Tests for error propagation
- [ ] Integration tests updated

### Regression Check
```bash
# Run full test suite
npm run test

# Run integration tests
npm run test:integration

# Verify no behavior changes
npm run test:regression
```

---

## Metrics

### Code Quality Improvements
<!-- Measurable improvements -->
- Cyclomatic complexity: 15 → 8
- Lines of code: 450 → 380
- Test coverage: 82% → 94%
- `any` types: 12 → 0

### Performance
<!-- Performance impact -->
- [ ] No performance change
- [ ] Performance improved
- [ ] Slight performance degradation (acceptable)

---

## Migration Path

### Backwards Compatibility
- [ ] Fully compatible (no changes needed)
- [ ] Minor breaking changes (documented)
- [ ] Major breaking changes (migration guide provided)

### Migration Required
<!-- If consumers need to update code -->
```typescript
// Before
const payment = await processPayment(100, method)

// After
const result = await processPayment(100, method)
if (!result.ok) {
  // Handle error
}
```

---

## Documentation

- [ ] README updated with new patterns
- [ ] Migration guide provided (if needed)
- [ ] Code examples updated
- [ ] JSDoc updated

---

## Open Questions

<!-- Design decisions that need discussion -->
- [ ] Should we add a `PaymentResult` type alias?
- [ ] Migration timeline for other payment functions

---

*Refactoring Impact: 3 services, 8 functions*
*Estimated Migration Effort: 2 hours*
