# API Local Architecture

A high-level ORM approach that unifies data access across all platforms while maintaining explicitness and type safety.

---

## The Problem: Fragmented Data Access

```
CURRENT STATE (fragmented):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Mobile          Desktop          Web
  │               │               │
  ▼               ▼               ▼
API A          API B           Direct DB
  │               │               │
  ▼               ▼               ▼
Remote API     Remote API      Postgres
                  │               │
                  ▼               ▼
               Database       Database

PROBLEMS:
* Different APIs for each platform
* No shared types or contracts
* Inconsistent error handling
* Duplicated business logic
```

---

## The Solution: Unified API Local

```
PROPOSED STATE (unified):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Mobile          Desktop          Web
  │               │               │
  ▼               ▼               ▼
  └───────────────┼───────────────┘
                  │
                  ▼
          ┌───────────────┐
          │   API Local   │  ← High-level ORM
          │   (Shared)    │
          └───────┬───────┘
                  │
         ┌────────┴────────┐
         │                 │
         ▼                 ▼
   ┌───────────┐    ┌───────────┐
   │  Remote   │    │  Direct   │
   │  Access   │    │  Access   │
   └─────┬─────┘    └─────┬─────┘
         │                │
         ▼                ▼
   ┌───────────┐    ┌───────────┐
   │Remote API │    │ Database  │
   └─────┬─────┘    └─────┬─────┘
         │                │
         └────────┬───────┘
                  │
                  ▼
            ┌───────────┐
            │ Database  │
            └───────────┘
```

---

## What is API Local?

### Definition

**API Local** is a high-level ORM that:

1. Provides a **unified interface** for all platforms
2. Handles both **remote** and **direct** database access
3. Enforces **explicit typing** with Result/Option
4. Manages **real-time cache revalidation**
5. Contains **all business logic** in one place

### Core Principles

| Principle | Description |
|-----------|-------------|
| **Single Source of Truth** | All business logic in one place |
| **Explicit Errors** | Result<T, E> for all fallible operations |
| **Explicit Absence** | Option<T> for all nullable values |
| **Platform Agnostic** | Same API for mobile, desktop, web |
| **Cache Awareness** | Integrated with TanStack Query |

---

## Architecture Layers

### Layer 1: Client Interface

```
CLIENTS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   Mobile    │  │   Desktop   │  │     Web     │
│  (React     │  │  (Electron, │  │  (Browser)  │
│   Native)   │  │   Tauri)    │  │             │
└──────┬──────┘  └──────┬──────┘  └──────┬──────┘
       │                │                │
       └────────────────┼────────────────┘
                        │
                        ▼
              ┌─────────────────┐
              │   TanStack      │  ← Cache + Revalidation
              │   Query         │
              └────────┬────────┘
                       │
                       ▼
              ┌─────────────────┐
              │    API Local    │  ← High-level ORM
              │    Client       │
              └────────┬────────┘
```

### Layer 2: API Local (High-Level ORM)

```
API LOCAL STRUCTURE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                      API Local                              │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─────────────────┐    ┌─────────────────┐              │
│  │   Query Builder │    │  Mutation       │              │
│  │                 │    │  Builder        │              │
│  │  .select()     │    │  .create()      │              │
│  │  .where()      │    │  .update()      │              │
│  │  .join()       │    │  .delete()      │              │
│  │  .orderBy()    │    │  .transaction() │              │
│  └────────┬────────┘    └────────┬────────┘              │
│           │                      │                        │
│           └──────────┬───────────┘                        │
│                      ▼                                    │
│  ┌─────────────────────────────────────────────────────┐  │
│  │              Result<T, E>                           │  │
│  │                                                     │  │
│  │  All operations return Result, never throw        │  │
│  │  All nullable values are Option<T>                │  │
│  │                                                     │  │
│  └─────────────────────────────────────────────────────┘  │
│                      │                                    │
│           ┌──────────┴──────────┐                        │
│           ▼                      ▼                        │
│  ┌─────────────────┐    ┌─────────────────┐              │
│  │  Remote        │    │  Direct         │              │
│  │  Transport     │    │  Transport      │              │
│  │                │    │                 │              │
│  │  → REST/GraphQL│    │  → Database     │              │
│  │  → gRPC        │    │    Driver       │              │
│  └────────┬────────┘    └────────┬────────┘              │
└───────────┼──────────────────────┼───────────────────────┘
            │                      │
            ▼                      ▼
    ┌───────────────┐      ┌───────────────┐
    │   Remote API  │      │   Database    │
    └───────────────┘      └───────────────┘
```

