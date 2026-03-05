# Framework Primitives

Building blocks with built-in gates.

---

## The Vision

Every common building block should be a primitive with gates:

```
Every primitive has THREE gates:
1. COMPILE TIME: Types enforce correct usage
2. RUNTIME: Middleware intercepts calls
3. PR TIME (AST): Verifies all access points use primitive
```

---

## The Concept

```
PRIMITIVES WITH GATES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   PRIMITIVE = API + GATES                                  │
│                                                             │
│   ┌─────────────────────────────────────────────────┐      │
│   │                   PRIMITIVE                      │      │
│   │                                                  │      │
│   │   API (what you use)                            │      │
│   │   ─────────────────                              │      │
│   │   type RBAC = {                                  │      │
│   │     can: (perm) => boolean,                     │      │
│   │     check: (perm) => void,                      │      │
│   │   }                                              │      │
│   │                                                  │      │
│   │   GATES (built-in verification)                 │      │
│   │   ───────────────────────────                     │      │
│   │   1. COMPILE: Types enforce usage              │      │
│   │   2. RUNTIME: Middleware intercepts            │      │
│   │   3. PR TIME: AST verifies patterns            │      │
│   │                                                  │      │
│   └─────────────────────────────────────────────────┘      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Example: RBAC Primitive

### 1. Define Permissions as Code

```typescript
const Permissions = {
  USER_READ: 'user:read',
  USER_CREATE: 'user:create',
  USER_UPDATE: 'user:update',
  USER_DELETE: 'user:delete',
  ORDER_READ: 'order:read',
  ORDER_CREATE: 'order:create',
  ORDER_UPDATE: 'order:update',
  ORDER_DELETE: 'order:delete',
} as const;

type Permission = typeof Permissions[keyof typeof Permissions];
```

### 2. Define Roles

```typescript
const Roles = {
  ADMIN: ['*'],  // Wildcard = all permissions
  MANAGER: [
    Permissions.USER_READ,
    Permissions.USER_CREATE,
    Permissions.USER_UPDATE,
    Permissions.ORDER_CREATE,
    Permissions.ORDER_READ,
    Permissions.ORDER_UPDATE,
  ],
  USER: [Permissions.USER_READ],
  GUEST: [],
} as const;

type Role = keyof typeof Roles;
```

### 3. Compile Time: Types Enforce Usage

```typescript
// Decorator enforces permission at compile time
@RequirePermission(Permissions.USER_CREATE)
async function createUser(req: CreateUserRequest): Promise<User> {
  // Implementation
}

// TypeScript knows this is safe
// If you try to use without permission, compile error
```

### 4. Runtime: Middleware Intercepts

```typescript
// Gate middleware intercepts every request
const rbacMiddleware: Middleware = async (ctx, next) => {
  const { permission, userId } = ctx.req;

  // Check permission
  const hasPermission = await rbac.can(userId, permission);
  if (!hasPermission) {
    // Log access attempt
    await audit.log({
      userId,
      action: 'denied',
      permission,
      resource: ctx.req.path,
    });

    throw new ForbiddenError(permission);
  }

  // Log successful access
  await audit.log({
    userId,
    action: 'allowed',
    permission,
  });

  return next();
};
```

### 5. PR Time: AST Verifies Patterns

```
AST GATE CHECKS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Every @RequirePermission has a test
   * find all @RequirePermission
   * verify each has corresponding test

2. Every permission is used
   * find all Permissions values
   * verify each is referenced in code

3. No hardcoded permission checks
   * grep for: if (user.permissions.includes(...))
   * require: use rbac.can() instead

4. All roles tested
   * find all Roles values
   * verify each role tested in RBAC tests
```

---

## Frontend Performance Primitives

### Image Primitive

```tsx
// Gate verifies at compile time
<Image
  src="/photo.jpg"
  width={800}    // Required - prevents layout shift
  height={600}   // Required - prevents layout shift
  loading="lazy" // Required for below-fold images
  alt="Photo"
/>

// AST Gate checks:
// - Has width and height
// - Has loading="lazy" if not above fold
// - Has alt text
// - src is optimized format (webp/avif)
```

### CSS Performance Gate

```css
/* Gate verifies:
 * - No unused CSS classes
 * - No expensive selectors (>:nth-child, etc.)
 * - No layout thrashing
 * - Critical CSS inlined
 */
