# Code Rules

General rules learned from codebase analysis. Apply to all projects.

---

## 1. Types Are Not `any`

Never use `any`. Use proper types or `unknown`.

```
❌ Record<string, any>
❌ (opts: any) => any
❌ data as any

✅ Record<string, T>
✅ <T>(opts: Options<T>) => Result<T, Error>
```

---

## 2. Functions Return Types, Not `void`

If a function does work, it returns something meaningful.

```
❌ const create = async (data) => { await db.insert(data) }

✅ const create = async (data): Promise<Result<User, InsertError>> =>
    validate(data).andThen(d => db.insert(d))
```

---

## 3. Errors Are Not Thrown, They're Returned

Use `Result` or `Option` for error handling.

```
❌ if (!user) throw new Error('not found')
❌ try { ... } catch (e) { ... }

✅ if (!user) return Result.err({ type: 'not_found' })
✅ return user ? Result.ok(user) : Result.err({ type: 'not_found' })
```

---

## 4. One Function, One Responsibility

A function should do one thing. If it does multiple things, split it.

```
❌ const init = () => { connectDB(); buildSchema(); registerRoutes(); startServer(); }

✅ const connect = () => DB
✅ const buildSchema = (db) => Schema
✅ const registerRoutes = (schema) => Router
✅ const startServer = (router) => Server
✅ const init = () => pipe(connect(), buildSchema(), registerRoutes(), startServer())
```

---

## 5. Naming Is Explicit

Function names describe what they do. No ambiguity.

```
❌ compile()       // compile what?
❌ process()       // process what?
❌ register()      // register where?

✅ toDrizzleTable(fields)           // converts fields to Drizzle table
✅ buildRuntimeSchema(collections)  // builds runtime schema
✅ createCollectionOperations(db)  // creates CRUD operations
```

---

## 6. Configuration Returns a Type

A config function returns a typed object, not magic methods.

```
❌ const db = { users: { find: () => {}, create: () => {} } }

✅ type Db = { users: Collection<User> }
✅ const createDb = (config): Db => { ... }
```

---

## 7. No God Objects

Single objects that do everything are unreadable. Split responsibilities.

```
❌ class DbWrapper { register(); get(); setOptions(); connect(); query(); }

✅ const connect = (config) => Connection
✅ const register = (connection, table) => Operations
✅ const toDrizzle = (fields) => Table
```

---

## 8. Data Flow Is Clear

Follow data through the system step by step.

```
❌ function config(options) {
    let db = null
    let schema = {}
    if (x) { db = new Pool(...) }
    // ...100 lines later...
    return { db, schema, ... }
  }

✅ pipe(
    connect(options.database),
    buildDrizzleTables,
    buildRuntimeSchema,
    createOperations,
    buildFinal
  )
```

---

## 9. Inputs Are Validated

Validate at boundaries, return errors early.

```
❌ const find = (table, where) => db.select().where(where)

✅ const find = (table, where) =>
    validateWhere(table.schema, where)
      .andThen(w => db.select().where(w))
```

---

## 10. Types Describe Data, Not Objects

Types represent data structures, not classes with methods.

```
❌ class User {
    find() {}
    create() {}
    update() {}
  }

✅ type User = { id: string, name: string, email: string }
✅ const findUser = (id): Result<User, NotFound> => db.query(...)
```

---

## 11. Composition Over Configuration

Wire things together with functions, not magic.

```
❌ @Injectable()
class UserService {
  constructor(private db: Database) {}
}

✅ const createUserService = (db: Database): UserService =>
  ({
    find: (id) => db.find(id),
    create: (data) => db.create(data),
  })
```

---

## 12. No Silent Failures

If something goes wrong, the caller must know.

```
❌ if (!column) continue  // silently ignores
❌ if (!db) return { find: () => [] }  // fake success

✅ if (!column) return Result.err({ type: 'unknown_column', name })
```

---

## Naming Rule

If a function returns `X`, name it `X`, not `createX`.

```
❌ const createUser = (data) => User
❌ const createDatabase = (config) => Database

✅ const user = (data) => User
✅ const database = (config) => Database
```

---

## Summary

| Rule | Principle |
|------|-----------|
| 1 | No `any` |
| 2 | Return types |
| 3 | Result for errors |
| 4 | One responsibility |
| 5 | Explicit naming |
| 6 | Typed config |
| 7 | No god objects |
| 8 | Clear data flow |
| 9 | Validate inputs |
| 10 | Types as data |
| 11 | Composition |
| 12 | No silent failures |
| 13 | `createX` → `X` if returns X |
| 14 | Higher-order functions over duplicated patterns |
| 15 | Full names: `accumulator`, not `acc` |
| 16 | No suffixes: `Options`, `Service`, `Controller` — just name it |
| 17 | Each suffix costs mental effort to assemble |

---

*Document version: 1.0*
*Last updated: 2026-03-05*
