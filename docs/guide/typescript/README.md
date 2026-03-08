# TypeScript Guide

> A comprehensive course on modern TypeScript for type-safe, maintainable development

This guide provides a complete foundation for writing robust, maintainable TypeScript code with a focus on type safety, functional programming patterns, and AI-assisted development workflows.

## Course Philosophy

This guide follows these core principles:

- **Eliminate `null`/`undefined`** using `Option<T>`
- **Eliminate exceptions** using `Result<T, E>`
- **Eliminate `void`** using `Unit`
- **Make illegal states unrepresentable**
- **Encode business logic in the type system**
- **Types first, implementation second**

## Prerequisites

- Basic JavaScript knowledge
- Familiarity with ES6+ features
- Understanding of functional programming concepts (helpful but not required)

## Table of Contents

### Module 1: Fundamentals
**[Start here →](./01-fundamentals/README.md)**

- Type System Basics
- Interfaces vs Types
- Classes and Inheritance
- Type Inference
- Strict Mode Configuration

### Module 2: Advanced Patterns
**[Continue here →](./02-advanced-patterns/README.md)**

- Generics
- Utility Types
- Conditional Types
- Template Literal Types
- Type Guards and Narrowing
- Branded and Opaque Types

### Module 3: Functional Programming
**[Continue here →](./03-functional-programming/README.md)**

- Result<T, E> Pattern
- Option<T> Monad
- Unit Type
- Immutability and Readonly
- Function Composition

### Module 4: Error Handling
**[Continue here →](./04-error-handling/README.md)**

- Never Throw Philosophy
- Error Type Discrimination
- Result Chaining
- Async Error Handling
- Exhaustive Switch Patterns

### Module 5: Runtime Validation
**[Continue here →](./05-validation/README.md)**

- Zod Fundamentals
- Schema Composition
- Type Inference from Schemas
- Parse vs SafeParse
- Transform and Refine

### Module 6: Configuration
**[Continue here →](./06-configuration/README.md)**

- Environment Variables
- Config Schema Validation
- Type-Safe Config Objects
- Development vs Production

### Module 7: Performance
**[Continue here →](./07-performance/README.md)**

- Type Checking Performance
- Build Performance
- Monorepo Optimization
- Type Instantiation Depth
- Incremental Compilation

### Module 8: Testing
**[Continue here →](./08-testing/README.md)**

- Type Testing with Vitest
- Testing TypeScript Code
- Contract Testing
- Type-Driven Development

## Quick Reference

### Type-First Development Workflow

1. **Define the data model** - types, interfaces, and schemas first
2. **Define function signatures** - input/output types before logic
3. **Implement to satisfy types** - let the compiler guide completeness
4. **Validate at boundaries** - runtime checks where data enters the system

### Make Illegal States Unrepresentable

```typescript
// Good: only valid combinations possible
type RequestState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error };

// Bad: allows invalid combinations
type RequestStateBad<T> = {
  loading: boolean;
  data?: T;
  error?: Error;
};
```

### Branded Types for Domain Primitives

```typescript
type UserId = string & { readonly __brand: 'UserId' };
type OrderId = string & { readonly __brand: 'OrderId' };

// Compiler prevents passing OrderId where UserId expected
function getUser(id: UserId): Promise<User> { /* ... */ }
function createUserId(id: string): UserId {
  return id as UserId;
}
```

## Recommended Tools

- **TypeScript** - Language and compiler
- **Zod** - Schema validation and type inference
- **Vitest** - Testing and type testing
- **type-fest** - Advanced type utilities (optional)
- **Biome** - Fast linting and formatting (optional)

## Contributing

This is a living document. As you learn new patterns or discover better approaches, contribute back to make this guide more comprehensive.

## License

MIT
