# God Function Refactoring

How to break down a 1500-line function into small, focused, composable units.

---

## The Problem: `createCollectionOperations`

This function has:
- **7 parameters** (most as `any`)
- **1500+ lines** of code
- **11 methods** in a returned object
- **Massive duplication** — each method repeats hooks logic
- **Zero abstraction** — everything in one place
- **Impossible to test** — too many dependencies intertwined

```typescript
export const createCollectionOperations = (
  _collection: Collection,
  _slug: string,
  _db: any,
  _table: any,
  _hooks?: CollectionHooks,
  _validationOptions?: ValidationOptions
): CollectionOperations => {
  // 1500 lines...
}
```

---

## Step 1: Identify the Abstractions

Instead of one function returning 11 methods, we have:

```
Collection = type (data)
    ↓
createCollection = constructor (returns Result)
    ↓
Operations = separate functions
    ↓
Helpers = shared logic
    ↓
Composition = wiring
```

---

## Step 2: Define the Types

### The Collection Type

```typescript
// Just data, no methods
type Collection<Row> = {
  config: ValidatedConfig
  schema: Schema
  db: Database
  hooks: CollectionHooks
  cache?: Cache
}
```

### The Configuration

```typescript
type CollectionConfig = {
  name: string
  schema: RawSchema
  hooks?: CollectionHooks
  cache?: Cache
}

type ConfigError =
  | { type: 'missing_name' }
  | { type: 'invalid_schema'; reason: string }
  | { type: 'missing_db' }
```

---

## Step 3: Constructor Returns Result

```typescript
// No constructor, just a function
const createCollection = (
  config: CollectionConfig
): Result<Collection<Row>, ConfigError> => {
  // Validate inputs first
  if (!config.name) {
    return Result.err({ type: 'missing_name' })
  }

  const schema = validateSchema(config.schema)
  if (schema.isErr()) {
    return Result.err({
      type: 'invalid_schema',
      reason: schema.error.message
    })
  }

  // Return the type, not an object with methods
  return Result.ok({
    config: {
      name: config.name,
      hooks: config.hooks ?? defaultHooks,
    },
    schema: schema.value,
    db: config.db,
    hooks: config.hooks ?? defaultHooks,
    cache: config.cache,
  })
}
```

---

## Step 4: Separate Operations

Each operation is a standalone function:

```typescript
// Query: returns rows
const find = <Row>(
  collection: Collection<Row>,
  options: FindOptions<Row>
): Result<Row[], QueryError> => {
  const query = buildQuery(collection.schema, options)
  if (query.isErr()) return Result.err(query.error)

  return collection.db.query(query.value)
}

// Mutation: returns created/updated row
const create = <Row>(
  collection: Collection<Row>,
  data: CreateData<Row>
): Result<Row, MutationError> => {
  const validated = validateData(collection.schema, data)
  if (validated.isErr()) return Result.err(validated.error)

  return collection.db.insert(validated.value)
}

// Mutation: returns deleted row
const remove = <Row>(
  collection: Collection<Row>,
  where: WhereCondition<Row>
): Result<Row, MutationError> => {
  const query = buildQuery(collection.schema, { where })
  if (query.isErr()) return Result.err(query.error)

  return collection.db.delete(query.value)
}
```

---

## Step 5: Shared Helpers

### Query Builder

```typescript
// Single place for building queries
const buildQuery = <Row>(
  schema: Schema<Row>,
  options: FindOptions<Row>
): Result<Query, QueryError> => {
  // where, orderBy, limit, offset — one place
  return validateOptions(schema, options)
    .andThen(normalizeWhere)
    .andThen(normalizeOrderBy)
    .andThen(validateLimitOffset)
    .map(buildDrizzleQuery)
}
```

### Hooks Wrapper

```typescript
// Single place for hooks
const withHooks = <T>(
  operation: Operation,
  hooks: CollectionHooks,
  fn: () => Result<T, Error>
): Result<T, Error> => {
  // Run before hooks
  const beforeResult = hooks.before?.(operation)
  if (beforeResult?.isErr()) return Result.err(beforeResult.error)

  // Run the operation
  const result = fn()

  // Run after hooks
  hooks.after?.(operation, result)

  return result
}
```

---

## Step 6: Composition

Wire things together with pure functions:

```typescript
// Composable find with hooks and cache
const findWithHooks = <Row>(
  collection: Collection<Row>,
  options: FindOptions<Row>
): Result<Row[], Error> =>
  pipe(
    // Step 1: Build query
    buildQuery(collection.schema, options),
    // Step 2: Run with hooks
    Result.andThen(q => withHooks(
      { type: 'read', query: q },
      collection.hooks,
      () => collection.db.query(q)
    )),
    // Step 3: Cache result
    Result.map(data => {
      collection.cache?.set(options, data)
      return data
    })
  )
```

---

## What We Gained

| Before | After |
|--------|-------|
| 7 parameters | 1 config object |
| `any` everywhere | Fully typed |
| 1500 lines | ~50 lines per operation |
| Duplicate hooks logic | Single `withHooks` helper |
| Returns empty on error | Returns `Result` with errors |
| Impossible to test | Each function testable |
| One god function | Many small functions |

---

## Summary

- **Type = data**, not object with methods
- **Constructor = function** returning `Result`
- **Operations = separate functions** doing one thing
- **Helpers = shared logic** in one place
- **Composition = wiring** with pure functions
- **No classes**, no `new`, no `this`

---

*Document version: 1.0*
*Last updated: 2026-03-05*
