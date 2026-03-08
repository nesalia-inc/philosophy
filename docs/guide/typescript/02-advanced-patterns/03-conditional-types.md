# 2.3 Conditional Types

> **Learning Goal**: Create types that branch based on type properties, enabling powerful type-level logic and transformations.

## What are Conditional Types?

Conditional types allow you to choose a type based on a condition, similar to an `if` statement but at the type level. They follow the syntax:

```typescript
T extends U ? X : Y
```

If type `T` is assignable to type `U`, the result is `X`; otherwise, the result is `Y`.

## Basic Conditional Types

### Simple Condition

```typescript
type IsString<T> = T extends string ? true : false;

type Test1 = IsString<string>; // true
type Test2 = IsString<number>; // false
type Test3 = IsString<boolean>; // false
```

### TypeName Utility

```typescript
type TypeName<T> = T extends string
  ? "string"
  : T extends number
    ? "number"
    : T extends boolean
      ? "boolean"
      : T extends undefined
        ? "undefined"
        : T extends Function
          ? "function"
          : "object";

type T1 = TypeName<string>; // "string"
type T2 = TypeName<number[]>; // "object"
type T3 = TypeName<() => void>; // "function"
type T4 = TypeName<boolean>; // "boolean"
```

### NonNullable Implementation

```typescript
type NonNullable<T> = T extends null | undefined ? never : T;

type T1 = NonNullable<string | null>; // string
type T2 = NonNullable<number | undefined>; // number
type T3 = NonNullable<null | undefined>; // never
```

## Distributive Conditional Types

Conditional types are distributive over union types. When the checked type is a union, the conditional type is applied to each member of the union.

### How Distribution Works

```typescript
type ToArray<T> = T extends any ? T[] : never;

type StrArr = ToArray<string>; // string[]
type NumArr = ToArray<number>; // number[]

// Distributive: applies to each union member
type StrOrNumArr = ToArray<string | number>;
// Result: string[] | number[]
```

### Filter with Conditional Types

```typescript
// Exclude null and undefined
type Nullable<T> = T | null | undefined;

type NonNullable<T> = T extends null | undefined ? never : T;

type T1 = NonNullable<string | null>; // string
type T2 = NonNullable<number | undefined | boolean>; // number | boolean
```

### Extract Members

```typescript
// Extract only functions from union
type OnlyFunctions<T> = T extends (...args: any[]) => any ? T : never;

type Mixed = string | number | ((x: number) => string) | ((x: string) => boolean);
type FunctionsOnly = OnlyFunctions<Mixed>;
// Result: ((x: number) => string) | ((x: string) => boolean)
```

## Type Inference with `infer`

The `infer` keyword introduces a type variable that TypeScript infers from the type being checked.

### Extract Return Type

```typescript
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

function getUser() {
  return { id: 1, name: "Alice" };
}

function getNumbers() {
  return [1, 2, 3];
}

type User = ReturnType<typeof getUser>; // { id: number; name: string }
type Numbers = ReturnType<typeof getNumbers>; // number[]
```

### Extract Array Element Type

```typescript
type ElementType<T> = T extends (infer U)[] ? U : never;

type T1 = ElementType<string[]>; // string
type T2 = ElementType<number[]>; // number
type T3 = ElementType<Array<boolean>>; // boolean
```

### Extract Promise Resolve Type

```typescript
type PromiseType<T> = T extends Promise<infer U> ? U : never;

async function fetchUser() {
  return { id: 1, name: "Alice" };
}

type User = PromiseType<ReturnType<typeof fetchUser>>;
// Result: { id: number; name: string }
```

### Extract Function Parameters

```typescript
type Parameters<T> = T extends (...args: infer P) => any ? P : never;

function foo(a: string, b: number, c: boolean) {
  // ...
}

type FooParams = Parameters<typeof foo>;
// Result: [a: string, b: number, c: boolean]
```

### Extract Second Parameter

```typescript
type SecondParam<T> = T extends (first: any, second: infer S, ...rest: any[]) => any
  ? S
  : never;

function example(a: string, b: number, c: boolean) {
  // ...
}

type Second = SecondParam<typeof example>; // number
```

### Extract This Parameter

```typescript
type ThisParameterType<T> = T extends (this: infer U, ...args: any[]) => any
  ? U
  : unknown;

function toHex(this: Number): string {
  return this.toString(16);
}

type ToHexThis = ThisParameterType<typeof toHex>; // Number
```

## Advanced Conditional Type Patterns

### Flatten Arrays

```typescript
type Flatten<T> = T extends any[] ? T[number] : T;

type T1 = Flatten<string[]>; // string
type T2 = Flatten<number[][]>; // number[]
type T3 = Flatten<boolean>; // boolean
```

