# Module 3: Functional Programming

> **Goal**: Master functional programming patterns in TypeScript using Result<T, E>, Option<T>, and Unit types for safer, more composable code.

This module covers functional programming patterns that eliminate runtime exceptions and make illegal states unrepresentable at the type level.

## Learning Objectives

By the end of this module, you will:

- Understand the philosophy behind Result, Option, and Unit types
- Implement error handling without exceptions using Result<T, E>
- Handle absence without null/undefined using Option<T>
- Replace void with Unit for better composability
- Chain operations using monadic patterns
- Refactor imperative code to functional style

## Philosophy

### Eliminating Exceptions

Exceptions break type safety and control flow:

```typescript
// Bad: Exceptions can happen anywhere
function parseUser(json: string): User {
  const data = JSON.parse(json); // May throw
  if (!data.name) throw new Error("Invalid user"); // May throw
  return data;
}

// Good: Explicit error handling
function parseUser(json: string): Result<User, ParseError> {
  const data = safeJsonParse(json);
  if (!data.ok) return Err(data.error);
  if (!data.value.name) return Err(new InvalidUserError());
  return Ok(data.value);
}
```

### Eliminating Null/Undefined

Null and undefined create hidden failures:

```typescript
// Bad: Null checks everywhere
function getUserEmail(userId: string): string | null {
  const user = findUser(userId);
  if (user === null) return null;
  return user.email ?? null;
}

// Good: Explicit absence with Option
function getUserEmail(userId: string): Option<string> {
  const user = findUser(userId);
  if (user.isNone()) return None();
  return fromNullable(user.value.email);
}
```

### Eliminating Void

Void functions cannot be composed:

```typescript
// Bad: Void returns nothing
function log(message: string): void {
  console.log(message);
}

// Good: Unit returns a value
function log(message: string): Unit {
  console.log(message);
  return unit;
}
```

## The Three Pillars

### 1. Result<T, E> - Explicit Failure

Represents operations that can fail with a specific error type:

```typescript
type Result<T, E> = Ok<T> | Err<E>;

interface Ok<T> {
  readonly _tag: "ok";
  readonly value: T;
}

interface Err<E> {
  readonly _tag: "error";
  readonly error: E;
}
```

**Use when**: Operations can fail (network requests, file I/O, parsing)

### 2. Option<T> - Explicit Absence

Represents values that may or may not exist:

```typescript
type Option<T> = Some<T> | None;

interface Some<T> {
  readonly _tag: "some";
  readonly value: T;
}

interface None {
  readonly _tag: "none";
}
```

**Use when**: Values might be absent (optional fields, search results)

### 3. Unit - Explicit Completion

Represents operations that complete successfully:

```typescript
type Unit = { readonly _tag: "unit" };

const unit: Unit = { _tag: "unit" };
```

**Use when**: Operations produce side effects (logging, writing to database)

## Prerequisites

- Completed **[Module 1: Fundamentals](../01-fundamentals/README.md)**
- Completed **[Module 2: Advanced Patterns](../02-advanced-patterns/README.md)**
- Understanding of discriminated unions
- Familiarity with generic types

## Lessons

### 3.1 Result<T, E> Pattern
**[→ Lesson](./01-result-type.md)**

Learn how to handle errors without exceptions using the Result type.

**Key Topics:**
- What is Result<T, E>?
- Creating Ok and Err values
- Chaining with `map`, `flatMap`, `andThen`
- Handling errors with `match` or `getOrElse`
- Combining multiple Results

### 3.2 Option<T> Monad
**[→ Lesson](./02-option-type.md)**

Learn how to handle absence without null/undefined using the Option type.

**Key Topics:**
- What is Option<T>?
- Creating Some and None values
- Chaining with `map`, `flatMap`, `andThen`
- Providing defaults with `getOrElse` and `orElse`
- Working with collections

### 3.3 Unit Type
**[→ Lesson](./03-unit-type.md)**

Learn how to replace void with Unit for better composability.

**Key Topics:**
- What is Unit?
- Using Unit in function signatures
- Composing Unit-returning functions
- Sequencing operations
- Practical examples

### 3.4 Immutability and Readonly
**[→ Lesson](./04-immutability.md)**

Learn techniques for writing immutable code in TypeScript.

**Key Topics:**
- Readonly types and arrays
- Object spreading for updates
- Deep immutability
- Immutable data structures
- Performance considerations

### 3.5 Function Composition
**[→ Lesson](./05-function-composition.md)**

Learn how to compose small functions into larger operations.

**Key Topics:**
- What is function composition?
- Composing functions with `pipe` and `flow`
- Point-free style
- Currying and partial application
- Practical composition patterns

