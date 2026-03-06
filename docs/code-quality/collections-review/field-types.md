# Field Types Analysis

Breaking down a field type factory with magic strings and duplicated patterns.

---

## Current Implementation

```typescript
const getItemType = (itemSchema: z.ZodType): string => {
  if (itemSchema instanceof z.ZodString) return 'text'
  if (itemSchema instanceof z.ZodNumber) return 'integer'
  if (itemSchema instanceof z.ZodBoolean) return 'boolean'
  if (itemSchema instanceof z.ZodDate) return 'timestamp'
  if (itemSchema instanceof z.ZodEnum) return 'text'
  if (itemSchema instanceof z.ZodArray) return 'array'
  if (itemSchema instanceof z.ZodObject) return 'jsonb'
  return 'text'
}

export const f = {
  text: () => fieldType({ schema: z.string(), database: { type: 'text' } })(),
  email: () => fieldType({ schema: z.string().email(), database: { type: 'text' } })(),
  url: () => fieldType({ schema: z.string().url(), database: { type: 'text' } })(),
  number: () => fieldType({ schema: z.number(), database: { type: 'integer' } })(),
  boolean: () => fieldType({ schema: z.boolean(), database: { type: 'boolean' } })(),
  date: () => fieldType({ schema: z.date(), database: { type: 'date' } })(),
  timestamp: () => fieldType({ schema: z.date(), database: { type: 'timestamp' } })(),
  // ...
}
```

---

## What's Wrong

### 1. Naive If/Else Mapping

```typescript
if (itemSchema instanceof z.ZodString) return 'text'
if (itemSchema instanceof z.ZodNumber) return 'integer'
// ... 7 conditions
return 'text'  // silent fallback
```

Adding new types requires adding new if/else branches. Easy to forget one.

### 2. Magic Strings Everywhere

```typescript
database: { type: 'text' }
database: { type: 'integer' }
database: { type: 'boolean' }
```

No constants. Easy to typo `'tex'` instead of `'text'`.

### 3. Silent Fallback

```typescript
return 'text'  // if unknown type, returns 'text' silently
```

The caller doesn't know it was a fallback.

### 4. Repetitive Pattern

```typescript
text: () => fieldType({ schema: z.string(), database: { type: 'text' } })(),
email: () => fieldType({ schema: z.string().email(), database: { type: 'text' } })(),
url: () => fieldType({ schema: z.string().url(), database: { type: 'text' } })(),
```

Same structure repeated. Should be a helper.

### 5. Double Call

```typescript
fieldType({ ... })()  // ← calls fieldType, then calls result
```

Confusing design. What does the second `()` do?

---

## What It Should Be

### Step 1: Constants for Database Types

```typescript
const DB_TYPE = {
  TEXT: 'text',
  INTEGER: 'integer',
  BOOLEAN: 'boolean',
  DATE: 'date',
  TIMESTAMP: 'timestamp',
  ARRAY: 'array',
  JSONB: 'jsonb',
} as const

type DatabaseType = typeof DB_TYPE[keyof typeof DB_TYPE]
```

### Step 2: Lookup Table for Zod Mapping

```typescript
const ZOD_TO_DB: Record<string, DatabaseType> = {
  [z.ZodString]: DB_TYPE.TEXT,
  [z.ZodNumber]: DB_TYPE.INTEGER,
  [z.ZodBoolean]: DB_TYPE.BOOLEAN,
  [z.ZodDate]: DB_TYPE.TIMESTAMP,
  [z.ZodEnum]: DB_TYPE.TEXT,
  [z.ZodArray]: DB_TYPE.ARRAY,
  [z.ZodObject]: DB_TYPE.JSONB,
}

const getItemType = (itemSchema: z.ZodType): Result<DatabaseType, UnknownType> => {
  const type = ZOD_TO_DB[itemSchema.constructor.name]
  return type
    ? Result.ok(type)
    : Result.err({ type: 'unknown', schema: itemSchema })
}
```

### Step 3: Simple Factory

```typescript
const field = (
  schema: z.ZodType,
  dbType: DatabaseType
): FieldTypeInstance => ({
  schema,
  database: { type: dbType }
})
```

### Step 4: Cleaner Exports

```typescript
export const f = {
  text: () => field(z.string(), DB_TYPE.TEXT),
  email: () => field(z.string().email(), DB_TYPE.TEXT),
  number: () => field(z.number(), DB_TYPE.INTEGER),
  boolean: () => field(z.boolean(), DB_TYPE.BOOLEAN),
  date: () => field(z.date(), DB_TYPE.DATE),
  timestamp: () => field(z.date(), DB_TYPE.TIMESTAMP),
  // ...
}
```

---

## Summary

| Before | After |
|--------|-------|
| 7 if/else branches | Lookup table |
| Magic `'text'`, `'integer'` | Constants |
| Silent fallback | Result with error |
| Repetitive code | Simple factory |
| Double call `fieldType()()` | Direct function |

---

## Key Rules

1. **Constants over magic strings**
2. **Lookup tables over if/else**
3. **Result instead of fallbacks**
4. **DRY: factorize repeated patterns**

---

*Document version: 1.0*
*Last updated: 2026-03-05*
