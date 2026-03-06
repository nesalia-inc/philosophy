# DefineConfig Analysis

Breaking down a 200-line configuration function into focused responsibilities.

---

## The Current Implementation

```typescript
export const defineConfig = <T extends Collection[]>(
  options: ConfigOptions<T>
): DefineConfigReturn<T> => {
  let pool: PoolType | null = null
  let dbInstance: ReturnType<typeof drizzle> | null = null
  let schema: Record<string, unknown> = {}

  if (options.database.type === 'postgres') {
    pool = new Pool({ connectionString: options.database.config.url })
    schema = buildSchema(options.collections as Collection[])
    dbInstance = drizzle(pool, { schema })
  }

  const dbWrapper = new DbWrapper()
  if (options.validation) {
    dbWrapper.setValidationOptions(options.validation)
  }

  if (dbInstance) {
    for (const coll of options.collections) {
      const table = getTableFromSchema(schema, coll.slug)
      if (table) {
        dbWrapper.register(coll.slug, coll, dbInstance, table)
      }
    }
  }

  const collectionsMap: Record<string, Collection> = {}
  const collectionNames: string[] = []

  for (const coll of options.collections) {
    collectionsMap[coll.slug] = { ...metadata }
    collectionNames.push(coll.slug)
  }

  // ... plugins processing

  const db: Record<string, unknown> = {}
  for (const slug of collectionNames) {
    const wrapper = dbWrapper.get(slug)
    if (wrapper) {
      db[slug] = {
        find: (opts) => wrapper.find(opts),
        findById: (id) => wrapper.findById(id),
        // ... 8 more methods
      }
    }
  }

  return { collections, db, $meta }
}
```

---

## What's Wrong

### 1. Single Function Does 8 Things

| Line Range | Responsibility |
|-----------|---------------|
| ~10-20 | Initialize pool |
| ~21-23 | Build schema |
| ~24-25 | Create Drizzle instance |
| ~27-29 | Configure wrapper |
| ~31-36 | Register collections |
| ~39-46 | Build collections map |
| ~48-65 | Process plugins |
| ~68-80 | Create db object |

### 2. Implicit Dependencies

- `DbWrapper` created inside
- `Pool` created inside
- `schema` built inside
- Nothing can be tested independently

### 3. DbWrapper God Object

```typescript
dbWrapper.register(coll.slug, coll, dbInstance, table)
dbWrapper.get(slug)
dbWrapper.setValidationOptions(...)
```

Everything goes through one object.

### 4. Types With `any`

```typescript
find: (options?: { where?: Record<string, unknown> }) => Promise<OperationResult<any[]>>
```

The caller has no idea what `any` is.

---

## What It Should Be

### Step 1: Separate Each Responsibility

First, define clear types for each concept:

```typescript
// 1. Collection Fields = what user defines
type CollectionFields = {
  [key: string]: FieldDefinition
}

// 2. Drizzle Table = DB representation
type DrizzleTable = PgTable | MySqlTable | SqliteTable

// 3. Compiled = tables + operators ready for queries
type CompiledSchema = {
  tables: {
    [slug: string]: {
      fields: CollectionFields
      table: DrizzleTable
      operators: OperatorMap
    }
  }
}
```

