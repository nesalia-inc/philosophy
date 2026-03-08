# 1.5 Strict Mode Configuration

> **Learning Goal**: Configure TypeScript for maximum type safety with strict mode and additional compiler options.

## Overview

TypeScript's strict mode is a collection of compiler options that enable the strongest type-checking possible. Starting with strict mode enabled is strongly recommended for new projects.

## The Strict Flag

### Enabling Strict Mode

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

This single flag enables all of the following strict options:

- `strictNullChecks`
- `strictFunctionTypes`
- `strictBindCallApply`
- `strictPropertyInitialization`
- `noImplicitAny`
- `noImplicitThis`
- `alwaysStrict`

### What Strict Mode Catches

```typescript
// Before strict mode - compiles but crashes at runtime
function greet(name: string) {
  console.log(`Hello, ${name.toUpperCase()}!`);
}

greet(null); // Runtime error: Cannot read property 'toUpperCase' of null

// After strict mode - compile-time error
function greet(name: string) {
  console.log(`Hello, ${name.toUpperCase()}!`);
}

// Error: Argument of type 'null' is not assignable to parameter of type 'string'
greet(null);
```

## Individual Strict Options

### 1. strictNullChecks

Ensures `null` and `undefined` are only assignable to themselves and `any`.

```typescript
// Without strictNullChecks
let name: string = null; // OK

// With strictNullChecks
let name: string = null; // Error: Type 'null' is not assignable to type 'string'

// Solution: Use union type or Option<T>
let name: string | null = null; // OK
if (name !== null) {
  console.log(name.toUpperCase()); // Safe
}
```

### 2. noImplicitAny

Prevents TypeScript from inferring `any` when it cannot infer a type.

```typescript
// Without noImplicitAny
function getValue(value) { // Parameter 'value' implicitly has an 'any' type
  return value;
}

// With noImplicitAny
function getValue(value) { // Error: Parameter 'value' implicitly has an 'any' type
  return value;
}

// Solution: Add explicit type
function getValue(value: unknown) {
  return value;
}
```

### 3. strictFunctionTypes

Ensures function parameter types are checked contravariantly.

```typescript
// Without strictFunctionTypes
interface Animal {}
interface Dog extends Animal {}

let animalHandler = (animal: Animal) => {};
let dogHandler = (dog: Dog) => {};

animalHandler = dogHandler; // Allowed but unsafe

// With strictFunctionTypes
animalHandler = dogHandler; // Error: Type '(dog: Dog) => void' is not assignable to type '(animal: Animal) => void'
```

### 4. strictBindCallApply

Ensures `call()`, `bind()`, and `apply()` are used with correct argument types.

```typescript
// Without strictBindCallApply
function greet(greeting: string, name: string) {
  console.log(`${greeting}, ${name}!`);
}

greet.call(null, "Hello"); // May fail at runtime

// With strictBindCallApply
greet.call(null, "Hello"); // Error: Expected 2 arguments, but got 1
greet.call(null, "Hello", "World"); // OK
```

### 5. strictPropertyInitialization

Ensures class properties are initialized in the constructor.

```typescript
// Without strictPropertyInitialization
class User {
  name: string; // No error
  email: string;
}

// With strictPropertyInitialization
class User {
  name: string; // Error: Property 'name' has no initializer and is not definitely assigned
  email: string;
}

// Solutions:

// 1. Initialize property
class User {
  name: string = "";
  email: string = "";
}

// 2. Initialize in constructor
class User {
  name: string;
  email: string;

  constructor(name: string, email: string) {
    this.name = name;
    this.email = email;
  }
}

// 3. Use definite assignment assertion
class User {
  name!: string; // I promise to initialize this
  email!: string;

  initialize(name: string, email: string) {
    this.name = name;
    this.email = email;
  }
}

// 4. Mark as optional
class User {
  name?: string;
  email?: string;
}
```

### 6. noImplicitThis

Prevents `this` from being implicitly `any`.

```typescript
// Without noImplicitThis
const obj = {
  value: 42,
  getValue() {
    return this.value; // 'this' implicitly has type 'any'
  }
};

// With noImplicitThis
const obj = {
  value: 42,
  getValue(this: { value: number }) {
    return this.value; // OK
  }
};
```

### 7. alwaysStrict

Ensures JavaScript is always parsed in strict mode (e.g., `"use strict"`).

## Additional Strict Options

### noUncheckedIndexedAccess

Consider accessing an index that may not exist as `undefined`.

```typescript
// Without noUncheckedIndexedAccess
const arr = ["a", "b", "c"];
const value = arr[10]; // Type is string (but actually undefined at runtime)

// With noUncheckedIndexedAccess
const arr = ["a", "b", "c"];
const value = arr[10]; // Type is string | undefined
```

This is crucial for array safety:

```typescript
const numbers = [1, 2, 3];
const first = numbers[0]; // number | undefined

if (first !== undefined) {
  console.log(first.toFixed(2)); // Safe
}
```

### noImplicitOverride

Requires explicit `override` keyword when overriding base class methods.