```

### Bundle Size Gate

```
AST GATE CHECKS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. No new packages > 10KB (uncompressed)
2. Bundle size < threshold per route
3. No duplicate dependencies
4. Tree-shaking works correctly
5. Dynamic imports used for large modules
```

---

## Authentication Primitive

```typescript
const Auth = definePrimitive({
  name: 'auth',

  // API
  login: (credentials) => Promise<Result<Session, LoginError>>,
  logout: () => Promise<Result<Unit, LogoutError>>,
  refresh: (token) => Promise<Result<Session, RefreshError>>,
  verify: (token) => Promise<Result<User, VerifyError>>,

  // Gates
  compileTime: {
    // Types enforce: must handle errors
    // Cannot ignore Result
  },
  runtime: {
    // Token rotation automatic
    // Refresh before expiry
    // Secure storage
  },
  prTime: {
    // All auth flows tested
    // No hardcoded credentials
    // All error paths tested
  }
});
```

---

## Complete Primitive List

### Authentication

```
PRIMITIVES:
* Login/logout flows
* Token management (JWT, refresh, rotation)
* Multi-factor authentication
* Password reset
* Session management

GATES:
* Compile: Result types enforce error handling
* Runtime: Token refresh, secure storage
* PR Time: All flows tested, no hardcoded creds
```

### Authorization (RBAC/ABAC)

```
PRIMITIVES:
* Permission definitions
* Role definitions
* Policy engine
* Audit logging

GATES:
* Compile: @RequirePermission decorator
* Runtime: Middleware intercepts
* PR Time: AST verifies all access controlled
```

### Data Access

```
PRIMITIVES:
* Repository pattern
* Query builders
* Pagination
* Soft deletes
* Optimistic locking

GATES:
* Compile: Typed queries, Result return
* Runtime: Connection pooling, retries
* PR Time: All queries tested, no SQL injection
```

### Validation

```
PRIMITIVES:
* Input validation (schema-based)
* Cross-field validation
* Async validation
* Sanitization

GATES:
* Compile: Zod schemas, type inference
* Runtime: Validates before processing
* PR Time: All schemas tested, edge cases covered
```

### Error Handling

```
PRIMITIVES:
* Error codes
* User-facing errors
* Recovery strategies
* Retry logic

GATES:
* Compile: Result<T, E> enforced
* Runtime: Error logging, recovery
* PR Time: All error paths tested
```

### Caching

```
PRIMITIVES:
* Cache strategies (cache-aside, write-through)
* Invalidation patterns
* TTL management

GATES:
* Compile: Type-safe cache keys
* Runtime: Automatic invalidation
* PR Time: All cache paths tested
```

### Notifications

```
PRIMITIVES:
* Channel abstraction (email, SMS, push)
* Template system
* Rate limiting

GATES:
* Compile: Typed templates
* Runtime: Rate limits, retries
* PR Time: All templates tested
```

### Payments

```
PRIMITIVES:
* Provider abstraction (Stripe, etc.)
* Webhook handling
* Idempotency

GATES:
* Compile: Result types for all operations
* Runtime: Idempotency keys, webhooks verified
* PR Time: All payment flows tested
```

### Search

```
PRIMITIVES:
* Query abstraction
* Faceted search
* Pagination
* Highlighting

GATES:
* Compile: Typed queries
* Runtime: Rate limits, caching
* PR Time: All search paths tested
```

---

## Primitive Composition

```
COMPOSING PRIMITIVES
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Primitives can be composed:

const api = createAPI({
  // Authentication primitive
  auth: Auth,

  // RBAC primitive (depends on auth)
  rbac: RBAC,

  // Data access primitive
  users: Repository<User>,

  // All gates stacked automatically
});

// Every call goes through:
// 1. Auth gate (are you logged in?)
// 2. RBAC gate (do you have permission?)
// 3. Repository gate (valid input?)
// 4. Error handling gate (handle errors?)

const result = await api.users.create({ name: "Alice" });
```

---

## Benefits

```
BENEFITS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────┐  ┌─────────────────────┐
│   VERIFIED          │  │   COMPOSABLE        │
├─────────────────────┤  ├─────────────────────┤
│ • Compile time     │  │ • Stack primitives  │
│ • Runtime         │  │ • Any combination   │
│ • PR time         │  │ • Consistent API    │
└─────────────────────┘  └─────────────────────┘

┌─────────────────────┐  ┌─────────────────────┐
│   MAINTAINABLE     │  │   SECURE BY         │
├─────────────────────┤  ├─────────────────────┤
│ • Change in one   │  │   DEFAULT           │
│   place           │  │ • Gates can't be    │
│ • Clear contracts │  │   bypassed          │
│ • Tested once     │  │ • Built-in          │
└─────────────────────┘  └─────────────────────┘
```

---

## Related Documentation

- [Verification Gates](./verification-gates.md) - Gate system
- [Meta-Framework Philosophy](./meta-framework-philosophy.md) - Overall vision
- [Three Pillars](./three-pillars.md) - Result, Option, Unit

---

*Document version: 1.0*
*Last updated: 2026-03-04*
