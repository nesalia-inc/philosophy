# 2.6 Branded and Opaque Types

> **Learning Goal**: Create nominal types for domain primitives and prevent type confusion between semantically different values.

## The Problem with Primitive Obsession

TypeScript's structural typing can lead to bugs where semantically different values are accidentally mixed:

```typescript
// Bad: Both are just strings
function getUser(id: string) {
  // ...
}

function getOrder(id: string) {
  // ...
}

// Easy to mix up by accident
const userId = "user-123";
const orderId = "order-456";

getUser(orderId); // Compiles but is wrong!
```

## Branded Types Pattern

Branded types add a nominal type system on top of TypeScript's structural typing by intersecting with a unique brand.

### Basic Branded Type

```typescript
// Define branded types
type UserId = string & { readonly __brand: "UserId" };
type OrderId = string & { readonly __brand: "OrderId" };

// Constructor functions
function createUserId(id: string): UserId {
  return id as UserId;
}

function createOrderId(id: string): OrderId {
  return id as OrderId;
}

// Functions that use branded types
function getUser(id: UserId) {
  console.log(`Getting user: ${id}`);
}

function getOrder(id: OrderId) {
  console.log(`Getting order: ${id}`);
}

// Usage
const userId = createUserId("user-123");
const orderId = createOrderId("order-456");

getUser(userId); // OK
getUser(orderId); // Error: Argument of type 'OrderId' is not assignable to parameter of type 'UserId'
```

### Generic Brand Helper

```typescript
// Generic brand type
type Brand<T, B> = T & { readonly __brand: B };

// Domain-specific types
type UserId = Brand<string, "UserId">;
type OrderId = Brand<string, "OrderId">;
type Email = Brand<string, "Email">;
type Url = Brand<string, "Url">;

// Constructor functions
function createUserId(id: string): UserId {
  return id as UserId;
}

function createEmail(email: string): Email {
  if (!email.includes("@")) {
    throw new Error("Invalid email");
  }
  return email as Email;
}

// Usage
const userId = createUserId("user-123");
const email = createEmail("user@example.com");

// Cannot accidentally mix
// getUser(email); // Error: Argument of type 'Email' is not assignable to parameter of type 'UserId'
```

### Validated Branded Types

Combine branding with runtime validation:

```typescript
type Email = string & { readonly __brand: "Email" };

function createEmail(email: string): Email {
  if (!email.includes("@")) {
    throw new Error("Invalid email format");
  }
  if (!email.includes(".")) {
    throw new Error("Invalid email format");
  }
  return email as Email;
}

function isEmail(value: string): value is Email {
  return value.includes("@") && value.includes(".");
}

// Usage
const email = createEmail("user@example.com"); // OK

try {
  const invalid = createEmail("invalid"); // Throws
} catch (error) {
  console.error(error);
}

// Type guard
if (isEmail("user@example.com")) {
  // TypeScript knows it's a valid Email
}
```

## Numeric Branded Types

Prevent mixing of semantically different numbers:

```typescript
type Celsius = number & { readonly __brand: "Celsius" };
type Fahrenheit = number & { readonly __brand: "Fahrenheit" };

function celsius(c: number): Celsius {
  return c as Celsius;
}

function fahrenheit(f: number): Fahrenheit {
  return f as Fahrenheit;
}

function toFahrenheit(c: Celsius): Fahrenheit {
  return (c * 9 / 5) + 32 as Fahrenheit;
}

function toCelsius(f: Fahrenheit): Celsius {
  return (f - 32) * 5 / 9 as Celsius;
}

// Usage
const tempC = celsius(25);
const tempF = toFahrenheit(tempC); // OK

// toFahrenheit(100); // Error: Argument of type 'number' is not assignable to parameter of type 'Celsius'
```

## ID Type Patterns

### Typed IDs

```typescript
type UserId = Brand<string, "UserId">;
type PostId = Brand<string, "PostId">;
type CommentId = Brand<string, "CommentId">;

interface User {
  id: UserId;
  name: string;
}

interface Post {
  id: PostId;
  authorId: UserId; // Reference to user
  title: string;
}

interface Comment {
  id: CommentId;
  postId: PostId; // Reference to post
  authorId: UserId; // Reference to user
  content: string;
}

// Functions
function getUserById(id: UserId): User | null {
  // Implementation
  return null;
}

function getPostById(id: PostId): Post | null {
  // Implementation
  return null;
}

// Usage
const userId = "user-123" as UserId;
const postId = "post-456" as PostId;

getUserById(userId); // OK
// getUserById(postId); // Error: Type mismatch
```

