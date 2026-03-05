# Result, Option, Unit: The Three Pillars

Why Result<T, E>, Option<T>, and Unit are not monads—they are fundamental building blocks of software that should be woven into every line of code.

---

## The Problem: Null and Exceptions Are Silent Failures

```
TODAY'S DEFAULT (silent failures):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

function getUser(id: string): User {
  return db.find(id);  // What if not found? null? Exception?
}

function processOrder(order: Order): void {
  order.items.forEach(item => {
    // What if this fails? We never know.
    sendEmail(item);
  });
}

PROBLEM: Everything is implicit.
         - null means "maybe"
         - exceptions mean "something went wrong"
         - void means "nothing to care about"

THIS IS WRONG.
```

---

## The Philosophy: Make Everything Explicit

```
THE THREE PILLARS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│                 │  │                 │  │                 │
│    Result<T,   │  │   Option<T>     │  │    Unit        │
│       E>       │  │   (Maybe)       │  │     ()         │
│                 │  │                 │  │                 │
│  Success or    │  │  Something or   │  │  Done, but     │
│  Failure       │  │  Nothing       │  │  no value      │
│                 │  │                 │  │                 │
│  Not a monad.  │  │  Not a monad.  │  │  Not void.     │
│  A necessity.  │  │  A necessity.  │  │  A necessity.  │
│                 │  │                 │  │                 │
└─────────────────┘  └─────────────────┘  └─────────────────┘

These are not "functional programming concepts."
These are not "advanced patterns."
These are BASIC requirements for correct software.
```

---

## Pillar 1: Result<T, E> — All Operations That Can Fail

### Why Result Is Not Optional

```
PROBLEM: Silent errors
━━━━━━━━━━━━━━━━━━━━━

// BAD: Exceptions hide failure
async function createUser(data: UserData): Promise<User> {
  try {
    return await db.create(data);
  } catch (e) {
    // What went wrong? Was it validation? DB down? Duplicate?
    throw e;
  }
}

// GOOD: Result makes failure explicit
async function createUser(data: UserData): Promise<Result<User, CreateUserError>> {
  const validated = ValidateUserData(data);
  if (validated.isErr()) {
    return Err(validated.error);
  }

  try {
    const user = await db.create(validated.value);
    return Ok(user);
  } catch (e) {
    return Err(mapToCreateUserError(e));
  }
}
```

### Result Is Not a Monad—It's a Contract

```
A RESULT IS A CONTRACT:
━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  When you see Result<T, E>, you know:                     │
│                                                             │
│  1. This operation MIGHT fail                             │
│  2. If it fails, here's HOW it fails                      │
│  3. You MUST handle both cases                             │
│                                                             │
│  This is not "functional programming."                     │
│  This is BASIC SOFTWARE CONTRACTING.                       │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### When to Use Result

```
USE RESULT WHEN:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Database operations
* HTTP/API calls
* File system operations
* Validation
* Parsing
* Any external interaction
* Any operation that can fail for ANY reason
```

### The Error Type Must Be Specific

```
GENERIC VS SPECIFIC ERRORS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

// [BAD] Generic error - useless
function getUser(id: string): Result<User, Error> { ... }

// [BAD] Too generic
function getUser(id: string): Result<User, string> { ... }

// [GOOD] Specific, exhaustive error types
type GetUserError =
  | { type: 'not_found'; id: string }
  | { type: 'database_error'; cause: DatabaseError }
  | { type: 'unauthorized' };

function getUser(id: string): Result<User, GetUserError> { ... }
```

---

## Pillar 2: Option<T> — All Values That May Not Exist

### Why Option Is Not Null

```
PROBLEM: null ambiguity
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

// BAD: What does null mean?
interface User {
  id: string;
  name: string;
  email: string | null;    // Why null? User didn't enter email?
  avatar: string | null;   // Or failed to load? Or doesn't exist?
  bio: string | null;     // Or empty string vs null?
}

