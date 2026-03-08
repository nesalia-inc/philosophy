# Module 2: Advanced Patterns

> **Goal**: Master TypeScript's advanced type system features for building flexible, reusable, and type-safe abstractions

This module covers powerful type manipulation features that enable you to create sophisticated types and abstractions while maintaining full type safety.

## Learning Objectives

By the end of this module, you will:

- Write generic functions and classes that work with any type
- Leverage utility types to transform and manipulate types
- Create conditional types that branch based on type properties
- Use template literal types for type-level string manipulation
- Implement type guards for runtime type narrowing
- Create branded and opaque types for domain modeling

## Prerequisites

- Completed **[Module 1: Fundamentals](../01-fundamentals/README.md)**
- Comfortable with basic TypeScript syntax
- Understanding of interfaces and type aliases

## Lessons

### 2.1 Generics
**[→ Lesson](./01-generics.md)**

Learn how to write reusable, type-safe functions and classes that work with any type while maintaining type information.

**Key Topics:**
- Generic functions and type parameters
- Generic classes and interfaces
- Generic constraints
- Type parameter defaults
- Variance in generics

### 2.2 Utility Types
**[→ Lesson](./02-utility-types.md)**

Master TypeScript's built-in utility types for transforming and manipulating types.

**Key Topics:**
- `Partial<T>`, `Required<T>`, `Readonly<T>`
- `Pick<T, K>`, `Omit<T, K>`
- `Record<K, T>`, `Exclude<T, U>`, `Extract<T, U>`
- `ReturnType<T>`, `Parameters<T>`
- Combining utility types

### 2.3 Conditional Types
**[→ Lesson](./03-conditional-types.md)**

Create types that branch based on type properties, enabling powerful type-level logic.

**Key Topics:**
- Conditional type syntax
- Distributive conditional types
- Conditional type inference with `infer`
- Recursive conditional types
- Type-level pattern matching

### 2.4 Template Literal Types
**[→ Lesson](./04-template-literal-types.md)**

Use template literal types for type-level string manipulation and validation.

**Key Topics:**
- Template literal type syntax
- String manipulation at type level
- Intrinsic string types
- Type-safe event handlers
- Type-safe routing

### 2.5 Type Guards and Narrowing
**[→ Lesson](./05-type-guards.md)**

Implement runtime type checks that inform TypeScript's type system.

**Key Topics:**
- `typeof` and `instanceof` type guards
- Custom type guard functions
- Assertion functions
- Discriminated union narrowing
- Exhaustive checking

### 2.6 Branded and Opaque Types
**[→ Lesson](./06-branded-types.md)**

Create nominal types for domain primitives and prevent type confusion.

**Key Topics:**
- Branded types pattern
- Opaque types with type-fest
- Domain primitive modeling
- Type-safe IDs
- Unit-annotated values

## Common Patterns

### Generic Repository Pattern

```typescript
interface Repository<T, ID> {
  findById(id: ID): Promise<T | null>;
  findAll(): Promise<T[]>;
  create(entity: Omit<T, "id">): Promise<T>;
  update(id: ID, updates: Partial<T>): Promise<T>;
  delete(id: ID): Promise<void>;
}
```

### Builder Pattern with Generics

```typescript
class QueryBuilder<T> {
  private filters: Array<(item: T) => boolean> = [];

  where(predicate: (item: T) => boolean): this {
    this.filters.push(predicate);
    return this;
  }

  async execute(): Promise<T[]> {
    // Execute query
    return [];
  }
}
```

### Type-Safe Event Emitter

```typescript
type EventMap = Record<string, any>;

type EventKey<T extends EventMap> = string & keyof T;

interface EventEmitter<T extends EventMap> {
  on<K extends EventKey<T>>(
    event: K,
    handler: (payload: T[K]) => void
  ): this;
  emit<K extends EventKey<T>>(
    event: K,
    payload: T[K]
  ): this;
}
```

## Exercises

After completing these lessons, practice with these exercises:

1. **Generic Function**: Create a `first` function that returns the first element of an array or null
2. **Utility Type Chain**: Combine `Pick`, `Omit`, and `Partial` to create complex type transformations
3. **Conditional Type**: Create a type that extracts promise resolve values
4. **Template Literal**: Create type-safe CSS class names or routing paths
5. **Branded Types**: Refactor code to use branded types for IDs and domain primitives

## Common Pitfalls

- **Over-complicating types**: Keep types as simple as possible while maintaining safety
- **Type instantiation depth**: Deeply recursive types can cause compiler errors
- **Unnecessary generics**: Don't use generics when a simple type would work
- **Forgetting constraints**: Unconstrained generics limit what you can do with type parameters
- **Circular type references**: Can cause infinite loops in type checking

## Next Steps

Once you've mastered advanced patterns, continue to **[Module 3: Functional Programming](../03-functional-programming/README.md)** to learn about Result, Option, Unit, and functional programming patterns.

## Additional Resources

- [TypeScript Generics Documentation](https://www.typescriptlang.org/docs/handbook/2/generics.html)
- [TypeScript Utility Types](https://www.typescriptlang.org/docs/handbook/utility-types.html)
- [TypeScript Conditional Types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)
- [Type Challenges](https://github.com/type-challenges/type-challenges)
