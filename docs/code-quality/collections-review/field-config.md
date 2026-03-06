# Field Configuration Analysis

Breaking down a field configuration with duplicated types and unnecessary functions.

---

## Current Implementation

```typescript
export type FieldOptions = {
  fieldType: FieldTypeInstance
  required?: boolean
  unique?: boolean
  indexed?: boolean
  default?: unknown
  label?: string
  description?: string
}

export const field = (config: FieldOptions): FieldDefinition => {
  return {
    fieldType: config.fieldType,
    required: config.required ?? false,
    unique: config.unique ?? false,
    indexed: config.indexed ?? false,
    default: config.default,
    label: config.label,
    description: config.description
  }
}

export type FieldDefinition = {
  fieldType: FieldTypeInstance
  required: boolean
  unique: boolean
  indexed: boolean
  default?: unknown
  label?: string
  description?: string
}
```

---

## What's Wrong

### 1. Duplicated Types

```typescript
type FieldOptions = {      // Input - optional fields
  required?: boolean
  unique?: boolean
}

type FieldDefinition = {    // Output - required fields
  required: boolean
  unique: boolean
}
```

Almost identical. Only difference is `?` on some fields.

### 2. `default?: unknown`

Using `unknown` for the default value. Should be specific or use a union.

### 3. Unnecessary Function

```typescript
const field = (config: FieldOptions): FieldDefinition => ({
  required: config.required ?? false,
  // ...spreads everything
})
```

This just copies properties with defaults. No validation, no transformation. Why a function?

### 4. Repetitive Defaults

```typescript
required: config.required ?? false,
unique: config.unique ?? false,
indexed: config.indexed ?? false,
```

Same pattern 3 times. Could be a helper.

---

## What It Should Be

### Single Type

```typescript
// One type for a field
type Field = {
  fieldType: FieldTypeInstance
  required: boolean
  unique: boolean
  indexed: boolean
  default?: DefaultValue  // Specific type, not unknown
  label?: string
  description?: string
}
```

### Simple Factory (if needed)

```typescript
// If you need a factory, keep it simple
const field = (config: Partial<Field>): Field => ({
  required: false,
  unique: false,
  indexed: false,
  ...config
})

// Usage
const name = field({ fieldType: textField, required: true })
```

### Default Value Type

```typescript
// Specific type for default values
type DefaultValue =
  | string
  | number
  | boolean
  | null
  | { [key: string]: DefaultValue }
  | DefaultValue[]
```

---

## Summary

| Before | After |
|--------|-------|
| Two types (Options + Definition) | One type |
| `default?: unknown` | `default?: DefaultValue` |
| Unnecessary function | Simple factory or type only |
| Repetitive defaults | Spread operator |

---

## Key Rules

1. Don't duplicate types for input/output
2. Use specific types, not `unknown`
3. Functions should transform/validate, not just copy
4. Use spread for defaults

---

*Document version: 1.0*
*Last updated: 2026-03-05*
