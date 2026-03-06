# CollectionDbWrapper Analysis

Breaking down a 200-line wrapper class into pure functions.

---

## Current Implementation

```typescript
class CollectionDbWrapper<T> {
  constructor(collection, slug, db, table, hooks, validation) {
    this.operations = createCollectionOperations(...)
  }

  async find(options) {
    const data = await this.operations.findMany(options)
    const cacheKey = generateCacheKey(this.slug, 'find', options?.where)
    return { data, meta: { cacheKeys: [cacheKey] } }
  }

  async findById(id) {
    const data = await this.operations.findUnique({ where: { id } })
    const cacheKey = generateCacheKey(this.slug, 'findById', { id })
    return { data, meta: { cacheKeys: [cacheKey] } }
  }

  async findFirst(options) {
    const data = await this.operations.findFirst(options)
    const cacheKey = generateCacheKey(this.slug, 'findFirst', options.where)
    return { data, meta: { cacheKeys: [cacheKey] } }
  }

  async count(options) { /* same pattern */ }
  async exists(options) { /* same pattern */ }
  async create(options) { /* invalidateKeys */ }
  async createMany(options) { /* invalidateKeys */ }
  async update(options) { /* invalidateKeys */ }
  async updateMany(options) { /* invalidateKeys */ }
  async delete(options) { /* invalidateKeys */ }
  async deleteMany(options) { /* invalidateKeys */ }
}
```

---

## What's Wrong

### 1. Wrapper Adds Nothing

The wrapper only wraps results with cache keys. Every method is:
1. Call `this.operations.X`
2. Generate cache key or invalidate keys
3. Return wrapped result

### 2. Massive Duplication

| Method | Pattern |
|--------|---------|
| `find` | `operations.find` + cacheKey |
| `findById` | `operations.findUnique` + cacheKey |
| `create` | `operations.create` + invalidateKeys |
| `update` | `operations.update` + invalidateKeys |
| `delete` | `operations.delete` + invalidateKeys |

11 methods, same pattern.

### 3. Class = Untestable

```typescript
class CollectionDbWrapper {
  constructor(..., hooks?, validation?) {
    this.operations = createCollectionOperations(...)
  }
}
```

Dependencies hidden inside. Can't test without instantiating everything.

---

## What It Should Be

### Step 1: Higher-Order Metadata Functions

```typescript
// Higher-order: take a metadata generator, return a wrapper function
const withMetadata = <T>(
  generate: (slug: string) => Record<string, unknown>
) => (slug: string) =>
  (result: T): OperationResult<T> => ({
    data: result,
    meta: generate(slug)
  })

// Define specific generators
const generateCacheKeys = (slug: string) => ({
  cacheKeys: [generateCacheKey(slug)]
})

const generateInvalidateKeysForSlug = (slug: string) => ({
  invalidateKeys: generateInvalidateKeys(slug)
})

// Create specific wrappers
const withCache = withMetadata(generateCacheKeys)
const withInvalidate = withMetadata(generateInvalidateKeysForSlug)
```

### Step 2: Compose Operations

```typescript
// apply rule: createX → X
const collectionDb = (
  operations: CollectionOperations,
  slug: string
): CollectionDb => ({
  find: (options) => operations.findMany(options).map(withCache(slug)),
  findById: (id) => operations.findUnique({ where: { id } }).map(withCache(slug)),
  create: (data) => operations.create(data).map(withInvalidate(slug)),
  update: (data) => operations.update(data).map(withInvalidate(slug)),
  delete: (data) => operations.delete(data).map(withInvalidate(slug)),
})
```

### Step 3: The Result IS the Database

```typescript
// The result IS the database, not a "wrapper"
// Name it accordingly
const database = (collections: Collection[]): Database =>
  collections.reduce((accumulator, collection) => {
    const operations = collectionOperations(collection)
    const collectionDb = collectionDb(operations, collection.slug)

    // Expose directly as properties, not .get()
    return { ...accumulator, [collection.slug]: collectionDb }
  }, {} as Database)

// Usage
const db = database(collections)

// Direct access by property
db.users.find({ where: { status: 'active' } })
db.posts.create({ data: { title: 'Hello' } })
```

---

## What We Gained

| Before | After |
|--------|-------|
| Class with 200 lines | Functions with ~50 lines |
| 11 duplicate methods | 2 utility functions |
| Untestable | Each function testable |
| Hidden dependencies | Explicit composition |
| Mutation in constructor | Pure functions |
| `createDbWrapper` | `database` (the result IS the database) |
| `db.get('users')` | `db.users` (direct property access) |

## Naming Rule

If `createX` returns `X`, just name it `X`.

```
❌ const createUser = (data) => User
❌ const createDatabase = (config) => Database

✅ const user = (data) => User
✅ const database = (config) => Database
```

The function returns the thing, not a factory. Name it after what it returns.

---

## Key Insight

> If a class/wrapper only wraps results with metadata, it should be a function, not a class.

The wrapper adds:
- Cache keys for reads
- Invalidate keys for writes

These are metadata transformations. Transform, don't wrap.

---

*Document version: 1.0*
*Last updated: 2026-03-05*
