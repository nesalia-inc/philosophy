# 1.2 Interfaces vs Types

> **Learning Goal**: Understand the differences between interfaces and type aliases, and when to choose each for maximum clarity and maintainability.

## Overview

TypeScript provides two ways to define object shapes: **interfaces** and **type aliases**. While they often seem interchangeable, they have important differences that affect when you should use each.

## Quick Reference

| Feature | Interface | Type Alias |
|---------|-----------|------------|
| Object shapes | ✅ Yes | ✅ Yes |
| Primitives | ❌ No | ✅ Yes |
| Unions | ❌ No | ✅ Yes |
| Intersections | ❌ No | ✅ Yes |
| Tuples | ❌ No | ✅ Yes |
| Merging | ✅ Yes | ❌ No |
| Extends | ✅ Yes (extends) | ✅ Yes (intersection) |
| Declaration merging | ✅ Yes | ❌ No |
| Error messages | Better | Good |

## When to Use Interface

Use **interfaces** for object shapes that represent:

- Class contracts
- API contracts (public interfaces)
- Objects that might be extended
- Library public APIs (better error messages)

### Basic Interface

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  createdAt: Date;
}

const user: User = {
  id: "123",
  name: "Alice",
  email: "alice@example.com",
  createdAt: new Date(),
};
```

### Interface Extension

```typescript
interface Animal {
  name: string;
  age: number;
}

interface Dog extends Animal {
  breed: string;
  bark(): void;
}

const dog: Dog = {
  name: "Buddy",
  age: 5,
  breed: "Golden Retriever",
  bark() {
    console.log("Woof!");
  },
};
```

### Interface Merging (Declaration Merging)

Interfaces with the same name are automatically merged:

```typescript
interface User {
  id: string;
  name: string;
}

interface User {
  email: string;
  age?: number;
}

// Result: User has id, name, email, and optional age
const user: User = {
  id: "123",
  name: "Alice",
  email: "alice@example.com",
};
```

This is useful for:
- Augmenting external library types
- Splitting large interfaces across files
- Plugin architecture patterns

### Interface for Classes

```typescript
interface Serializable {
  toJSON(): string;
}

class User implements Serializable {
  constructor(
    public id: string,
    public name: string
  ) {}

  toJSON(): string {
    return JSON.stringify({ id: this.id, name: this.name });
  }
}
```

### Interface Methods

```typescript
interface Calculator {
  add(a: number, b: number): number;
  subtract(a: number, b: number): number;
}

// Shorthand method syntax
interface Calculator {
  add: (a: number, b: number) => number;
}
```

## When to Use Type Aliases

Use **type aliases** for:

- Union types
- Intersection types
- Tuples
- Primitive types
- Function types
- Complex type combinations
- Mapped types
- Conditional types

### Union Types

```typescript
type Status = "active" | "inactive" | "pending";

type Result = Success | Error;

interface Success {
  success: true;
  data: unknown;
}

interface Error {
  success: false;
  error: string;
}
```

### Intersection Types

```typescript
type Employee = Person & {
  employeeId: string;
  department: string;
};

interface Person {
  name: string;
  age: number;
}

const employee: Employee = {
  name: "Alice",
  age: 30,
  employeeId: "123",
  department: "Engineering",
};
```

### Tuples

```typescript
type Coordinate = [x: number, y: number];
type KeyValuePair = [key: string, value: unknown];

const coord: Coordinate = [10, 20];
const pair: KeyValuePair = ["name", "Alice"];
```

### Utility Types

```typescript
type PartialUser = Partial<User>; // All properties optional
type UserKeys = keyof User; // "id" | "name" | "email" | "createdAt"
type UserId = User["id"]; // string
```

### Conditional Types

```typescript
type NonNullable<T> = T extends null | undefined ? never : T;

type Result = NonNullable<string | null>; // string
```

### Function Types

```typescript
type EventHandler = (event: Event) => void;
type Mapper<T, U> = (value: T) => U;
type Predicate<T> = (value: T) => boolean;

const handleClick: EventHandler = (event) => {
  console.log(event.type);
};
```

## Key Differences

### 1. Extending

```typescript
// Interface extends using 'extends' keyword
interface Animal {
  name: string;
}

