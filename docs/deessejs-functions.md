# DeesseJS Functions: Local Procedure Pattern

A serverless, local-only procedure pattern for type-safe, validated function composition.

---

## The Problem: No Local tRPC

```
EXISTING SOLUTIONS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

tRPC:        Requires a server + client + network
GraphQL:     Requires a server + resolvers + network
REST:        Requires HTTP + server + network

PROBLEM: When you need:
  * Type-safe function calls
  * Input validation
  * No network overhead
  * No server setup
  * Pure local execution

NONE OF THESE FIT.
```

---

## The Solution: Local Procedures

```
DEESSEJS FUNCTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   No Server. No Network. No HTTP.                         │
│                                                             │
│   Just functions. But better.                              │
│                                                             │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   1. Define Context        → Single source of truth       │
│   2. Define Queries        → Read operations              │
│   3. Define Mutations     → Write operations            │
│   4. Create API           → Compose everything           │
│   5. Call                 → Type-safe, validated         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## The Five Steps

### Step 1: Define Context

```typescript
import { defineContext } from "@deessejs/functions";

// Define your context (single source of truth)
const { t, createAPI } = defineContext<{
  userId: string;
  database: Database;
  logger: Logger;
}>({
  userId: "user-123",
  database: myDatabase,
  logger: console,
});

// t gives you query() and mutation() builders
// createAPI composes everything
```

**Why defineContext?**

```
CONTEXT BENEFITS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. SINGLE SOURCE OF TRUTH
   All dependencies in one place

2. TYPE-SAFE
   Full TypeScript inference

3. TESTABLE
   Swap context for tests

4. COMPOSABLE
   Nest contexts if needed
```

---

### Step 2: Define Queries (Read Operations)

```typescript
const getUser = t.query({
  // Zod schema for args validation
  args: z.object({
    id: z.number(),
  }),

  // Handler receives context and validated args
  handler: async (ctx, args) => {
    const user = await ctx.database.users.findUnique({
      where: { id: args.id },
    });

    if (!user) {
      // Return error (automatically typed)
      return error("USER_NOT_FOUND", { id: args.id });
    }

    // Return success (automatically Result<T, E>)
    return success(user);
  },
});
```

**Query vs Mutation:**

```
QUERY:
* Read-only by convention
* Can be cached (future feature)
* Idempotent
* No side effects expected

MUTATION:
* Write operations
* Triggers cache invalidation (future)
* May have side effects
```

---

### Step 3: Define Mutations (Write Operations)

```typescript
const createUser = t.mutation({
  args: z.object({
    name: z.string().min(2),
    email: z.string().email(),
    age: z.number().min(0).optional(),
  }),

  handler: async (ctx, args) => {
    // Validation happens automatically via Zod
    // If args are invalid, error is returned before handler runs

    try {
      const user = await ctx.database.users.create({
        data: args,
      });

      // Log action (using context)
      ctx.logger.info("User created", { userId: user.id });

      return success(user);
    } catch (e) {
      return error("CREATE_FAILED", { cause: e });
    }
  },
});
```

---

### Step 4: Create API

```typescript
// Compose all procedures into one API
const api = createAPI({
  getUser,
  createUser,
  // Add more procedures here
});
```

**The API Type:**

```typescript
// TypeScript automatically infers:
type API = {
  getUser: (args: { id: number }) => Promise<Result<User, UserError>>;
  createUser: (args: { name: string; email: string; age?: number }) => Promise<Result<User, CreateError>>;
};

// Full type safety - IDE autocomplete works
```

---

### Step 5: Call

```typescript
// Call your endpoints - fully typed
const result = await api.getUser({ id: 123 });

// Result pattern - no exceptions
if (result.ok) {
  console.log("User:", result.value);  // TypeScript knows it's User
} else {
  console.error("Error:", result.error);  // TypeScript knows it's UserError
}
```

---

## The success and error Helpers

### success<T>

```typescript
// Wraps value in Ok result
return success(user);
// Returns: Ok<User>
```

### error<E>

```typescript
// Wraps error in Err result
return error("USER_NOT_FOUND", { id: 123 });
// Returns: Err<{ code: string; details?: any }>
```

### Internal Pattern

```typescript
// Result type is built-in
type Result<T, E = Error> =
  | { ok: true; value: T }
  | { ok: false; error: E };

// success and error are typed helpers
function success<T>(value: T): Result<T, never> {
  return { ok: true, value };
}

function error<E>(code: string, details?: any): Result<never, E> {
  return { ok: false, error: { code, details } as E };
}
```

---

## Full Example

```typescript
import { defineContext, success, error } from "@deessejs/functions";
import { z } from "zod";

// Type definitions
type Database = any;
type Logger = any;

type User = { id: number; name: string; email: string };
type UserError = { code: string; details?: any };

// 1. Define context
const { t, createAPI } = defineContext<{
  userId: string;
  database: Database;
  logger: Logger;
}>({
  userId: "user-123",
  database: myDatabase,
  logger: console,
});