```typescript
class Parent {
  greet() {
    console.log("Hello from Parent");
  }
}

class Child extends Parent {
  // Without noImplicitOverride
  greet() { // May be accidental
    console.log("Hello from Child");
  }

  // With noImplicitOverride
  override greet() { // Explicit override
    console.log("Hello from Child");
  }
}
```

This catches typos and makes code more maintainable:

```typescript
class Child extends Parent {
  // Error: This method cannot have an 'override' modifier because it is not declared in the base class 'Parent'
  override gret() {
    console.log("Typo!");
  }
}
```

### exactOptionalPropertyTypes

Ensures optional properties are exactly `undefined`, not just missing.

```typescript
interface User {
  name: string;
  email?: string; // Without exactOptionalPropertyTypes: can be missing OR undefined
}

// Without exactOptionalPropertyTypes
const user1: User = { name: "Alice" }; // OK
const user2: User = { name: "Bob", email: undefined }; // OK
user2.email = undefined; // OK

// With exactOptionalPropertyTypes
interface User {
  name: string;
  email?: string;
}

const user1: User = { name: "Alice" }; // OK
const user2: User = { name: "Bob", email: undefined }; // Error

// Correct usage
const user3: User = { name: "Charlie" };
if (user3.email) {
  console.log(user3.email);
}
```

### noPropertyAccessFromIndexSignature

Prevents accessing properties via dot notation when using index signatures.

```typescript
// Without noPropertyAccessFromIndexSignature
interface Config {
  [key: string]: any;
  timeout: number; // Actually index signature, not property
}

const config: Config = { timeout: 1000 };
config.timeout; // Works but misleading

// With noPropertyAccessFromIndexSignature
interface Config {
  [key: string]: any;
}

const config: Config = { timeout: 1000 };
config.timeout; // Error: Property 'timeout' comes from an index signature
config["timeout"]; // OK: Explicit access
```

## Recommended tsconfig.json

```json
{
  "compilerOptions": {
    // Language and Environment
    "target": "ES2022",
    "lib": ["ES2022"],
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "resolveJsonModule": true,

    // Type Checking
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitOverride": true,
    "exactOptionalPropertyTypes": true,
    "noPropertyAccessFromIndexSignature": true,

    // Emit
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "outDir": "./dist",

    // Interop Constraints
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,

    // Completeness
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

## Migration Strategy

### Incremental Strict Mode Migration

When migrating existing codebases, enable strict options incrementally:

**Step 1: Enable strictNullChecks**
```json
{
  "compilerOptions": {
    "strictNullChecks": true
  }
}
```

Fix all null/undefined errors.

**Step 2: Enable noImplicitAny**
```json
{
  "compilerOptions": {
    "strictNullChecks": true,
    "noImplicitAny": true
  }
}
```

Add explicit type annotations.

**Step 3: Enable remaining strict options**
```json
{
  "compilerOptions": {
    "strictNullChecks": true,
    "noImplicitAny": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true
  }
}
```

**Step 4: Enable full strict mode**
```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

## Common Errors and Solutions

### Error: "Object is possibly 'undefined'"

```typescript
// Problem
const arr = [1, 2, 3];
const first = arr[0];
console.log(first * 2); // Error

// Solution 1: Non-null assertion (use sparingly)
console.log(first! * 2);

// Solution 2: Type guard
if (first !== undefined) {
  console.log(first * 2);
}

// Solution 3: Nullish coalescing
console.log((first ?? 0) * 2);
```

### Error: "Property 'X' has no initializer"

```typescript
// Problem
class User {
  name: string;
}

// Solution 1: Initialize in constructor
class User {
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}

// Solution 2: Definite assignment assertion
class User {
  name!: string;
}

// Solution 3: Optional property
class User {
  name?: string;
}
```

### Error: "Parameter 'X' implicitly has an 'any' type"

```typescript
// Problem
function process(value) {
  return value * 2;
}

// Solution
function process(value: number) {
  return value * 2;
}
```

## Best Practices

1. **Start new projects with strict mode enabled**
2. **Enable noUncheckedIndexedAccess** for array safety
3. **Use noImplicitOverride** for better maintainability
4. **Enable exactOptionalPropertyTypes** for precise optional handling
5. **Migrate incrementally** for existing codebases
6. **Never use `any`** to silence strict mode errors
7. **Fix the root cause** rather than suppressing errors
8. **Use type guards** instead of type assertions

## Exercises

1. Create a new tsconfig.json with all strict options enabled
2. Fix strict mode errors in sample code
3. Refactor code to handle `undefined` from indexed access
4. Add `override` keyword to all overridden methods
5. Migrate a small codebase to strict mode incrementally

## Summary

Strict mode is essential for type-safe TypeScript development. It catches entire categories of bugs at compile time rather than runtime:

- **Null/undefined errors** → caught by strictNullChecks
- **Implicit any types** → caught by noImplicitAny
- **Array out of bounds** → caught by noUncheckedIndexedAccess
- **Uninitialized properties** → caught by strictPropertyInitialization
- **Typos in overrides** → caught by noImplicitOverride

Always start with strict mode enabled in new projects.

## Next Steps

Congratulations! You've completed **Module 1: Fundamentals**. Continue to **[Module 2: Advanced Patterns](../02-advanced-patterns/README.md)** to learn about generics, utility types, and advanced type manipulation.
