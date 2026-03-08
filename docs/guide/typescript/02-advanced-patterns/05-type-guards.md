# 2.5 Type Guards and Narrowing

> **Learning Goal**: Implement runtime type checks that inform TypeScript's type system for safer, more predictable code.

## What are Type Guards?

Type guards are runtime checks that verify the type of a value, allowing TypeScript to narrow types in conditional blocks. This enables type-safe code that works with unions and unknown types.

## Built-in Type Guards

### `typeof` Type Guard

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

Supported `typeof` checks:
- `"string"` - checks for string
- `"number"` - checks for number (including NaN)
- `"boolean"` - checks for boolean
- `"undefined"` - checks for undefined
- `"object"` - checks for object (not null)
- `"function"` - checks for function
- `"symbol"` - checks for symbol
- `"bigint"` - checks for bigint

### `instanceof` Type Guard

```typescript
class Dog {
  bark() {
    console.log("Woof!");
  }
}

class Cat {
  meow() {
    console.log("Meow!");
  }
}

function makeSound(animal: Dog | Cat) {
  if (animal instanceof Dog) {
    animal.bark(); // OK: TypeScript knows it's Dog
  } else {
    animal.meow(); // OK: TypeScript knows it's Cat
  }
}
```

### `in` Operator Type Guard

```typescript
interface Bird {
  fly(): void;
  layEggs(): void;
}

interface Fish {
  swim(): void;
  layEggs(): void;
}

function move(animal: Bird | Fish) {
  if ("fly" in animal) {
    animal.fly(); // OK: TypeScript knows it's Bird
  } else {
    animal.swim(); // OK: TypeScript knows it's Fish
  }
}
```

## Custom Type Guards

### Type Predicate Functions

Create custom type guards using `parameterName is Type`:

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
    pet.swim(); // OK: TypeScript knows it's Fish
  } else {
    pet.fly(); // OK: TypeScript knows it's Bird
  }
}
```

### Type Guard with Validation

```typescript
interface User {
  name: string;
  email: string;
}

function isUser(obj: unknown): obj is User {
  return (
    typeof obj === "object" &&
    obj !== null &&
    "name" in obj &&
    typeof obj.name === "string" &&
    "email" in obj &&
    typeof obj.email === "string"
  );
}

function processValue(value: unknown) {
  if (isUser(value)) {
    console.log(value.name); // OK: TypeScript knows it's User
  }
}
```

### Generic Type Guard

```typescript
function isDefined<T>(value: T | undefined | null): value is T {
  return value !== undefined && value !== null;
}

const values: (string | undefined)[] = ["a", "b", undefined, "c"];

const definedValues = values.filter(isDefined);
// Type: string[]

function isString(value: unknown): value is string {
  return typeof value === "string";
}

const mixed: unknown[] = ["hello", 42, true, "world"];

const strings = mixed.filter(isString);
// Type: string[]
```

## Assertion Functions

Assertion functions are functions that assert a condition and throw if it's false. They narrow types after being called.

### Basic Assertion Function

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

### Assertion Function for Condition

```typescript
function assert(condition: boolean, message: string): asserts condition {
  if (!condition) {
    throw new Error(message);
  }
}

function divide(a: number, b: number): number {
  assert(b !== 0, "Division by zero");
  return a / b; // TypeScript knows b is not 0
}
```

### Assertion Function for Defined Values

```typescript
function assertDefined<T>(value: T | null | undefined, message?: string): asserts value is T {
  if (value === undefined || value === null) {
    throw new Error(message ?? "Value is undefined or null");
  }
}

function getFirstElement<T>(arr: T[]): T | undefined {
  return arr[0];
}

const numbers: number[] = [1, 2, 3];
const first = getFirstElement(numbers);

assertDefined(first, "Array is empty");
console.log(first.toFixed(2)); // OK: TypeScript knows first is number
```

## Discriminated Unions

Discriminated unions use a common property to distinguish between types:

### Basic Discriminated Union

```typescript
interface LoadingState {
  status: "loading";
}

interface SuccessState<T> {
  status: "success";
  data: T;
}