// GOOD: Option makes absence explicit
interface User {
  id: string;
  name: string;
  email: Option<Email>;          // Provided but not verified?
  avatar: Option<AvatarUrl>;     // Not set yet?
  bio: Option<string>;          // User chose not to write one?
}
```

### Option Is Not a Monad—It's Clarity

```
AN OPTION IS A PROMISE:
━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  When you see Option<T>, you know:                         │
│                                                             │
│  1. This value MIGHT NOT EXIST                            │
│  2. There's a DISTINCTION between:                         │
│     - None (doesn't exist, and that's normal)              │
│     - Error (should have existed but didn't)               │
│  3. You MUST handle both cases                             │
│                                                             │
│  This is not "nullable types."                             │
│  This is EXPLICIT ABSENCE.                                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### When to Use Option

```
USE OPTION WHEN:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Any nullable field in database
* Any optional parameter
* Any field that might not be set
* Any computed value that might not exist
* Any lookup that might return nothing
* Any search with no results
```

### Option vs Result: The Rule

```
OPTION VS RESULT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  OPTION<T>:  "The value might not exist"                  │
│              - Normal, expected absence                    │
│              - Not an error                                │
│              - User didn't provide it                      │
│              - Record doesn't have it                      │
│                                                             │
│  RESULT<T, E>: "The operation might fail"                 │
│              - Abnormal, unexpected                        │
│              - IS an error                                 │
│              - Something went wrong                       │
│              - Need to handle failure                       │
│                                                             │
│  EXAMPLE:                                                  │
│  ─────────────────────────────────────────────────────────│
│  Option<User> user = db.findUser(id);                     │
│  // User might not exist - that's fine, not an error       │
│                                                             │
│  Result<User, GetUserError> user = db.findUser(id);       │
│  // Wait, why is this Result?                             │
│  // Oh, database might be down - that's an error           │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Pillar 3: Unit — All Operations That Complete

### Why Unit Is Not Void

```
PROBLEM: void is meaningless
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

// BAD: void tells us nothing
async function sendEmail(to: string, subject: string): Promise<void> {
  // What happened? Sent? Failed? Retrying?
}

// GOOD: Unit tells us completion
async function sendEmail(to: string, subject: string): Promise<Result<Unit, EmailError>> {
  // Result<Unit, E> = operation completed (success)
  // but we need to know if email actually sent
}

// BETTER: If we don't care about the result:
async function logAccess(userId: string): Promise<Unit> {
  // Just logging - we don't care if it succeeds
  // But we still acknowledge completion
}
```

### Unit Is Not Nothing—It's Acknowledgment

```
A UNIT IS AN ACKNOWLEDGMENT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  When you see Unit, you know:                               │
│                                                             │
│  1. The operation COMPLETED                                 │
│  2. There was no meaningful VALUE to return                │
│  3. Success was achieved (or failure was handled)          │
│  4. The computation is DONE                                 │
│                                                             │
│  This is not "void."                                        │
│  This is "COMPLETED SUCCESSFULLY."                          │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## The Three Pillars in Practice

### The Standard Return Types

```
STANDARD API SIGNATURES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│  Query (might find or not):                                │
│  ─────────────────────────────────────────────────────────  │
│  getUser(id):        Result<Option<User>, GetUserError>    │
│  searchUsers(query): Result<Option<User[]>, SearchError>   │
│                                                             │
│  Mutation (always completes):                              │
│  ─────────────────────────────────────────────────────────  │
│  createUser(data):   Result<User, CreateUserError>         │
│  deleteUser(id):     Result<Unit, DeleteUserError>         │
│  updateUser(id, d):  Result<User, UpdateUserError>         │
│                                                             │
│  Side Effects (fire and forget):                          │
│  ─────────────────────────────────────────────────────────  │
│  logEvent(event):   Result<Unit, LogError>                │
│  sendEmail(to, ...): Result<Unit, EmailError>             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Code Example

```typescript
// Traditional (BAD):
function processOrder(orderId: string): Order {
  const order = db.findOrder(orderId);
  const user = db.findUser(order.userId);
  sendEmail(user.email, 'Your order is processing');
  return order;
}

// With Three Pillars (GOOD):
type ProcessOrderError =
  | { type: 'order_not_found'; orderId: string }
  | { type: 'user_not_found'; userId: string }
  | { type: 'email_failed'; cause: EmailError };

function processOrder(orderId: string): Result<Order, ProcessOrderError> {
  // Step 1: Get order (might not exist)
  const orderOpt = db.findOrder(orderId);
  if (isNone(orderOpt)) {
    return Err({ type: 'order_not_found', orderId });
  }
  const order = orderOpt.value;

  // Step 2: Get user (might not exist)
  const userOpt = db.findUser(order.userId);
  if (isNone(userOpt)) {
    return Err({ type: 'user_not_found', userId: order.userId });
  }
  const user = userOpt.value;

  // Step 3: Send email (might fail)
  const emailResult = sendEmail(user.email, 'Your order is processing');
  if (emailResult.isErr()) {
    return Err({ type: 'email_failed', cause: emailResult.error });
  }

  // Step 4: Return success
  return Ok(order);
}
```

---

## Why This Matters for Agents

```
AGENT PERSPECTIVE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

When an agent writes code with Three Pillars:

1. AGENT CAN'T HIDE FAILURES
   * Every Result forces handling
   * Agent must think about what can go wrong

2. AGENT CAN'T AMBIGUATE ABSENCE
   * Every Option forces clarity
   * Agent must distinguish "not found" from "error"

3. AGENT CAN'T IGNORE COMPLETION
   * Every Unit forces acknowledgment
   * Agent must confirm operations finished

4. AGENT PRODUCES CORRECT CODE BY DEFAULT
   * Not because it's "functional programming"
   * Because the TYPES MAKE IT IMPOSSIBLE TO DO WRONG
```

---

## Implementation Guidelines

### TypeScript Implementation

```typescript
// result.ts
type Result<T, E> = { ok: true; value: T } | { ok: false; error: E };

function Ok<T, E>(value: T): Result<T, E> {
  return { ok: true, value };
}

function Err<T, E>(error: E): Result<T, E> {
  return { ok: false, error };
}

// option.ts
type Option<T> = { type: 'some'; value: T } | { type: 'none' };

function Some<T>(value: T): Option<T> {
  return { type: 'some', value };
}

function None<T>(): Option<T> {
  return { type: 'none' };
}

// unit.ts
type Unit = {};
const UNIT: Unit = {};
```

### No Exceptions Policy

```
NO EXCEPTIONS POLICY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. NEVER throw exceptions in application code
   ───────────────────────────────────────────
   Use Result<T, E> instead

2. NEVER catch exceptions without rethrowing as Result
   ───────────────────────────────────────────────────
   Convert to Result or don't catch

3. NEVER use try/catch for control flow
   ─────────────────────────────────────
   Use Result for expected failures

4. EXCEPTIONS ARE RESERVED FOR:
   ──────────────────────────────
   - Programming bugs (assertions)
   - Catastrophic failures (out of memory)
   - That's it.
```

---

## Related Documentation

- [API Local Architecture](./api-local-architecture.md) - Uses Result/Option
- [Code Quality System](./code-quality-system.md) - Explicitness requirements

---

*Document version: 1.0*
*Last updated: 2026-03-04*
