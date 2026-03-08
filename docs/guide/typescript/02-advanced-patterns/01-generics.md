# 2.1 Generics

> **Learning Goal**: Write reusable, type-safe functions and classes that work with any type while maintaining type information.

## What are Generics?

Generics allow you to write code that works with multiple types while maintaining type safety. Instead of using `any`, generics capture the type information and make it available throughout your code.

### Why Generics?

```typescript
// Bad: Using 'any' loses type information
function parseAny(json: string): any {
  return JSON.parse(json);
}

const data = parseAny('{"name": "Alice"}');
data.name.toUpperCase(); // OK at compile time, may fail at runtime

// Good: Using generic preserves type information
function parse<T>(json: string): T {
  return JSON.parse(json);
}

interface User {
  name: string;
}

const user = parse<User>('{"name": "Alice"}');
user.name.toUpperCase(); // OK: TypeScript knows it's a string
user.age; // Error: Property 'age' does not exist on type 'User'
```

## Generic Functions

### Basic Generic Function

```typescript
function identity<T>(arg: T): T {
  return arg;
}

// TypeScript infers T as string
const str = identity("hello");
str.toUpperCase(); // OK

// TypeScript infers T as number
const num = identity(42);
num.toFixed(2); // OK

// Explicit type argument
const bool = identity<boolean>(true);
bool.toString(); // OK
```

### Multiple Type Parameters

```typescript
function pair<T, U>(first: T, second: U): [T, U] {
  return [first, second];
}

const p1 = pair(1, "one"); // [number, string]
const p2 = pair("hello", true); // [string, boolean]
```

### Generic Constraints

Constrain type parameters to specific shapes:

```typescript
// Constraint: T must have a length property
interface Lengthwise {
  length: number;
}

function logLength<T extends Lengthwise>(arg: T): void {
  console.log(arg.length);
}

logLength("hello"); // OK: string has length
logLength([1, 2, 3]); // OK: array has length
logLength({ length: 10, value: "test" }); // OK: object has length
// logLength(42); // Error: number doesn't have length
```

### Using Type Parameters in Constraints

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = {
  name: "Alice",
  age: 30,
  email: "alice@example.com"
};

const name = getProperty(user, "name"); // string
// getProperty(user, "invalid"); // Error: '"invalid"' is not assignable to parameter of type '"name" | "age" | "email"'
```

### Type Parameter Defaults

```typescript
interface DefaultValue<T = string> {
  value: T;
  defaultValue?: T;
}

const str: DefaultValue = { value: "hello" };
const num: DefaultValue<number> = { value: 42, defaultValue: 0 };
```

## Generic Classes

### Basic Generic Class

```typescript
class Box<T> {
  private _value: T;

  constructor(value: T) {
    this._value = value;
  }

  get value(): T {
    return this._value;
  }

  set value(newValue: T) {
    this._value = newValue;
  }
}

const stringBox = new Box("hello");
stringBox.value.toUpperCase(); // OK

const numberBox = new Box(42);
numberBox.value.toFixed(2); // OK
```

### Generic Class with Multiple Type Parameters

```typescript
class Pair<T, U> {
  constructor(
    public first: T,
    public second: U
  ) {}

  mapFirst<V>(fn: (value: T) => V): Pair<V, U> {
    return new Pair(fn(this.first), this.second);
  }

  mapSecond<V>(fn: (value: U) => V): Pair<T, V> {
    return new Pair(this.first, fn(this.second));
  }
}

const pair = new Pair(1, "one");
const mapped = pair.mapFirst(x => x * 2); // Pair<number, string>
```

### Generic Interface

```typescript
interface Result<T, E> {
  success: boolean;
  data?: T;
  error?: E;
}

function createSuccess<T>(data: T): Result<T, never> {
  return { success: true, data };
}

function createError<E>(error: E): Result<never, E> {
  return { success: false, error };
}
```

## Generic Utility Functions

### `first` - Get First Element

```typescript
function first<T>(arr: readonly T[]): T | undefined {
  return arr[0];
}

const numbers = [1, 2, 3];
const n = first(numbers); // number | undefined

if (n !== undefined) {
  console.log(n.toFixed(2)); // OK
}
```

### `pick` - Pick Properties

```typescript
function pick<T, K extends keyof T>(obj: T, keys: K[]): Pick<T, K> {
  const result = {} as Pick<T, K>;
  for (const key of keys) {
    result[key] = obj[key];
  }
  return result;
}

const user = {
  name: "Alice",
  age: 30,
  email: "alice@example.com"
};

const partial = pick(user, ["name", "age"]);
// partial: { name: string; age: number }
```

### `mapValues` - Transform Object Values

```typescript
function mapValues<T, U>(
  obj: Record<string, T>,
  fn: (value: T, key: string) => U
): Record<string, U> {
  const result: Record<string, U> = {};
  for (const [key, value] of Object.entries(obj)) {
    result[key] = fn(value as T, key);
  }
  return result;
}

const data = {
  a: 1,
  b: 2,
  c: 3
};