interface ErrorState {
  status: "error";
  error: string;
}

type AsyncState<T> = LoadingState | SuccessState<T> | ErrorState;

function renderState<T>(state: AsyncState<T>) {
  switch (state.status) {
    case "loading":
      return "Loading...";
    case "success":
      return `Data: ${JSON.stringify(state.data)}`;
    case "error":
      return `Error: ${state.error}`;
  }
}
```

### Exhaustive Checking

Ensure all union members are handled:

```typescript
function renderState<T>(state: AsyncState<T>): string {
  switch (state.status) {
    case "loading":
      return "Loading...";
    case "success":
      return `Data: ${JSON.stringify(state.data)}`;
    case "error":
      return `Error: ${state.error}`;
    default:
      // TypeScript error if a new status is added
      const _exhaustive: never = state;
      throw new Error(`Unhandled state: ${_exhaustive}`);
  }
}
```

### Discriminated Union with Methods

```typescript
interface Circle {
  kind: "circle";
  radius: number;
  area(): number;
}

interface Rectangle {
  kind: "rectangle";
  width: number;
  height: number;
  area(): number;
}

type Shape = Circle | Rectangle;

function getArea(shape: Shape): number {
  switch (shape.kind) {
    case "circle":
      return Math.PI * shape.radius ** 2;
    case "rectangle":
      return shape.width * shape.height;
  }
}
```

## Narrowing with Control Flow

### Truthiness Narrowing

```typescript
function processValue(value: string | null | undefined) {
  if (value) {
    // TypeScript knows 'value' is string here
    console.log(value.toUpperCase());
  }
}
```

### Equality Narrowing

```typescript
function processValue(value: string | number) {
  if (value === "hello") {
    // TypeScript knows 'value' is string here
    console.log(value.toUpperCase());
  }
}
```

### Logical Operators

```typescript
function processValue(value: string | null) {
  // Using && operator
  value && console.log(value.toUpperCase());

  // Using ?? operator
  const display = value ?? "default";
  console.log(display);
}
```

### Assignment Narrowing

```typescript
let value: string | number;

value = "hello";
console.log(value.toUpperCase()); // OK: TypeScript knows it's string

value = 42;
console.log(value.toFixed(2)); // OK: TypeScript knows it's number
```

## Advanced Type Guard Patterns

### Type Guard for Array Elements

```typescript
function isArrayOf<T>(
  value: unknown,
  guard: (item: unknown) => item is T
): value is T[] {
  return Array.isArray(value) && value.every(guard);
}

function isString(value: unknown): value is string {
  return typeof value === "string";
}

const data: unknown = ["a", "b", "c"];

if (isArrayOf(data, isString)) {
  data.forEach((s) => console.log(s.toUpperCase())); // OK
}
```

### Type Guard for Object Shape

```typescript
function hasProperties<T extends string>(
  obj: unknown,
  properties: T[]
): obj is Record<T, unknown> {
  return (
    typeof obj === "object" &&
    obj !== null &&
    properties.every((prop) => prop in obj)
  );
}

const data: unknown = { name: "Alice", age: 30 };

if (hasProperties(data, ["name", "age"])) {
  console.log(data.name); // OK: TypeScript knows it exists
}
```

### Type Guard Constructor

```typescript
interface Constructor<T> {
  new (...args: any[]): T;
}

function isInstanceOf<T>(
  value: unknown,
  constructor: Constructor<T>
): value is T {
  return value instanceof constructor;
}

class User {
  constructor(public name: string) {}
}

const value: unknown = new User("Alice");

if (isInstanceOf(value, User)) {
  console.log(value.name); // OK
}
```

## Practical Examples

### Safe Property Access

```typescript
function getDeepProperty<T, K extends keyof T>(
  obj: T,
  ...path: (string | number)[]
): T[K] | undefined {
  let current: any = obj;

  for (const key of path) {
    if (current == null) return undefined;
    if (typeof key === "string" && key in current) {
      current = current[key];
    } else if (typeof key === "number" && Array.isArray(current)) {
      current = current[key];
    } else {
      return undefined;
    }
  }

  return current;
}