### Deep Flatten

```typescript
type DeepFlatten<T> = T extends any[]
  ? T[number] extends infer U
    ? DeepFlatten<U>
    : never
  : T;

type T1 = DeepFlatten<number[][][]>; // number
type T2 = DeepFlatten<string[][][]>; // string
```

### Promisify

Convert a callback-based function to a Promise-based one:

```typescript
type Promisify<T> = T extends (callback: (err: Error, result: infer R) => void) => void
  ? () => Promise<R>
  : T;

// Example usage
function fetchUser(callback: (err: Error, user: User) => void): void {
  // Implementation
}

type FetchUserPromise = Promisify<typeof fetchUser>;
// Result: () => Promise<User>
```

### Equal Types Check

```typescript
type Equal<X, Y> =
  (<T>() => T extends X ? 1 : 2) extends
  (<T>() => T extends Y ? 1 : 2)
    ? true
    : false;

type T1 = Equal<string, string>; // true
type T2 = Equal<string, number>; // false
type T3 = Equal<{ a: string }, { a: string }>; // true
```

### Nullable Properties

Make properties nullable if they were originally optional:

```typescript
type Nullable<T> = {
  [P in keyof T]: T[P] | null;
};

type OptionalNullable<T> = {
  [P in keyof T]+?: T[P] | null;
};

interface User {
  name: string;
  age?: number;
}

type NullableUser = Nullable<User>;
// { name: string | null; age: number | null }

type OptionalNullableUser = OptionalNullable<User>;
// { name?: string | null; age?: number | null }
```

## Recursive Conditional Types

Conditional types can be recursive, allowing for complex type-level computations.

### Deep Readonly

```typescript
type DeepReadonly<T> = T extends (...args: any[]) => any
  ? T
  : T extends object
    ? { readonly [P in keyof T]: DeepReadonly<T[P]> }
    : T;

interface Config {
  server: {
    host: string;
    port: number;
  };
  database: {
    url: string;
  };
}

type ReadonlyConfig = DeepReadonly<Config>;
// All nested properties are readonly
```

### Deep Partial

```typescript
type DeepPartial<T> = T extends (...args: any[]) => any
  ? T
  : T extends object
    ? { [P in keyof T]?: DeepPartial<T[P]> }
    : T;
```

### Deep Required

```typescript
type DeepRequired<T> = T extends (...args: any[]) => any
  ? T
  : T extends object
    ? { [P in keyof T]-?: DeepRequired<T[P]> }
    : T;
```

### Union to Intersection

Convert a union type to an intersection type:

```typescript
type UnionToIntersection<U> =
  (U extends any ? (k: U) => void : never) extends (k: infer I) => void
    ? I
    : never;

type T1 = UnionToIntersection<{ a: string } | { b: number }>;
// Result: { a: string } & { b: number }
```

### Union to Tuple

Convert a union type to a tuple type:

```typescript
type UnionToIntersection<U> =
  (U extends any ? (k: U) => void : never) extends (k: infer I) => void ? I : never;

type LastOf<T> = UnionToIntersection<T extends any ? () => T : never> extends () => infer R
  ? R
  : never;

type Push<T extends any[], V> = [...T, V];

type UnionToTuple<T, L = LastOf<T>, Acc extends any[] = []> =
  [T] extends [never]
    ? Acc
    : UnionToTuple<Exclude<T, L>, LastOf<Exclude<T, L>>, Push<Acc, L>>;

type T1 = UnionToTuple<"a" | "b" | "c">;
// Result: ["a", "b", "c"] (order may vary)
```

## Type-Level Pattern Matching

### Extract Route Parameters

```typescript
type ExtractRouteParams<T> = T extends `${infer Start}:${infer Param}/${infer Rest}`
  ? { [K in Param | keyof ExtractRouteParams<`/${Rest}`>]: string }
  : T extends `${infer Start}:${infer Param}`
    ? { [K in Param]: string }
    : {};

type T1 = ExtractRouteParams<"/users/:id">;
// Result: { id: string }

type T2 = ExtractRouteParams<"/users/:userId/posts/:postId">;
// Result: { userId: string; postId: string }
```

### Split String

```typescript
type Split<S, Sep extends string> =
  S extends `${infer Head}${Sep}${infer Tail}`
    ? [Head, ...Split<Tail, Sep>]
    : [S];

type T1 = Split<"a,b,c", ",">;
// Result: ["a", "b", "c"]

type T2 = Split<"hello-world", "-">;
// Result: ["hello", "world"]
```

### Replace String

