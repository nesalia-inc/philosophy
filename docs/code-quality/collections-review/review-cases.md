# Collections Code Review

Analysis of the collections operations implementation.

---

## Case 1: `buildWhereClause` — Input/Output Problems

### Current Implementation

```typescript
const buildWhereClause = (
  tableColumns: Record<string, any>,
  where?: Record<string, unknown>
): any => {
  if (!where) return undefined

  const conditions: any[] = []

  for (const [key, value] of Object.entries(where)) {
    const column = tableColumns[key]
    if (!column) continue  // ← silently ignores invalid columns

    if (value === null || typeof value !== 'object') {
      conditions.push(eq(column, value))
    } else {
      const operator = value as WhereOperator<unknown>
      if ('eq' in operator) {
        conditions.push(eq(column, operator.eq))
      } else if ('neq' in operator) {
        conditions.push(not(eq(column, operator.neq)))
      } else if ('gt' in operator) {
        conditions.push(gt(column, operator.gt as number))
      }
      // ... 15 more operators written individually
    }
  }

  if (conditions.length === 0) return undefined
  if (conditions.length === 1) return conditions[0]
  return and(...conditions)
}
```

### Problems

| Issue | Impact | Severity |
|-------|--------|----------|
| `tableColumns: Record<string, any>` | No type safety, passes anything | High |
| `where?: Record<string, unknown>` | Opaque input structure | High |
| Returns `any` | Caller cannot predict output | High |
| Silent `continue` on invalid column | User thinks query works, it doesn't | Critical |
| Manual operator mapping | Adding operators = copy-paste errors | Medium |
| No validation | SQL injection possible | Critical |

### What It Should Be

```typescript
// Step 1: Define the structure
type ColumnSchema<T> = {
  type: 'text' | 'number' | 'boolean' | 'date'
  nullable: boolean
}

type TableSchema<T extends string> = Record<T, ColumnSchema<any>>

// Step 2: Type-safe operators
type Operator<T> =
  | { type: 'eq'; value: T }
  | { type: 'neq'; value: T }
  | { type: 'gt'; value: T }
  | { type: 'gte'; value: T }
  | { type: 'in'; value: T[] }
  | { type: 'contains'; value: string }
  | { type: 'isNull' }

type WhereCondition<T> = {
  [K in keyof T]?: Operator<T[K]>
}

// Step 3: Result-based function
type BuildError =
  | { type: 'unknown_column'; name: string }
  | { type: 'invalid_operator'; column: string; operator: string }
  | { type: 'type_mismatch'; column: string; expected: string; actual: string }

buildWhereClause ::
  <T extends string>(
    schema: TableSchema<T>,
    condition: WhereCondition<T>
  )
  => Result<SQLCondition, BuildError>
```

---

## Case 2: Naive Operator Mapping

### Current Implementation

```typescript
if ('eq' in operator) {
  conditions.push(eq(column, operator.eq))
} else if ('neq' in operator) {
  conditions.push(not(eq(column, operator.neq)))
} else if ('gt' in operator) {
  conditions.push(gt(column, operator.gt as number))
} else if ('gte' in operator) {
  conditions.push(gte(column, operator.gte as number))
} else if ('lt' in operator) {
  conditions.push(lt(column, operator.lt as number))
} else if ('lte' in operator) {
  conditions.push(lte(column, operator.lte as number))
} else if ('in' in operator) {
  conditions.push(inArray(column, operator.in))
} else if ('notIn' in operator) {
  conditions.push(not(inArray(column, operator.notIn)))
} else if ('contains' in operator) {
  conditions.push(like(column, `%${operator.contains}%`))
}
// ... 5 more
```

### Problems

- 15+ `else if` branches for a simple mapping
- Easy to make typos (`operator.eq` vs `operator.equal`)
- No central place for new operators
- Easy to forget to handle a case

### What It Should Be

```typescript
// Single source of truth for operators
const OPERATORS = {
  eq: (col, val) => eq(col, val),
  neq: (col, val) => not(eq(col, val)),
  gt: (col, val) => gt(col, val),
  gte: (col, val) => gte(col, val),
  lt: (col, val) => lt(col, val),
  lte: (col, val) => lte(col, val),
  in: (col, val) => inArray(col, val),
  notIn: (col, val) => not(inArray(col, val)),
  contains: (col, val) => like(col, `%${val}%`),
  startsWith: (col, val) => like(col, `${val}%`),
  endsWith: (col, val) => like(col, `%${val}`),
  isNull: (col, _val) => isNull(col),
  not: (col, val) => not(eq(col, val)),
} as const

type OperatorType = keyof typeof OPERATORS

// One function handles all
const applyOperator = <T>(
  column: Column,
  operator: Operator<T>
): Maybe<SQLCondition> => {
  if (!('type' in operator)) return Maybe.none()

  const builder = OPERATORS[operator.type as OperatorType]
  if (!builder) return Maybe.none()

  return Maybe.some(builder(column, operator.value))
}
```

---

## Case 3: Hooks as Side Effects

### Current Implementation

