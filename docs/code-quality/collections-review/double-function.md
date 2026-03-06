# Double Function Pattern

Why returning a function from a function creates confusion.

---

## Current Implementation

```typescript
export const fieldType = (config: FieldTypeConfig): (() => FieldTypeInstance) => {
  return () => ({
    schema: config.schema,
    database: config.database ?? {}
  })
}

// Usage:
const text = fieldType({ schema: z.string(), database: { type: 'text' } })
// text is a function, not the result

const textField = text()  // Need to call again to get result
```

---

## What's Wrong

### 1. Double Call

```typescript
fieldType({ ... })()  // Call twice to get result
```

The function returns another function that must be called. Why?

### 2. Confusing API

```typescript
// What is text?
const text = fieldType({ schema: z.string() })

// Is it:
// A) The field itself?
// B) A function that returns the field?
// C) A factory?

// Can't tell from the code.
```

### 3. Useless Types

```typescript
type FieldTypeInstance = { schema, database }
type FieldTypeCreator = () => FieldTypeInstance  // Just describes what fieldType returns

// These are redundant. FieldTypeCreator = what fieldType returns.
```

---

## What It Should Be

```typescript
// Simple: function returns the thing
type Field = {
  schema: z.ZodType
  database: unknown
}

const field = (config: { schema: z.ZodType; database?: unknown }): Field => ({
  schema: config.schema,
  database: config.database ?? {}
})

// Usage:
const text = field({ schema: z.string(), database: { type: 'text' } })
// text IS the field. No second call needed.
```

---

## The Rule

> If a function returns `T`, return `T`, not `() => T`.

```
❌ const fieldType = (config) => () => result
✅ const field = (config) => result
```

---

## Summary

| Before | After |
|--------|-------|
| `fieldType()()` | `field()` |
| Returns function | Returns value |
| Must call twice | Call once |
| Confusing | Clear |

---

*Document version: 1.0*
*Last updated: 2026-03-05*