interface User {
  name: string;
  address: {
    street: string;
    city: string;
  };
}

const user: User = {
  name: "Alice",
  address: {
    street: "123 Main St",
    city: "New York"
  }
};

const city = getDeepProperty(user, "address", "city"); // string | undefined
```

### Type Guard for API Responses

```typescript
type ApiSuccess<T> = {
  success: true;
  data: T;
};

type ApiError = {
  success: false;
  error: string;
};

type ApiResponse<T> = ApiSuccess<T> | ApiError;

function isSuccess<T>(response: ApiResponse<T>): response is ApiSuccess<T> {
  return response.success;
}

function isApiError(response: ApiResponse<unknown>): response is ApiError {
  return !response.success;
}

async function fetchUser(id: string): Promise<User> {
  const response: ApiResponse<User> = await fetch(`/api/users/${id}`).then(r => r.json());

  if (isSuccess(response)) {
    return response.data;
  } else {
    throw new Error(response.error);
  }
}
```

### Type Guard for Form Validation

```typescript
type FormErrors<T> = {
  [K in keyof T]?: string[];
};

function validateForm<T extends Record<string, any>>(
  data: T,
  rules: {
    [K in keyof T]?: (value: T[K]) => string | undefined;
  }
): FormErrors<T> | null {
  const errors: FormErrors<T> = {};

  for (const key in rules) {
    const rule = rules[key];
    if (rule) {
      const error = rule(data[key]);
      if (error) {
        errors[key] = [error];
      }
    }
  }

  return Object.keys(errors).length > 0 ? errors : null;
}

interface LoginForm {
  email: string;
  password: string;
}

function isValidEmail(value: string): string | undefined {
  if (!value.includes("@")) {
    return "Invalid email";
  }
}

function isValidPassword(value: string): string | undefined {
  if (value.length < 8) {
    return "Password too short";
  }
}

function validateLoginForm(data: LoginForm) {
  return validateForm(data, {
    email: isValidEmail,
    password: isValidPassword
  });
}
```

## Best Practices

1. **Prefer type guards over type assertions**:
   ```typescript
   // Good: Type guard
   if (isString(value)) {
     console.log(value.toUpperCase());
   }

   // Bad: Type assertion
   console.log((value as string).toUpperCase());
   ```

2. **Use assertion functions for invariants**:
   ```typescript
   function assert(condition: boolean, message: string): asserts condition {
     if (!condition) {
       throw new Error(message);
     }
   }
   ```

3. **Use discriminated unions for state machines**:
   ```typescript
   type State =
     | { type: "idle" }
     | { type: "loading" }
     | { type: "success"; data: any }
     | { type: "error"; error: Error };
   ```

4. **Implement exhaustive checks** for discriminated unions:
   ```typescript
   default: {
     const _exhaustive: never = state;
     throw new Error(`Unhandled state: ${_exhaustive}`);
   }
   ```

## Common Mistakes

### Mistake 1: Using Type Assertions Instead of Guards

```typescript
// Bad: Unsafe type assertion
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

### Mistake 2: Not Narrowing After Guard

```typescript
// Bad: Not using the guard
function processValue(value: string | number) {
  if (typeof value === "string") {
    // ...
  }
  console.log(value.toFixed(2)); // Error: might be string
}

// Good: Use else branch
function processValue(value: string | number) {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else {
    console.log(value.toFixed(2));
  }
}
```

### Mistake 3: Forgetting to Check for null

```typescript
// Bad: Might be null
function processValue(value: string | null) {
  console.log(value.toUpperCase()); // Error
}

// Good: Check for null
function processValue(value: string | null) {
  if (value !== null) {
    console.log(value.toUpperCase()); // Safe
  }
}
```

## Exercises

1. Create a type guard for validating email addresses
2. Create an assertion function for checking if a value is defined
3. Implement a discriminated union for different request states
4. Create a type guard for validating user objects
5. Implement exhaustive checking for a complex discriminated union

## Next Lesson

Now that you understand type guards and narrowing, let's explore **[2.6 Branded and Opaque Types](./06-branded-types.md)** to learn about creating nominal types for domain modeling.