interface Dog extends Animal {
  bark(): void;
}

// Type extends using intersection
type Cat = Animal & {
  meow(): void;
};
```

### 2. Merging

```typescript
// Interfaces merge
interface Box {
  width: number;
}
interface Box {
  height: number;
}
const box: Box = { width: 10, height: 20 }; // OK

// Types do NOT merge - error
type Container = {
  width: number;
};
// Error: Duplicate identifier 'Container'
// type Container = {
//   height: number;
// };
```

### 3. Complex Types

```typescript
// Only type aliases can do this
type StringOrNumber = string | number;
type Tuple = [string, number, boolean];
type Recursive = Tree | Tree[];

// Interfaces can only represent object shapes
interface Tree {
  value: string;
  children?: Tree[];
}
```

## Practical Examples

### Example 1: API Response Types

```typescript
// Good: interface for API contracts
interface User {
  id: string;
  name: string;
  email: string;
}

interface CreateUserResponse {
  success: true;
  user: User;
}

interface CreateErrorResponse {
  success: false;
  error: string;
}

// Good: type for union of responses
type CreateUserResult = CreateUserResponse | CreateErrorResponse;
```

### Example 2: Configuration Types

```typescript
// Good: type for complex configuration
type DatabaseConfig = {
  host: string;
  port: number;
  database: string;
};

type ServerConfig = {
  port: number;
  cors: boolean;
};

// Intersection for combined config
type AppConfig = DatabaseConfig & ServerConfig;

const config: AppConfig = {
  host: "localhost",
  port: 5432,
  database: "mydb",
  port: 3000, // Error: Duplicate property
  cors: true,
};
```

### Example 3: Discriminated Unions

```typescript
// Good: interfaces for each state
interface IdleState {
  status: "idle";
}

interface LoadingState {
  status: "loading";
}

interface SuccessState<T> {
  status: "success";
  data: T;
}

interface ErrorState {
  status: "error";
  error: Error;
}

// Good: type for the union
type AsyncState<T> =
  | IdleState
  | LoadingState
  | SuccessState<T>
  | ErrorState;
```

## Best Practices

1. **Default to interfaces** for object shapes that represent "things"
2. **Use type aliases** for unions, intersections, and utility types
3. **Export interfaces** from your library for better error messages
4. **Use interface merging** only when necessary (can be confusing)
5. **Prefer `interface`** when defining public APIs
6. **Use `type`** for complex type manipulations and transformations

## Decision Tree

```
Need to define a type?
│
├─ Is it a primitive or literal value?
│  └─ Use type alias
│
├─ Is it a union or intersection?
│  └─ Use type alias
│
├─ Is it a tuple?
│  └─ Use type alias
│
├─ Is it a function type?
│  └─ Use type alias
│
├─ Will it be extended by others?
│  └─ Use interface (supports merging)
│
└─ Is it an object shape?
   └─ Prefer interface (better error messages)
```

## Common Mistakes

### Mistake 1: Using Type for Simple Objects

```typescript
// Works but not ideal
type User = {
  id: string;
  name: string;
};

// Better: use interface
interface User {
  id: string;
  name: string;
}
```

### Mistake 2: Trying to Merge Types

```typescript
// Error: Duplicate identifier
type Box = { width: number };
// type Box = { height: number };

// Solution: Use intersection
type Box = { width: number } & { height: number };

// Or use interface
interface Box {
  width: number;
}
interface Box {
  height: number;
}
```

### Mistake 3: Over-complicating with Interface

```typescript
// Unnecessary interface
type Status = "active" | "inactive";

// Bad: interface for union doesn't work
// interface Status = "active" | "inactive"; // Error
```

## Exercises

1. Create an interface for a `Product` with properties like id, name, price, and category
2. Create a type alias for a union of `ProductStatus` that can be "available", "out-of-stock", or "discontinued"
3. Create an intersection type that combines `User` and `Admin` interfaces
4. Write a discriminated union using interfaces for different request states
5. Practice interface merging by extending an external library type

## Next Lesson

Now that you understand interfaces and types, let's explore **[1.3 Classes and Inheritance](./03-classes-and-inheritance.md)** to learn object-oriented programming in TypeScript.