### Layer 3: Transport Abstraction

```
TRANSPORT LAYER
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

                    ┌─────────────────┐
                    │   API Local     │
                    │   (Same API)    │
                    └────────┬────────┘
                             │
            ┌────────────────┼────────────────┐
            │                │                │
            ▼                ▼                ▼
    ┌───────────────┐ ┌───────────────┐ ┌───────────────┐
    │    Mobile     │ │    Desktop    │ │      Web      │
    │               │ │               │ │               │
    │  Transport:  │ │  Transport:   │ │  Transport:   │
    │  HTTP/REST   │ │  HTTP/REST    │ │  Direct DB    │
    │              │ │  or IPC       │ │  (Postgres)  │
    └──────┬───────┘ └───────┬───────┘ └───────┬───────┘
           │                 │                 │
           └─────────────────┼─────────────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │    Database     │
                    │   (PostgreSQL)  │
                    └─────────────────┘

KEY INSIGHT: Client code is IDENTICAL regardless of transport.
             The ORM handles the abstraction.
```

---

## Explicitness: Result and Option

### Why Result<T, E>?

```
PROBLEM: Silent failures
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

// BAD: Silent failure - who knows what happened?
function getUser(id: string): User {
  const user = db.query('SELECT * FROM users WHERE id = ?', id);
  return user; // What if not found? What if DB down?
}

// GOOD: Explicit result
function getUser(id: string): Result<User, UserNotFoundError> {
  const user = db.query('SELECT * FROM users WHERE id = ?', id);
  if (!user) {
    return Err(new UserNotFoundError(id));
  }
  return Ok(user);
}

// GOOD: Explicit result with database error
function getUser(id: string): Result<User, FetchError> {
  try {
    const user = db.query('SELECT * FROM users WHERE id = ?', id);
    if (!user) return Err(new UserNotFoundError(id));
    return Ok(user);
  } catch (e) {
    return Err(new DatabaseError(e));
  }
}
```

### Why Option<T>?

```
PROBLEM: null/undefined ambiguity
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

// BAD: Is "null" meaning "not found" or "explicitly empty"?
const user = getUser(id);
if (user === null) {
  // What does this mean?
}

// GOOD: Option makes intent clear
function getUser(id: string): Result<User, Error> {
  // ... returns Ok(user) or Err(error)
}

function getUserAvatar(userId: string): Option<Avatar> {
  // Returns Some(avatar) or None
  // None = user exists but has no avatar
  // Err = user not found (different from "no avatar")
}
```

### The Type System Rules

```
EXPLICITNESS RULES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. NEVER return null/undefined
   ─────────────────────────────
   Use Option<T> instead of T | null

2. NEVER throw exceptions
   ─────────────────────────────
   Use Result<T, E> instead of try/catch

3. ALWAYS propagate errors
   ─────────────────────────────
   Don't swallow errors in catch blocks

4. ALWAYS handle None
   ─────────────────────────────
   Force explicit handling of missing values

5. NEVER use any
   ─────────────────────────────
   Everything must be typed
```

---

## Real-Time Revalidation

### The Problem with Traditional Real-Time

```
TRADITIONAL REAL-TIME (complex):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

WebSocket Server    │  Complex setup
        │          │
        ▼          │
   ┌─────────┐     │  Expensive
   │Socket   │     │
   │Server   │     │  Hard to scale
   └────┬────┘     │
        │          │
        ▼          │
   ┌─────────┐     │
   │  Redis  │     │  Lots of infrastructure
   │Pub/Sub  │     │
   └─────────┘     │

COST: High | Complexity: High | Scale: Hard
```

### The Modern Solution: Cache Revalidation

```
MODERN REAL-TIME (simple):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

              Cache Revalidation
              ┌────────────────┐
              │                │
              ▼                ▼
┌─────────┐   ┌─────────┐   ┌─────────┐
│ Query   │◄─►│ TanStack│◄─►│  API    │
│ Key     │   │ Query   │   │ Local   │
└─────────┘   └─────────┘   └─────────┘
                     │
                     │ Invalidation
                     ▼
              ┌─────────┐
              │ Triggers│
              │         │
              │ * Time  │
              │ * Focus │
              │ * Event │
              │ * Manual│
              └─────────┘

COST: Low | Complexity: Low | Scale: Easy
```

### Revalidation Key System