```typescript
const executeAfterCreateHooks = async (
  hooks: CollectionHooks | undefined,
  context: CreateHookContext
): Promise<void> => {
  if (!hooks?.afterCreate) return
  for (const hook of hooks.afterCreate) {
    await hook(context)  // ← arbitrary side effect, hidden
  }
}

// Called inside create()
await executeAfterCreateHooks(hooks, {
  collection: _slug,
  operation: 'create',
  data: firstData,
  result: returnValue,
  db
})
```

### Problems

- No way to know what hooks actually do
- Hooks can fail silently
- No rollback if hook fails mid-way
- Cannot test or mock easily
- Intermingles business logic with infrastructure

### What It Should Be

```typescript
// Hooks as data transformers (no side effects)
type QueryTransformer<T> = (query: Query<T>) => Query<T>

const withLogging = <T>(logger: Logger): QueryTransformer<T> =>
  (query) => query.mapAfter(result => {
    logger.log(`Query returned ${result.length} rows`)
    return result
  })

const withCache = <T>(cache: Cache): QueryTransformer<T> =>
  (query) => query
    .mapBefore(q => cache.get(q.key) || q)
    .mapAfter(result => {
      cache.set(result)
      return result
    })

// Composition
const query = Query.create(table)
  .where(conditions)
  .transform(withLogging(logger))
  .transform(withCache(cache))
  .execute()
```

---

## Case 4: Bulk Operations N+1

### Current Implementation

```typescript
deleteMany: async (options: DeleteManyOptions): Promise<number> => {
  const whereClause = buildWhereClause(tableColumns, options.where)
  if (!whereClause) return 0

  // ❌ N+1: SELECT for each row
  const previousResults = await db.select().from(_table).where(whereClause)

  for (const previousData of previousResults) {
    await executeBeforeDeleteHooks(hooks, {  // ← runs for EACH row
      collection: _slug,
      previousData: previousData as Record<string, unknown>,
      db
    })
  }

  const result = await db.delete(_table).where(whereClause)
  return result.rowCount ?? 0
}
```

### Problems

- If you delete 10,000 rows, you do 10,000 SELECT queries first
- O(n) performance instead of O(1)
- Hooks run sequentially, no batching

### What It Should Be

```typescript
// Batch approach
deleteMany: async (options: DeleteOptions): Promise<number> => {
  const whereClause = buildWhereClause(options.where)

  // Single query with WHERE
  const result = await db
    .delete(table)
    .where(whereClause)
    .returning('*')  // Get all deleted in one query

  // Single hook call with all data
  await hooks.afterDelete({
    deleted: result,  // All deleted rows at once
    count: result.length
  })

  return result.length
}
```

---

## Case 5: Missing Error Handling

### Current Implementation

```typescript
const tableColumns = _table as Record<string, any>
const db = _db as any

// If _table is invalid, no error - just returns empty
return {
  findMany: async (): Promise<T[]> => [],
  findUnique: async (): Promise<T | undefined> => undefined,
}
```

### Problems

- Invalid table passes silently
- Returns empty arrays, undefined — caller thinks "no results"
- No distinction between "error" and "empty"

### What It Should Be

```typescript
// Result-based initialization
type InitError =
  | { type: 'table_not_found'; name: string }
  | { type: 'invalid_schema'; reason: string }

const createCollection = (config: CollectionConfig):
  Result<CollectionOperations, InitError> => {
  if (!config.table) {
    return Result.err({ type: 'table_not_found', name: config.name })
  }

  const schema = validateSchema(config.table)
  if (schema.isErr()) {
    return Result.err({ type: 'invalid_schema', reason: schema.error })
  }

  return Result.ok(buildOperations(schema.value))
}
```

---

## Case 6: Duplicate Hook Execution

### Current Implementation

```typescript
// executeBeforeOperationHooks + executeBeforeCreateHooks
// Both run, with overlapping concerns

await executeBeforeOperationHooks(hooks, {
  collection: _slug,
  operation: 'create',
  data: firstData,
  where: undefined
})

await executeBeforeCreateHooks(hooks, {
  collection: _slug,
  operation: 'create',
  data: firstData,
  db
})
```

### Problems

- Duplicate execution points
- Overlapping context (operation vs specific)
- No clear contract between hooks

### What It Should Be

```typescript
// Single hook point with full context
type HookContext<T> = {
  operation: 'read' | 'create' | 'update' | 'delete'
  data?: T
  previousData?: T
  result?: T
}

hooks.run('before', context)  // Runs all 'before' hooks once
hooks.run('after', context)   // Runs all 'after' hooks once
```

---

## Summary Checklist

| Check | Question |
|-------|----------|
| Types | Does every function return a typed structure (not `any`)? |
| Errors | Does every function return `Result` or `Maybe`? |
| Inputs | Are all inputs validated at the boundary? |
| Mapping | Is there duplicate `if/else` that should be a lookup table? |
| Side effects | Are effects explicit, not hidden in hooks? |
| Bulk ops | Do loops query the DB inside? |
| Null | Does `undefined` or `null` mean "not found" or "error"? |

---

*Document version: 1.0*
*Last updated: 2026-03-05*