### Database IDs

```typescript
type DatabaseId<T> = Brand<string, T>;

type UserId = DatabaseId<"User">;
type OrderId = DatabaseId<"Order">;
type ProductId = DatabaseId<"Product">;

// Generic ID generator
function generateId<T>(prefix: string): DatabaseId<T> {
  return `${prefix}-${crypto.randomUUID()}` as DatabaseId<T>;
}

const userId = generateId<User>("user");
const orderId = generateId<Order>("order");

// Type-safe repository pattern
interface Repository<T, ID extends DatabaseId<any>> {
  findById(id: ID): Promise<T | null>;
  findAll(): Promise<T[]>;
  create(entity: Omit<T, "id">): Promise<T>;
  update(id: ID, updates: Partial<T>): Promise<T>;
  delete(id: ID): Promise<void>;
}

interface User {
  id: UserId;
  name: string;
  email: string;
}

class UserRepository implements Repository<User, UserId> {
  async findById(id: UserId): Promise<User | null> {
    // Implementation
    return null;
  }

  // ... other methods
}
```

## Opaque Types with type-fest

For more ergonomic opaque types, use the `type-fest` library:

```typescript
import type { Opaque } from "type-fest";

// Define opaque types
type UserId = Opaque<string, "UserId">;
type OrderId = Opaque<string, "OrderId">;

// Constructor functions
function createUserId(id: string): UserId {
  return id as UserId;
}

function createOrderId(id: string): OrderId {
  return id as OrderId;
}

// Usage
const userId = createUserId("user-123");
const orderId = createOrderId("order-456");

// Type error: Cannot mix different opaque types
// getUser(orderId);
```

### Benefits of type-fest Opaque

- **Cleaner syntax** than manual branding
- **Better IDE support** with proper type names
- **Same type safety** as manual branding
- **Works with TypeScript 5.0+**

## Domain Primitive Pattern

Combine branding with validation for domain primitives:

```typescript
// Email address
type Email = Brand<string, "Email">;

function createEmail(email: string): Email {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!emailRegex.test(email)) {
    throw new Error(`Invalid email: ${email}`);
  }
  return email as Email;
}

// Phone number
type PhoneNumber = Brand<string, "PhoneNumber">;

function createPhoneNumber(phone: string): PhoneNumber {
  const digits = phone.replace(/\D/g, "");
  if (digits.length < 10) {
    throw new Error(`Invalid phone number: ${phone}`);
  }
  return phone as PhoneNumber;
}

// Money amount
type Money = Brand<number, "Money">;

function createMoney(amount: number): Money {
  if (amount < 0) {
    throw new Error(`Money cannot be negative: ${amount}`);
  }
  return amount as Money;
}

// User with domain primitives
interface User {
  id: UserId;
  email: Email;
  phoneNumber: PhoneNumber;
  balance: Money;
}

// Usage
const user: User = {
  id: "user-123" as UserId,
  email: createEmail("user@example.com"),
  phoneNumber: createPhoneNumber("+1 (555) 123-4567"),
  balance: createMoney(1000)
};
```

## Type-Safe Units

Prevent unit confusion:

```typescript
type Meters = Brand<number, "Meters">;
type Feet = Brand<number, "Feet">;
type Kilograms = Brand<number, "Kilograms">;
type Pounds = Brand<number, "Pounds">;

function meters(m: number): Meters {
  return m as Meters;
}

function feet(f: number): Feet {
  return f as Feet;
}

function metersToFeet(m: Meters): Feet {
  return (m * 3.28084) as Feet;
}

function feetToMeters(f: Feet): Meters {
  return (f * 0.3048) as Meters;
}

// Usage
const distanceM = meters(100);
const distanceF = metersToFeet(distanceM); // OK

// Cannot accidentally add different units
// const total = meters(100) + feet(50); // Error: Operator '+' cannot be applied to types 'Meters' and 'Feet'
```

## Type-Safe Identifiers

### API Token Types

```typescript
type ApiKey = Brand<string, "ApiKey">;
type SessionToken = Brand<string, "SessionToken">;
type RefreshToken = Brand<string, "RefreshToken">;

function createApiKey(key: string): ApiKey {
  if (!key.startsWith("key_")) {
    throw new Error("Invalid API key format");
  }
  return key as ApiKey;
}

function createSessionToken(token: string): SessionToken {
  if (token.length < 32) {
    throw new Error("Invalid session token");
  }
  return token as SessionToken;
}

// Usage
const apiKey = createApiKey("key_abc123");
const sessionToken = createSessionToken(crypto.randomUUID());

function authenticateWithKey(key: ApiKey): void {
  // Implementation
}

function authenticateWithSession(token: SessionToken): void {
  // Implementation
}

authenticateWithKey(apiKey); // OK
// authenticateWithKey(sessionToken); // Error: Type mismatch
```