```typescript
// 1. Database connection only
const connect = (
  config: DatabaseConfig
): Result<Database, ConnectionError> => {
  switch (config.type) {
    case 'postgres':
      return Result.ok(new Pool({ connectionString: config.url }))
    case 'sqlite':
      return Result.ok(betterSqlite3(config.path))
    default:
      return Result.err({ type: 'unsupported', db: config.type })
  }
}

// 2. Convert fields to Drizzle tables
const toDrizzleTable = (
  fields: CollectionFields
): DrizzleTable =>
  // Maps user field definitions to Drizzle table
  {}

const buildDrizzleTables = (
  collections: Collection[]
): DrizzleTable[] =>
  collections.map(coll => toDrizzleTable(coll.fields))

// 3. Build runtime schema (fields + drizzle tables + operators)
const buildRuntimeSchema = (
  collections: Collection[],
  drizzleTables: DrizzleTable[]
): RuntimeSchema => {
  const tables: RuntimeSchema['tables'] = {}

  for (const [i, coll] of collections.entries()) {
    tables[coll.slug] = {
      fields: coll.fields,
      table: drizzleTables[i],
      operators: buildOperators(coll.fields)
    }
  }

  return { tables }
}

// 4. Create operations (CRUD functions) for a collection
const createCollectionOperationsForTable = (
  connection: Database,
  runtimeTable: RuntimeTable
): CollectionOperations =>
  // Returns { find, create, update, delete, ... }
  {}

// 4. Plugin processing
const applyPlugins = (
  base: Collection[],
  plugins: Plugin[]
): Collection[] =>
  plugins.reduce((acc, plugin) => {
    if (plugin.collections) {
      return [...acc, ...Object.values(plugin.collections)]
    }
    return acc
  }, base)

// 5. Build db object
const buildDbObject = (
  collections: Collection[],
  schema: Schema,
  db: Database,
  options: ConfigOptions
): DbObject => {
  const result: DbObject = {}

  for (const coll of collections) {
    const ops = registerCollection(db, schema, coll)
    result[coll.slug] = {
      find: ops.find,
      findById: ops.findById,
      // ...
    }
  }

  return result
}
```

### Step 2: Compose in defineConfig

```typescript
const defineConfig = <T extends Collection[]>(
  options: ConfigOptions<T>
): DefineConfigReturn<T> =>
  pipe(
    // Step 1: Connect to DB (raw connection)
    connect(options.database),

    // Step 2: Convert fields to Drizzle tables
    (connection) => ({
      connection,
      drizzleTables: buildDrizzleTables(options.collections)
    }),

    // Step 3: Apply plugins (adds more collections)
    ({ connection, drizzleTables }) => ({
      connection,
      allCollections: applyPlugins(options.collections, options.plugins ?? []),
      drizzleTables
    }),

    // Step 4: Build runtime schema (fields + tables + operators)
    ({ connection, allCollections, drizzleTables }) => ({
      connection,
      runtime: buildRuntimeSchema(allCollections, drizzleTables)
    }),

    // Step 5: Create CRUD operations for each table
    ({ connection, runtime }) => ({
      operations: runtime.tables.map(t =>
        createCollectionOperationsForTable(connection, t)
      )
    }),

    // Step 6: Build final object
    ({ operations, runtime }) => ({
      collections: toMetadata(runtime.tables),
      db: buildDbObject(operations),
      $meta: buildMeta(runtime.tables)
    })
  )
```

### Key Concepts Explained

| Concept | Represents | Function |
|---------|-----------|----------|
| `connection` | Raw DB pool (Pool, better-sqlite3) | `connect()` |
| `drizzleTables` | Drizzle table definitions | `buildDrizzleTables()` |
| `runtime` | Tables + operators + fields ready to use | `buildRuntimeSchema()` |
| `operations` | CRUD functions for each table | `createCollectionOperationsForTable()` |
| `metadata` | Slug, name, fields for docs | `toMetadata()` |

### Step 3: Types That Mean Something

```typescript
// Instead of
type DbWithCollections<T> = {
  [K]: { find: (opts) => Promise<OperationResult<any[]>> }
}

// Use
type DbWithCollections<T extends Collection[]> = {
  [K in T[number] as K['slug']]: {
    find: (opts: FindOptions<K['fields']>) => Promise<Result<K['fields'][], QueryError>>
    findById: (id: K['id']) => Promise<Result<Option<K['fields']>, QueryError>>
    // ...
  }
}
```

---

## Summary

| Before | After |
|--------|-------|
| 1 function, 200 lines | 6 functions, ~30 lines each |
| Everything coupled | Each function testable |
| Implicit dependencies | Explicit inputs/outputs |
| Types with `any` | Fully typed |
| DbWrapper god object | Simple functions |
| Impossible to debug | Clear data flow |

---

## Key Principle

> A configuration function should **wire** things together, not **do** things.

The `defineConfig` should only:
1. Take options
2. Call other functions
3. Return result

Not:
- Create pools
- Build schemas
- Register collections

---

*Document version: 1.0*
*Last updated: 2026-03-05*