```typescript
type Replace<S, From extends string, To extends string> =
  From extends ""
    ? S
    : S extends `${infer Head}${From}${infer Tail}`
      ? `${Head}${To}${Replace<Tail, From, To>}`
      : S;

type T1 = Replace<"hello world", " ", "-">;
// Result: "hello-world"

type T2 = Replace<"a.b.c", ".", "/">;
// Result: "a/b/c"
```

## Practical Examples

### ApiResponse Type

```typescript
type ApiResponseType<T> = T extends { success: true }
  ? { data: T["data"] }
  : { error: T["error"] };

type SuccessResponse = { success: true; data: { id: number; name: string } };
type ErrorResponse = { success: false; error: string };

type T1 = ApiResponseType<SuccessResponse>;
// Result: { data: { id: number; name: string } }

type T2 = ApiResponseType<ErrorResponse>;
// Result: { error: string }
```

### Function Overload Resolution

```typescript
type Overload<T, U> = T extends { (x: infer A): infer R }
  ? U extends { (x: infer A): infer R }
    ? (x: A | U extends (...args: any[]) => any ? any : never) => R
    : never
  : never;

// Simplified example
function process(value: string): string;
function process(value: number): number;
function process(value: string | number): string | number {
  return value;
}
```

### Event Handler Types

```typescript
type EventHandler<T extends { type: string }> = T extends { type: infer K }
  ? K extends string
    ? `on${Capitalize<K>}`
    : never
  : never;

type ClickEvent = { type: "click"; x: number; y: number };
type FocusEvent = { type: "focus" };

type ClickHandler = EventHandler<ClickEvent>;
// Result: "onClick"

type FocusHandler = EventHandler<FocusEvent>;
// Result: "onFocus"
```

## Best Practices

1. **Use `infer` for type extraction** instead of hardcoding types:
   ```typescript
   // Good
   type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

   // Bad
   type ReturnType<T> = any;
   ```

2. **Keep conditional types simple** when possible:
   ```typescript
   // Good: Simple and clear
   type IsString<T> = T extends string ? true : false;

   // Avoid: Overly complex
   type Complex<T> = T extends { a: infer A } ? A extends string ? true : false : false;
   ```

3. **Use distributive types** for union manipulation:
   ```typescript
   // Good: Leverages distribution
   type NonNullable<T> = T extends null | undefined ? never : T;
   ```

4. **Document complex conditional types** with comments:
   ```typescript
   /**
    * Extracts the type of promise resolve value
    * @example PromiseType<Promise<string>> = string
    */
   type PromiseType<T> = T extends Promise<infer U> ? U : never;
   ```

## Common Mistakes

### Mistake 1: Not Understanding Distribution

```typescript
// Union distribution
type ToArray<T> = T extends any ? T[] : never;

type StrOrNumArr = ToArray<string | number>;
// Result: string[] | number[] (not (string | number)[])

// To get (string | number)[], wrap in tuple
type ToArrayNonDistributive<T> = [T] extends [any] ? T[] : never;
type StrOrNumArr2 = ToArrayNonDistributive<string | number>;
// Result: (string | number)[]
```

### Mistake 2: Infinite Recursion

```typescript
// Bad: Never terminates
type Infinite<T> = T extends object ? { [K in keyof T]: Infinite<T[K]> } : T;

// Good: Limit recursion depth
type Finite<T, Depth extends number = 10> =
  Depth extends 0
    ? T
    : T extends object
      ? { [K in keyof T]: Finite<T[K], [-1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9][Depth]> }
      : T;
```

### Mistake 3: Over-Complicating Types

```typescript
// Bad: Hard to understand and maintain
type Complex<T> = T extends { a: infer A } ? A extends string
  ? T extends { b: infer B } ? B extends number ? [A, B] : never : never
  : never : never;

// Good: Break into smaller, named types
type HasStringA<T> = T extends { a: infer A } ? A extends string ? A : never : never;
type HasNumberB<T> = T extends { b: infer B } ? B extends number ? B : never : never;
type Complex<T> = [HasStringA<T>, HasNumberB<T>];
```

## Exercises

1. Create a `DeepNullable<T>` utility type that makes all properties nullable
2. Create a `PromiseReturnType<T>` that extracts the return type of an async function
3. Create a `ReplaceAll<S, From, To>` type that replaces all occurrences
4. Create a `GetOptional<T>` type that extracts only optional properties
5. Create a `FlattenDeep<T>` type that flattens nested arrays

## Next Lesson

Now that you understand conditional types, let's explore **[2.4 Template Literal Types](./04-template-literal-types.md)** to learn about type-level string manipulation.