## Common Patterns

### Async Operations with Result

```typescript
async function fetchUser(id: string): Promise<Result<User, NetworkError>> {
  try {
    const response = await fetch(`/api/users/${id}`);
    if (!response.ok) {
      return Err(new NetworkError(response.status));
    }
    const data = await response.json();
    return Ok(data);
  } catch (error) {
    return Err(new NetworkError(0));
  }
}

// Usage
const result = await fetchUser("123");
match(result, {
  ok: (user) => console.log(user.name),
  error: (err) => console.error(err.message)
});
```

### Validation with Result

```typescript
function validateEmail(email: string): Result<string, ValidationError> {
  if (email.includes("@")) {
    return Ok(email);
  }
  return Err(new ValidationError("Invalid email"));
}

function validateAge(age: number): Result<number, ValidationError> {
  if (age >= 0 && age <= 150) {
    return Ok(age);
  }
  return Err(new ValidationError("Invalid age"));
}

function validateUser(data: { email: string; age: number }): Result<User, ValidationError> {
  return all(
    validateEmail(data.email),
    validateAge(data.age)
  ).map(([email, age]) => ({ email, age }));
}
```

### Optional Configuration with Option

```typescript
interface Config {
  apiUrl: string;
  timeout: Option<number>;
  retries: Option<number>;
}

function getConfig(): Config {
  return {
    apiUrl: "https://api.example.com",
    timeout: fromNullable(process.env.TIMEOUT).map(Number),
    retries: fromNullable(process.env.RETRIES).map(Number)
  };
}

function fetchWithConfig(url: string, config: Config): Promise<Response> {
  const timeout = config.timeout.getOrElse(5000);
  const retries = config.retries.getOrElse(3);
  // Implementation
}
```

## Migration Guide

### From Exceptions to Result

```typescript
// Before
function divide(a: number, b: number): number {
  if (b === 0) throw new Error("Division by zero");
  return a / b;
}

// After
function divide(a: number, b: number): Result<number, DivisionByZeroError> {
  if (b === 0) return Err(new DivisionByZeroError());
  return Ok(a / b);
}
```

### From Null to Option

```typescript
// Before
function findUser(id: string): User | null {
  const user = db.users.find(id);
  return user ?? null;
}

// After
function findUser(id: string): Option<User> {
  const user = db.users.find(id);
  return fromNullable(user);
}
```

### From Void to Unit

```typescript
// Before
function log(message: string): void {
  console.log(message);
}

// After
function log(message: string): Unit {
  console.log(message);
  return unit;
}
```

## Benefits

1. **Type Safety**: Errors and absence are explicit in the type system
2. **Composability**: Operations can be chained and combined
3. **Predictability**: No hidden exceptions or null reference errors
4. **Testability**: Pure functions are easier to test
5. **Documentation**: Types encode behavior and constraints

## Trade-offs

- **Learning Curve**: Requires understanding functional programming concepts
- **Verbosity**: More type definitions and explicit error handling
- **Ecosystem**: May require custom libraries or patterns
- **Team Adoption**: Requires team-wide understanding and buy-in

## Best Practices

1. **Always handle Results**: Never ignore error cases
2. **Prefer Option over nullable**: Use Option<T> instead of T | null
3. **Chain operations**: Use `map`, `flatMap`, `andThen` for composition
4. **Use `match` for pattern matching**: Explicit handling of all cases
5. **Keep functions pure**: Avoid side effects in functional code

## Common Pitfalls

- **Nesting Results**: Use `flatMap`/`andThen` to flatten
- **Ignoring errors**: Always handle both Ok and Err cases
- **Mixing patterns**: Don't mix Option with nullable types
- **Over-composing**: Keep chains readable and understandable

## Exercises

After completing these lessons, practice with these exercises:

1. Refactor exception-throwing code to use Result<T, E>
2. Replace null/undefined in a codebase with Option<T>
3. Implement a validation pipeline using Result
4. Create a repository pattern using Result for async operations
5. Build a form validation system using Result

## Next Steps

Once you've mastered functional programming patterns, continue to **[Module 4: Error Handling](../04-error-handling/README.md)** to learn advanced error handling strategies.

## Additional Resources

- [NeverThrow](https://gcanti.github.io/fp-ts/modules/Option.ts.html) - Functional programming library
- [Effect-TS](https://effect.website/) - Effect system for TypeScript
- [ fp-ts](https://gcanti.github.io/fp-ts/) - Functional programming toolkit
- [Practical Functional Programming in TypeScript](https://www.effect.website/docs/guide/)