// 2. Define queries
const getUser = t.query({
  args: z.object({ id: z.number() }),
  handler: async (ctx, args) => {
    const user = await ctx.database.users.findUnique({
      where: { id: args.id },
    });
    return user
      ? success(user)
      : error<UserError>("NOT_FOUND", { id: args.id });
  },
});

const listUsers = t.query({
  args: z.object({
    limit: z.number().min(1).max(100).default(10),
  }),
  handler: async (ctx, args) => {
    const users = await ctx.database.users.findMany({
      take: args.limit,
    });
    return success(users);
  },
});

// 3. Define mutations
const createUser = t.mutation({
  args: z.object({
    name: z.string().min(2),
    email: z.string().email(),
  }),
  handler: async (ctx, args) => {
    const existing = await ctx.database.users.findUnique({
      where: { email: args.email },
    });

    if (existing) {
      return error<UserError>("EMAIL_EXISTS", { email: args.email });
    }

    const user = await ctx.database.users.create({ data: args });
    return success(user);
  },
});

// 4. Create API
const api = createAPI({ getUser, listUsers, createUser });

// 5. Use
async function main() {
  // Query
  const user = await api.getUser({ id: 1 });
  if (user.ok) {
    console.log(user.value.name);
  }

  // Mutation
  const created = await api.createUser({
    name: "Alice",
    email: "alice@example.com",
  });

  if (created.ok) {
    console.log("Created:", created.value.id);
  } else {
    console.error(created.error.code);
  }
}
```

---

## Why This Is Not tRPC

```
TRPC VS DEESSEJS FUNCTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────┬─────────────────────────┬─────────────────────────┐
│                     │      tRPC               │   DeesseJS Functions    │
├─────────────────────┼─────────────────────────┼─────────────────────────┤
│ Network             │ HTTP/WebSocket          │ None (local)            │
│ Server required     │ Yes                     │ No                      │
│ Client needed       │ Yes                     │ No                      │
│ HTTP overhead       │ Yes                     │ No                      │
│ Function calls     │ HTTP requests           │ Direct calls            │
│ Type sharing       │ TS plugin               │ Same process            │
│ Performance        │ Network latency         │ In-memory               │
└─────────────────────┴─────────────────────────┴─────────────────────────┘

USE TRPC WHEN:
* You need client-server communication
* You want HTTP/REST semantics
* You need network caching

USE DEESSEJS WHEN:
* All code runs in one process
* You want function composition
* You need type safety without network
* Performance is critical
```

---

## Use Cases

### 1. Micro-services Communication

```typescript
// Same process, different "services"
const userService = createAPI({ getUser, createUser, ... });
const orderService = createAPI({ getOrder, createOrder, ... });
const paymentService = createAPI({ charge, refund, ... });

// Composing in one handler
async function checkout(cartId: number) {
  const cart = await orderService.getCart({ id: cartId });
  if (!cart.ok) return error("CART_NOT_FOUND", cart.error);

  const payment = await paymentService.charge({
    amount: cart.value.total,
    card: cart.value.cardId,
  });
  if (!payment.ok) return error("PAYMENT_FAILED", payment.error);

  return orderService.completeOrder({ cartId });
}
```

### 2. Clean Architecture

```typescript
// Domain layer
const { t, createAPI } = defineContext<{ db: Database }>({ db });

const createOrder = t.mutation({
  args: OrderSchema,
  handler: async (ctx, args) => {
    // Domain logic here
    const validated = validateOrder(args);
    if (!validated) return error("INVALID_ORDER", args);

    const order = await ctx.db.orders.create(validated);
    return success(order);
  },
});

// Repository layer uses the same pattern
// Application layer uses the same pattern
// All type-safe, all tested
```

### 3. Plugin System

```typescript
const coreAPI = createAPI({ ... });

const pluginAPI = createAPI({
  ...coreAPI,  // Extend core
  myPlugin: t.mutation({
    args: z.object({ data: z.string() }),
    handler: async (ctx, args) => {
      // Access full context including core
      const user = await ctx.core.getUser({ id: ctx.userId });
      return success({ plugin: true, user: user.value });
    },
  }),
});
```

---

## Benefits Summary

```
BENEFITS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   Type-Safe    │  │   Validated    │  │   Local        │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ • Full TS      │  │ • Zod schemas  │  │ • No network   │
│ • IDE complete │  │ • Auto-validate│  │ • In-memory    │
│ • Refactor     │  │ • Type inference│ │ • Fast         │
│   safe         │  │                │  │                │
└─────────────────┘  └─────────────────┘  └─────────────────┘

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   Testable      │  │   Composable    │  │   Explicit     │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ • Swap context  │  │ • Nest APIs    │  │ • Result<T, E> │
│ • Mock easily   │  │ • Extend        │  │ • No exceptions│
│ • Pure funcs   │  │ • Plugin        │  │ • Error handling│
│                │  │   friendly      │  │   required     │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

---

## Related Documentation

- [Three Pillars](./three-pillars.md) - Result, Option, Unit philosophy
- [API Local Architecture](./api-local-architecture.md) - High-level ORM

---

*Document version: 1.0*
*Last updated: 2026-03-04*