```
REVALIDATION KEYS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Every cache entry has a revalidation key:

┌─────────────────────────────────────────────────────────────┐
│ Query Key          │ Revalidation Key                       │
├────────────────────┼────────────────────────────────────────┤
│ ['users']          │ 'users'                                │
│ ['user', id]       │ 'user:{id}'                            │
│ ['posts', 'user', │ 'posts:user:{userId}'                  │
│   userId]          │                                        │
│ ['notifications']  │ 'notifications:{userId}'               │
└────────────────────┴────────────────────────────────────────┘

When data changes:
──────────────────

1. Mutation happens (create/update/delete)
2. API Local computes affected keys
3. Invalidate matching cache entries
4. TanStack Query refetches automatically

Example:
───────

// User creates a post
await api.createPost({ title: 'Hello' });

// Invalidation triggered:
// - 'posts:user:{userId}' → refetch user's posts
// - 'feed' → refetch main feed
// - 'stats' → refetch user stats
```

---

## Implementation Pattern

### Client Usage (Same for All Platforms)

```typescript
// Define a query with revalidation keys
const userQuery = api.query({
  key: ['user', userId],
  revalidationKeys: ['user', userId, 'stats'],
  query: () => db.user.findUnique({ where: { id: userId } }),
});

// In React:
const { data: user, isLoading, error } = useQuery(userQuery);

// Mutations automatically invalidate revalidation keys
const createPost = api.mutation({
  mutation: (data) => db.post.create({ data }),
  revalidate: ['posts', 'feed', 'stats'],
});
```

### API Local Definition

```typescript
// api-local.ts - Shared across all platforms
export class ApiLocal {
  private transport: Transport;

  constructor(transport: Transport) {
    this.transport = transport;
  }

  // Queries
  async query<T, E>(
    query: QueryDef<T, E>
  ): Promise<Result<Option<T>, E>> {
    try {
      const result = await this.transport.fetch(query);
      return Ok(some(result));
    } catch (e) {
      return Err(mapError(e));
    }
  }

  // Mutations with revalidation
  async mutate<T, E, R>(
    mutation: MutationDef<T, E, R>
  ): Promise<Result<R, E>> {
    try {
      const result = await this.transport.mutate(mutation);

      // Trigger revalidation
      await this.invalidate(mutation.revalidate);

      return Ok(result);
    } catch (e) {
      return Err(mapError(e));
    }
  }

  // Revalidation
  private async invalidate(keys: string[]) {
    const cache = getCache();
    for (const key of keys) {
      await cache.invalidatePattern(key);
    }
  }
}
```

---

## Platform-Specific Transports

### Web (Direct Database)

```typescript
// web-transport.ts
export class DirectTransport implements Transport {
  constructor(private db: Database) {}

  async fetch<T>(query: QueryDef<T, any>): Promise<T> {
    return this.db.query(query.sql, query.params);
  }

  async mutate<T>(mutation: MutationDef<T, any>): Promise<T> {
    return this.db.mutate(mutation.sql, mutation.params);
  }
}
```

### Mobile/Desktop (Remote API)

```typescript
// mobile-transport.ts
export class RemoteTransport implements Transport {
  constructor(private baseUrl: string) {}

  async fetch<T>(query: QueryDef<T, any>): Promise<T> {
    const response = await fetch(`${this.baseUrl}/query`, {
      method: 'POST',
      body: JSON.stringify(query),
    });
    return response.json();
  }

  async mutate<T>(mutation: MutationDef<T, any>): Promise<T> {
    const response = await fetch(`${this.baseUrl}/mutate`, {
      method: 'POST',
      body: JSON.stringify(mutation),
    });
    return response.json();
  }
}
```

---

## Benefits Summary

```
BENEFITS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   Unified      │  │   Type-Safe    │  │   Real-Time    │
│   API          │  │   Explicitness │  │   Simple       │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ • Same code    │  │ • Result<T, E> │  │ • Cache-based  │
│   for all      │  │ • Option<T>    │  │ • No websockets│
│   platforms    │  │ • No null/     │  │ • Easy scale   │
│ • Single       │  │   undefined    │  │ • Low cost    │
│   source of    │  │ • All errors   │  │                │
│   truth        │  │   explicit     │  │                │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

---

## Next Steps

1. **Define the API Local interface** - Core methods and types
2. **Implement Result/Option utilities** - Error handling primitives
3. **Create transport implementations** - Direct and remote
4. **Build revalidation system** - Key-based invalidation
5. **Integrate with TanStack Query** - Client-side caching

---

## Related Documentation

- [Code Quality System](./code-quality-system.md) - Explicitness rules
- [Agent Knowledge Enhancement](./agent-knowledge-enhancement.md) - Zero Trust philosophy

---

*Document version: 1.0*
*Last updated: 2026-03-04*
