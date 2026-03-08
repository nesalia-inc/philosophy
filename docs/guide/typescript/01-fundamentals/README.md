# Module 1: TypeScript Fundamentals

> **Goal**: Master the foundational concepts of TypeScript's type system and strict mode configuration

This module covers the essential building blocks of TypeScript: primitive types, interfaces, classes, type inference, and the strict mode configuration that forms the foundation of type-safe development.

## Learning Objectives

By the end of this module, you will:

- Understand TypeScript's primitive types and when to use them
- Know the difference between `interface` and `type` and when to choose each
- Master class inheritance and access modifiers
- Leverage type inference to write cleaner code
- Configure strict mode for maximum type safety

## Lessons

### 1.1 Type System Basics
**[→ Lesson](./01-type-system-basics.md)**

Learn about TypeScript's primitive types, special types (`any`, `unknown`, `never`), and how the type system catches errors at compile time.

**Key Topics:**
- Primitive types: `string`, `number`, `boolean`, `bigint`, `symbol`
- Special types: `any`, `unknown`, `never`, `void`
- Arrays and tuples
- Type annotations vs type inference

### 1.2 Interfaces vs Types
**[→ Lesson](./02-interfaces-vs-types.md)**

Understand the differences between interfaces and type aliases, and when to use each for maximum clarity and maintainability.

**Key Topics:**
- Interface declarations and extension
- Type aliases and unions
- Interface merging behavior
- When to choose interface vs type

### 1.3 Classes and Inheritance
**[→ Lesson](./03-classes-and-inheritance.md)**

Master object-oriented programming in TypeScript with classes, inheritance, abstract classes, and access modifiers.

**Key Topics:**
- Class properties and methods
- Constructor parameter properties
- Inheritance and `extends`
- Abstract classes and members
- Access modifiers: `public`, `private`, `protected`, `readonly`

### 1.4 Type Inference and Control Flow
**[→ Lesson](./04-type-inference.md)**

Learn how TypeScript infers types automatically and uses control flow analysis to narrow types.

**Key Topics:**
- Variable declaration inference
- Contextual typing
- Control flow analysis
- Type narrowing with `typeof`, `instanceof`
- Nullish coalescing and optional chaining

### 1.5 Strict Mode Configuration
**[→ Lesson](./05-strict-mode.md)**

Configure TypeScript for maximum type safety with strict mode and additional compiler options.

**Key Topics:**
- The `strict` flag and what it enables
- `noUncheckedIndexedAccess`
- `noImplicitOverride`
- `exactOptionalPropertyTypes`
- Recommended `tsconfig.json` setup

## Exercises

After completing these lessons, practice with these exercises:

1. **Type Annotations**: Create a function with explicit parameter and return types
2. **Interface Extension**: Extend an interface and add new properties
3. **Class Hierarchy**: Create an abstract class with two concrete implementations
4. **Type Narrowing**: Write a function that uses control flow analysis
5. **Strict Migration**: Enable strict flags incrementally and fix resulting errors

## Common Pitfalls

- **Using `any`**: This disables type checking. Use `unknown` instead when the type is truly unknown
- **Assuming `interface === type`**: They have different behaviors (e.g., interface merging)
- **Ignoring strict mode**: Start with strict mode enabled to avoid refactoring later
- **Over-annotating**: TypeScript can infer most types; only annotate where necessary

## Next Steps

Once you've mastered the fundamentals, continue to **[Module 2: Advanced Patterns](../02-advanced-patterns/README.md)** to learn about generics, utility types, and advanced type manipulation.

## Additional Resources

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [Effective TypeScript](https://effectivetypescript.com/)
