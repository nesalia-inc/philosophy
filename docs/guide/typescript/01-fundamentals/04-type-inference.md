# 1.4 Type Inference and Control Flow

> **Learning Goal**: Leverage TypeScript's type inference and control flow analysis to write cleaner, safer code with fewer type annotations.

## Type Inference

TypeScript can infer types from initial values and usage patterns, reducing the need for explicit annotations.

### Variable Declaration Inference

```typescript
// Type is inferred as number
let count = 42;

// Type is inferred as string
let name = "Alice";

// Type is inferred as boolean
let isActive = true;

// Type is inferred as number[]
const numbers = [1, 2, 3, 4, 5];

// Type is inferred as (string | number)[]
const mixed = [1, "two", 3, "four"];
```

### Function Return Type Inference

```typescript
// Return type inferred as number
function add(a: number, b: number) {
  return a + b;
}

// Return type inferred as string
function greet(name: string) {
  return `Hello, ${name}!`;
}

// Return type inferred as number | null
function parseInput(input: string) {
  const parsed = parseInt(input);
  return isNaN(parsed) ? null : parsed;
}
```

### Best Practices for Inference

```typescript
// Good: Let TypeScript infer when obvious
const count = 42; // number
const name = "Alice"; // string
const active = true; // boolean

// Good: Explicit types for public APIs
function calculateTotal(price: number, quantity: number): number {
  return price * quantity;
}

// Good: Explicit types for complex types
type Config = {
  apiUrl: string;
  timeout: number;
  retries: number;
};

function init(config: Config) {
  // ...
}

// Good: Explicit types when inference isn't clear
const data = JSON.parse(jsonString) as UserData;
```

## Contextual Typing

TypeScript uses context to infer types, especially in callbacks and event handlers.

### Array Methods

```typescript
const numbers = [1, 2, 3, 4, 5];

// 'n' is inferred as number
const doubled = numbers.map(n => n * 2);

// 'a' and 'b' are inferred as number
const sorted = numbers.sort((a, b) => a - b);

// 'acc' and 'curr' are inferred as number
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
```

### Event Handlers

```typescript
// TypeScript knows the type from addEventListener signature
document.addEventListener("click", (event) => {
  // 'event' is inferred as MouseEvent
  console.log(event.clientX, event.clientY);
});

// Button click handler
const button = document.querySelector("button");
button?.addEventListener("click", (e) => {
  // 'e' is inferred as MouseEvent
  e.preventDefault();
});
```

### Callbacks

```typescript
function fetchData(callback: (data: string) => void) {
  setTimeout(() => {
    callback("data received");
  }, 1000);
}

// 'data' is inferred as string
fetchData((data) => {
  console.log(data.toUpperCase()); // OK
});
```

## Control Flow Analysis

TypeScript uses control flow analysis to narrow types based on runtime checks.

### Type Narrowing with typeof

```typescript
function processValue(value: string | number) {
  if (typeof value === "string") {
    // TypeScript knows 'value' is string here
    console.log(value.toUpperCase());
  } else {
    // TypeScript knows 'value' is number here
    console.log(value.toFixed(2));
  }
}
```

### Type Narrowing with instanceof

```typescript
class Dog {
  bark() { console.log("Woof!"); }
}

class Cat {
  meow() { console.log("Meow!"); }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark(); // OK
  } else {
    animal.meow(); // OK
  }
}
```

### Equality Narrowing

```typescript
function processValue(value: string | number | null) {
  if (value !== null) {
    // TypeScript knows 'value' is string | number
    if (typeof value === "string") {
      console.log(value.toUpperCase()); // OK
    } else {
      console.log(value.toFixed(2)); // OK
    }
  }
}
```

### Property Presence Narrowing

```typescript
interface Circle {
  kind: "circle";
  radius: number;
}

interface Rectangle {
  kind: "rectangle";
  width: number;
  height: number;
}

type Shape = Circle | Rectangle;

function getArea(shape: Shape) {
  if (shape.kind === "circle") {
    // TypeScript knows 'shape' is Circle
    return Math.PI * shape.radius ** 2;
  } else {
    // TypeScript knows 'shape' is Rectangle
    return shape.width * shape.height;
  }
}
```

## Type Guards

### Custom Type Guards

```typescript
interface Fish {
  swim: () => void;
}

interface Bird {
  fly: () => void;
}

function isFish(pet: Fish | Bird): pet is Fish {
  return (pet as Fish).swim !== undefined;
}

function move(pet: Fish | Bird) {
  if (isFish(pet)) {
    pet.swim(); // TypeScript knows it's Fish
  } else {
    pet.fly(); // TypeScript knows it's Bird
  }
}
```

### Assertion Functions

```typescript
function assertIsString(value: unknown): asserts value is string {
  if (typeof value !== "string") {
    throw new Error("Value is not a string");
  }
}

function processValue(value: unknown) {
  assertIsString(value);
  // After this, TypeScript knows 'value' is string
  console.log(value.toUpperCase());
}
```