const doubled = mapValues(data, x => x * 2);
// doubled: { a: number; b: number; c: number }
```

## Generic Constraints Patterns

### Constraint to Constructor

```typescript
interface Constructor<T> {
  new (...args: any[]): T;
}

function createInstance<T>(
  ctor: Constructor<T>,
  ...args: any[]
): T {
  return new ctor(...args);
}

class User {
  constructor(public name: string) {}
}

const user = createInstance(User, "Alice");
user.name; // string
```

### Constraint to Array-Like

```typescript
interface ArrayLike<T> {
  length: number;
  [n: number]: T;
}

function getFirst<T>(arr: ArrayLike<T>): T | undefined {
  return arr[0];
}

getFirst("hello"); // string | undefined
getFirst([1, 2, 3]); // number | undefined
getFirst({ length: 2, 0: "a", 1: "b" }); // string | undefined
```

### Constraint to Function

```typescript
interface Function {
  (...args: any[]): any;
}

function callFunction<F extends Function>(
  fn: F,
  ...args: Parameters<F>
): ReturnType<F> {
  return fn(...args);
}

function add(a: number, b: number): number {
  return a + b;
}

const result = callFunction(add, 1, 2); // number
```

## Variance in Generics

### Covariance (Arrays)

```typescript
class Animal {}
class Dog extends Animal {}

const dogs: Dog[] = [new Dog()];
const animals: Animal[] = dogs; // OK: Arrays are covariant
animals.push(new Animal()); // Problem: can add non-Dog to dogs array
```

### Invariance (Generic Classes)

```typescript
class Box<T> {
  constructor(public value: T) {}
}

const dogBox = new Box(new Dog());
// const animalBox: Box<Animal> = dogBox; // Error: Box is invariant
```

### Bivariance (Functions)

```typescript
type Handler = (arg: Animal) => void;

const dogHandler: (arg: Dog) => void = (dog: Dog) => {};
const handler: Handler = dogHandler; // OK: Functions are bivariant (unsafe but practical)
```

## Common Generic Patterns

### Repository Pattern

```typescript
interface Entity {
  id: string;
}

interface Repository<T extends Entity> {
  findById(id: string): Promise<T | null>;
  findAll(): Promise<T[]>;
  create(entity: Omit<T, "id">): Promise<T>;
  update(id: string, updates: Partial<T>): Promise<T>;
  delete(id: string): Promise<void>;
}

class UserRepository implements Repository<User> {
  async findById(id: string): Promise<User | null> {
    // Implementation
    return null;
  }

  // ... other methods
}
```

### Builder Pattern

```typescript
class QueryBuilder<T> {
  private conditions: Array<(item: T) => boolean> = [];

  where(predicate: (item: T) => boolean): this {
    this.conditions.push(predicate);
    return this;
  }

  async execute(): Promise<T[]> {
    // Implementation
    return [];
  }
}

class UserQueryBuilder extends QueryBuilder<User> {
  active(): this {
    return this.where(user => user.isActive);
  }

  named(name: string): this {
    return this.where(user => user.name === name);
  }
}
```

## Best Practices

1. **Use descriptive type parameter names**:
   - `T` for Type
   - `K` for Key
   - `V` for Value
   - `E` for Event/Error
   - `R` for Return type

2. **Constrain type parameters** when possible:
   ```typescript
   // Good
   function logLength<T extends { length: number }>(arg: T) {
     console.log(arg.length);
   }

   // Bad: Too unconstrained
   function logLength<T>(arg: T) {
     console.log((arg as any).length);
   }
   ```

3. **Don't use generics unnecessarily**:
   ```typescript
   // Bad: Generic adds no value
   function echo<T>(value: T): T {
     return value;
   }

   // Good: Simple type is sufficient
   function echo(value: string): string {
     return value;
   }
   ```

4. **Prefer type inference** over explicit type arguments:
   ```typescript
   // Good: Let TypeScript infer
   const result = identity(42);

   // Only use explicit when necessary
   const result = identity<number>("42"); // Would error anyway
   ```

## Common Mistakes

### Mistake 1: Unconstrained Generics

```typescript
// Bad: Can't access length property
function getLength<T>(arg: T): number {
  // Error: Property 'length' does not exist on type 'T'
  // return arg.length;
  return 0;
}

// Good: Add constraint
function getLength<T extends { length: number }>(arg: T): number {
  return arg.length;
}
```

### Mistake 2: Over-Complicating Types

```typescript
// Bad: Too complex
function transform<T, U, V>(
  value: T,
  fn1: (v: T) => U,
  fn2: (v: U) => V
): V {
  return fn2(fn1(value));
}

// Good: Simplify
function transform<T, U>(
  value: T,
  fn: (v: T) => U
): U {
  return fn(value);
}
```

## Exercises

1. Create a generic `last` function that returns the last element of an array or undefined
2. Create a generic `map` function that works with arrays
3. Create a generic `filter` function that filters arrays based on a predicate
4. Create a generic `Repository` interface for database operations
5. Create a generic `Result<T, E>` type for error handling

## Next Lesson

Now that you understand generics, let's explore **[2.2 Utility Types](./02-utility-types.md)** to learn about TypeScript's built-in utility types for transforming types.
