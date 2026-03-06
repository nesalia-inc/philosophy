# Naming Costs

Why prefixes and suffixes are expensive and should be avoided.

---

## The Problem

Every prefix and suffix in a name adds cognitive load:

```
❌ FindManyOptions         → findMany + Options
❌ CollectionOperations   → collection + Operations
❌ DatabaseAdapter        → database + Adapter
❌ UserService            → user + Service
❌ createUser             → create + user
```

The reader must mentally assemble the meaning from parts.

---

## The Cost

| Name | Mental Operations |
|------|-------------------|
| `CollectionOperations` | 2 parts to combine |
| `CreateUserOptions` | 3 parts to combine |
| `IDatabaseConnectionConfig` | 4+ parts = confusion |

Each suffix forces the reader to:
1. Parse the suffix
2. Parse the prefix
3. Combine them mentally
4. Understand the relationship

---

## The Rule

If a name needs explanation, it's too complex.

```
❌ UserService
✅ user

❌ FindManyOptions
✅ findMany

❌ DatabaseAdapter
✅ database
```

The type/function name should be self-explanatory from context.

---

## When Suffixes Are Acceptable

Only when they add critical information:

| Suffix | Acceptable When |
|--------|-----------------|
| `Error` | Returning error types (`NotFoundError`) |
| `Result` | When it's not obvious from context |
| `Config` | For configuration objects |

But prefer:
- `Result<User, NotFound>` over `UserResult`
- `database` over `databaseConfig` (the parameter name already says it's config)

---

## Legacy Patterns to Avoid

| Avoid | Prefer |
|-------|--------|
| `IUser` (interface) | `User` |
| `UserDTO` | `User` (or be specific: `UserInput`) |
| `UserService` | `user` |
| `createUser` | `user` (if it returns user) |
| `UserController` | `user` |
| `UserRepository` | `user` |

---

## The Test

If you need to explain the name, it's too complex:

```
❌ "It's FindManyOptions because it's the options for findMany"
✅ Just call it `findMany`
```

---

*Document version: 1.0*
*Last updated: 2026-03-05*