### Predicate Type Guards

```typescript
interface User {
  name: string;
  email?: string;
}

function hasEmail(user: User): user is User & { email: string } {
  return user.email !== undefined;
}

const users: User[] = [
  { name: "Alice", email: "alice@example.com" },
  { name: "Bob" }
];

const usersWithEmail = users.filter(hasEmail);
// Type is (User & { email: string })[]
```

## Nullish Coalescing and Optional Chaining

### Nullish Coalescing Operator (??)

```typescript
function processName(name: string | null) {
  // Returns right side only if left side is null or undefined
  const displayName = name ?? "Anonymous";
  console.log(displayName);
}

processName("Alice"); // "Alice"
processName(null); // "Anonymous"
processName(undefined); // "Anonymous"
```

### Optional Chaining Operator (?.)

```typescript
interface User {
  name: string;
  address?: {
    street?: string;
    city?: string;
  };
}

function getCity(user: User): string | undefined {
  // Safe navigation through potentially undefined properties
  return user.address?.city;
}

const user1: User = { name: "Alice" };
console.log(getCity(user1)); // undefined

const user2: User = {
  name: "Bob",
  address: { city: "New York" }
};
console.log(getCity(user2)); // "New York"
```

### Nullish Assignment Operator (??=)

```typescript
let config: { timeout?: number } = {};

// Only assigns if config.timeout is null or undefined
config.timeout ??= 3000;

console.log(config.timeout); // 3000
```

## Non-Null Assertion Operator

Use the `!` operator when you know a value is not null/undefined:

```typescript
function getLength(str: string | null) {
  // Assert that str is not null
  return str!.length;
}

// Warning: Only use when you're certain the value is not null
```

> **Warning**: This operator removes null checks at compile time but not at runtime. Use sparingly and prefer proper type guards.

## Discriminated Unions

### Using Literal Types for Discrimination

```typescript
type LoadingState = { status: "loading" };
type SuccessState<T> = { status: "success"; data: T };
type ErrorState = { status: "error"; error: Error };

type AsyncState<T> = LoadingState | SuccessState<T> | ErrorState;

function renderState<T>(state: AsyncState<T>) {
  switch (state.status) {
    case "loading":
      return "Loading...";
    case "success":
      return `Data: ${JSON.stringify(state.data)}`;
    case "error":
      return `Error: ${state.error.message}`;
    default:
      // TypeScript ensures all cases are handled
      const _exhaustive: never = state;
      throw new Error(`Unhandled state: ${_exhaustive}`);
  }
}
```

## Exhaustiveness Checking

Ensure all union members are handled:

```typescript
type Color = "red" | "green" | "blue";

function getColorHex(color: Color): string {
  switch (color) {
    case "red":
      return "#FF0000";
    case "green":
      return "#00FF00";
    case "blue":
      return "#0000FF";
    default:
      // TypeScript error if a new Color is added
      const _exhaustive: never = color;
      throw new Error(`Unhandled color: ${_exhaustive}`);
  }
}
```

## Best Practices

1. **Let TypeScript infer** when the type is obvious
2. **Annotate function parameters** explicitly
3. **Annotate return types** for public APIs
4. **Use type guards** instead of type assertions when possible
5. **Prefer nullish coalescing (??)** over OR operator (||) for null checks
6. **Use optional chaining (?.)** for safe property access
7. **Implement exhaustive checks** for discriminated unions
8. **Avoid non-null assertions** unless absolutely necessary

## Common Mistakes

### Mistake 1: Over-Annotating

```typescript
// Bad: Unnecessary annotations
const count: number = 42;
const name: string = "Alice";
const active: boolean = true;

// Good: Let TypeScript infer
const count = 42;
const name = "Alice";
const active = true;
```

### Mistake 2: Not Using Type Guards

```typescript
// Bad: Type assertion
function processValue(value: unknown) {
  const str = value as string;
  console.log(str.toUpperCase()); // May fail at runtime
}

// Good: Type guard
function processValue(value: unknown) {
  if (typeof value === "string") {
    console.log(value.toUpperCase()); // Safe
  }
}
```

### Mistake 3: Confusing || with ??

```typescript
const value = 0;

// Bad: Treats 0 as falsy
const result1 = value || 10; // 10

// Good: Only uses default if null or undefined
const result2 = value ?? 10; // 0
```

## Exercises

1. Write a function that uses type guards to handle different input types
2. Create a discriminated union with exhaustive checking
3. Refactor code to use optional chaining and nullish coalescing
4. Write a custom type guard for a specific interface
5. Create an assertion function for runtime validation

## Next Lesson

Now that you understand type inference and control flow, let's explore **[1.5 Strict Mode Configuration](./05-strict-mode.md)** to learn how to configure TypeScript for maximum type safety.
