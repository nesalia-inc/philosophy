# 1.1 Type System Basics

> **Learning Goal**: Understand TypeScript's primitive types, special types, and how the type system catches errors at compile time.

## Primitive Types

TypeScript has several primitive types that represent basic JavaScript values:

### String

```typescript
let name: string = "Alice";
const greeting: string = `Hello, ${name}!`;
```

### Number

All numbers in TypeScript are floating-point values:

```typescript
let count: number = 42;
const pi: number = 3.14159;
const hex: number = 0xf00d;
const binary: number = 0b1010;
const octal: number = 0o744;
```

### Boolean

```typescript
let isActive: boolean = true;
const hasPermission: boolean = false;
```

### BigInt

For integers larger than `Number.MAX_SAFE_INTEGER`:

```typescript
let big: bigint = 100n;
const anotherBig: bigint = BigInt(100);
```

### Symbol

For unique object keys:

```typescript
const sym1: symbol = Symbol("key");
const sym2: symbol = Symbol.for("key");
```

## Special Types

### any

**Avoid using `any`** - it completely disables type checking:

```typescript
// Bad: type safety is lost
let anything: any = 42;
anything = "now a string";
anything.foo.bar.baz; // No error, but crashes at runtime
```

Use `any` only as a last resort during migration or when working with inherently untyped code.

### unknown

**Use `unknown` for type-safe values** - requires type narrowing before use:

```typescript
let value: unknown = 42;

// Error: Object is of type 'unknown'
// value.toFixed(2);

// Good: narrow the type first
if (typeof value === "number") {
  value.toFixed(2); // OK: TypeScript knows it's a number
}

// Good: use type guard
function isString(val: unknown): val is string {
  return typeof val === "string";
}

if (isString(value)) {
  console.log(value.toUpperCase()); // OK
}
```

### never

**`never` represents values that never occur** - useful for exhaustive checks:

```typescript
// Function that never returns (always throws)
function fail(message: string): never {
  throw new Error(message);
}

// Function that never returns (infinite loop)
function infiniteLoop(): never {
  while (true) {
    // do something
  }
}

// Exhaustive switch
type Status = "active" | "inactive";

function processStatus(status: Status): string {
  switch (status) {
    case "active":
      return "processing";
    case "inactive":
      return "skipped";
    default: {
      const _exhaustive: never = status; // Error if new status added
      throw new Error(`unhandled status: ${_exhaustive}`);
    }
  }
}
```

### void

**`void` represents the absence of a return value**:

```typescript
function log(message: string): void {
  console.log(message);
  // No return statement needed
}

// Arrow function
const logger = (msg: string): void => {
  console.log(msg);
};
```

> **Note**: In functional programming, we prefer the `Unit` type over `void` for better composability. See Module 3.

## Arrays and Tuples

### Arrays

Type arrays using the type followed by `[]` or `Array<Type>`:

```typescript
// Preferred: using []
const numbers: number[] = [1, 2, 3, 4, 5];
const strings: string[] = ["a", "b", "c"];

// Alternative: generic syntax
const moreNumbers: Array<number> = [1, 2, 3];
```

### Readonly Arrays

Prevent mutations with readonly arrays:

```typescript
const immutable: readonly number[] = [1, 2, 3];
const alsoImmutable: ReadonlyArray<number> = [1, 2, 3];

// Error: Property 'push' does not exist on type 'readonly number[]'
// immutable.push(4);
```

### Tuples

Fixed-length arrays with specific types:

```typescript
// Basic tuple
let tuple: [string, number] = ["hello", 42];

// Destructuring tuples
const [label, value] = tuple;
console.log(label); // "hello"
console.log(value); // 42

// Named tuples (TypeScript 4.0+)
type UserTuple = [name: string, age: number, isActive: boolean];
const user: UserTuple = ["Alice", 30, true];

// Optional tuple elements
type OptionalTuple = [string, number?];
const valid1: OptionalTuple = ["hello"];
const valid2: OptionalTuple = ["hello", 42];
```

### Readonly Tuples

```typescript
const readonlyTuple: readonly [string, number] = ["hello", 42];

// Error: Cannot assign to '0' because it is a read-only property
// readonlyTuple[0] = "world";
```

## Type Annotations vs Type Inference

### Type Inference

TypeScript can infer types in many cases:

```typescript
// Type is inferred as number
let count = 42;

// Type is inferred as string[]
const fruits = ["apple", "banana", "orange"];

// Function return type inference
function add(a: number, b: number) {
  return a + b; // Inferred as number
}
```

### When to Annotate

Annotate when TypeScript cannot infer or when you want to be explicit:

```typescript
// Function parameters require annotations
function greet(name: string) {
  return `Hello, ${name}!`;
}

// Return type annotations for public APIs
function calculateTotal(price: number, quantity: number): number {
  return price * quantity;
}

// Complex types benefit from explicit annotations
type Config = {
  apiUrl: string;
  timeout: number;
  retries: number;
};

function init(config: Config) {
  // ...
}
```

## Type Assertions

Use type assertions when you know more about a value than TypeScript does:

```typescript
// Using 'as' (preferred)
const value = "42" as number; // Assertion to number

// Using angle brackets (not valid in JSX files)
const value2 = <number>"42";

// Asserting to unknown first (double assertion)
const value3 = "hello" as unknown as number;
```

> **Warning**: Type assertions override the compiler. Use sparingly and prefer type guards when possible.

## Best Practices

1. **Avoid `any`** - use `unknown` when the type is truly unknown
2. **Prefer `readonly`** for arrays that shouldn't mutate
3. **Let TypeScript infer** when possible - annotate function parameters and return types for public APIs
4. **Use type guards** instead of assertions when possible
5. **Leverage `never`** for exhaustive checks

## Common Mistakes

### Mistake 1: Using `any` for Convenience

```typescript
// Bad: loses all type safety
function processData(data: any) {
  return data.value;
}

// Good: use unknown with type guard
function processData(data: unknown) {
  if (typeof data === "object" && data !== null && "value" in data) {
    return (data as { value: unknown }).value;
  }
  throw new Error("Invalid data");
}
```

### Mistake 2: Ignoring Array Types

```typescript
// Bad: array of any
const items = [];

// Good: explicit array type
const items: string[] = [];
// Or let TypeScript infer from initial values
const items2 = ["a", "b", "c"];
```

### Mistake 3: Unnecessary Type Assertions

```typescript
// Bad: unnecessary assertion
const count: number = 42 as number;

// Good: let TypeScript infer
const count = 42;
```

## Exercises

1. Create a function that accepts `unknown` and uses type guards to safely handle strings, numbers, and arrays
2. Write an exhaustive switch using `never` for a custom enum
3. Create a tuple type representing a database row with multiple fields
4. Refactor code using `any` to use `unknown` with proper type guards

## Next Lesson

Now that you understand the basic types, let's explore **[1.2 Interfaces vs Types](./02-interfaces-vs-types.md)** to learn when to use each for defining object shapes.