### Resource Identifiers

```typescript
type ResourceId<T> = Brand<string, T>;

type UserId = ResourceId<"User">;
type PostId = ResourceId<"Post">;
type CommentId = ResourceId<"Comment">;

interface Resource<T> {
  id: ResourceId<T>;
  type: T;
  attributes: Record<string, unknown>;
}

interface UserResource extends Resource<"User"> {
  attributes: {
    name: string;
    email: string;
  };
}

interface PostResource extends Resource<"Post"> {
  attributes: {
    title: string;
    content: string;
  };
}
```

## Serialization and Deserialization

### JSON Serialization

```typescript
type UserId = Brand<string, "UserId">;

// Serialization
function serializeUserId(id: UserId): string {
  return id; // Just the string value
}

// Deserialization
function deserializeUserId(json: string): UserId {
  return json as UserId;
}

// Usage
const userId = "user-123" as UserId;
const json = JSON.stringify({ userId: serializeUserId(userId) });
const parsed = JSON.parse(json);
const restoredUserId = deserializeUserId(parsed.userId);
```

### Type Guards for Validation

```typescript
function isUserId(value: string): value is UserId {
  return value.startsWith("user-");
}

function parseUserId(value: string): UserId {
  if (!isUserId(value)) {
    throw new Error(`Invalid user ID: ${value}`);
  }
  return value as UserId;
}

// Usage
const value = "user-123";
if (isUserId(value)) {
  // TypeScript knows it's a UserId
  console.log(value); // UserId
}
```

## Best Practices

1. **Always use constructor functions** for branded types:
   ```typescript
   function createUserId(id: string): UserId {
     return id as UserId;
   }

   const userId = createUserId("user-123"); // Good
   // const userId = "user-123" as UserId; // Bad: No validation
   ```

2. **Include runtime validation** when creating branded types:
   ```typescript
   function createEmail(email: string): Email {
     if (!isValidEmail(email)) {
       throw new Error("Invalid email");
     }
     return email as Email;
   }
   ```

3. **Use branded types for domain primitives**:
   ```typescript
   type Email = Brand<string, "Email">;
   type UserId = Brand<string, "UserId">;
   type Money = Brand<number, "Money">;
   ```

4. **Prefer opaque types** with type-fest for cleaner syntax:
   ```typescript
   import type { Opaque } from "type-fest";

   type UserId = Opaque<string, "UserId">;
   ```

5. **Document the brand** in JSDoc comments:
   ```typescript
   /**
    * A user ID in the format "user-{uuid}"
    */
   type UserId = Brand<string, "UserId">;
   ```

## Common Mistakes

### Mistake 1: Using Type Assertions Directly

```typescript
// Bad: Bypasses validation
const userId = "user-123" as UserId;

// Good: Use constructor function
const userId = createUserId("user-123");
```

### Mistake 2: Not Validating at Runtime

```typescript
// Bad: No runtime validation
function createUserId(id: string): UserId {
  return id as UserId;
}

// Good: Validate the format
function createUserId(id: string): UserId {
  if (!id.startsWith("user-")) {
    throw new Error("Invalid user ID format");
  }
  return id as UserId;
}
```

### Mistake 3: Forgetting to Serialize

```typescript
// Bad: Trying to serialize branded type directly
const json = JSON.stringify({ userId }); // Results in {}

// Good: Serialize the underlying value
const json = JSON.stringify({ userId: serializeUserId(userId) });
```

## Exercises

1. Create branded types for Email, PhoneNumber, and ZipCode
2. Implement a Money type with currency branding
3. Create type-safe IDs for a blog API (PostId, CommentId, AuthorId)
4. Implement unit types (Meters, Feet, Celsius, Fahrenheit)
5. Create a UserId type with validation for UUID format

## Summary

Branded and opaque types add nominal typing to TypeScript, preventing accidental mixing of semantically different values:

- **Prevent bugs** from mixing incompatible values
- **Encode domain logic** in the type system
- **Improve code documentation** with explicit types
- **Enable safer refactoring** with compile-time checks

Use them for domain primitives, IDs, and any value where type safety is critical.

## Next Steps

Congratulations! You've completed **Module 2: Advanced Patterns**. Continue to **[Module 3: Functional Programming](../03-functional-programming/README.md)** to learn about Result, Option, Unit, and functional programming patterns.
