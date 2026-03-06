# Double Function Pattern

When returning a function from a function is valid — and when it's not.

---

## The Intent: Optional Configuration

The goal is to support both:

```typescript
text()                    // Field without options
text({ min: 4, max: 20 }) // Field with options
```

This requires the function to accept optional parameters and return different results.

---

## Why Current Implementation Doesn't Work

```typescript
export const fieldType = (config: FieldTypeConfig): (() => FieldTypeInstance) => {
  return () => ({
    schema: config.schema,
    database: config.database ?? {}
  })
}
```

This doesn't support options at all. The config is static.

---

## Valid Pattern: Options as Parameters

```typescript
type FieldOptions = {
  min?: number
  max?: number
  required?: boolean
}

const text = (options?: FieldOptions): Field => {
  let schema = z.string()

  if (options?.min) schema = schema.min(options.min)
  if (options?.max) schema = schema.max(options.max)

  return {
    schema,
    database: { type: 'text' }
  }
}

// Usage
text()                    // { schema: z.string(), database: { type: 'text' } }
text({ min: 4 })          // { schema: z.string().min(4), database: { type: 'text' } }
text({ min: 4, max: 20 }) // { schema: z.string().min(4).max(20), database: { type: 'text' } }
```

This works perfectly. No double function needed.

---

## When Double Function IS Valid

The double function pattern is valid when:

1. **Factory pattern**: You create a factory that returns configured functions
2. **Lazy evaluation**: You don't want to compute until called
3. **Reuse**: The same configuration creates multiple instances

```typescript
// Valid use case: factory
const createValidator = (rules) => (data) => validate(data, rules)
const validateEmail = createValidator(emailRules)

// Valid use case: lazy
const expensiveComputation = () => computeExpensiveThing()
```

---

## When Double Function is NOT Valid

When the outer function could simply return the result:

```typescript
// Not valid: just returns static data
const fieldType = (config) => () => ({ schema: config.schema })

// Should be:
const fieldType = (config) => ({ schema: config.schema })
```

---

## Summary

| Pattern | Valid? | When |
|---------|--------|------|
| `(opts?) => T` | ✅ | Optional configuration |
| `() => () => T` | ❌ | Static return |
| `(a) => (b) => T` | ✅ | Factory pattern |
| `(a) => () => T` | ❌ | Could return T directly |

---

*Document version: 1.0*
*Last updated: 2026-03-05*
