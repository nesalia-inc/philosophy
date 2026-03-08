# 2.2 Utility Types

> **Learning Goal**: Master TypeScript's built-in utility types for transforming and manipulating types.

## What are Utility Types?

Utility types are built-in generic types that transform and manipulate other types. They help you avoid repetitive type definitions and enable powerful type transformations.

## Partial<T>

Makes all properties of `T` optional.

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  age: number;
}

// All properties are optional
type PartialUser = Partial<User>;

// Equivalent to:
type PartialUser = {
  id?: string;
  name?: string;
  email?: string;
  age?: number;
};

// Use case: Update function
function updateUser(id: string, updates: Partial<User>): User {
  const existing = getUser(id);
  return { ...existing, ...updates };
}

updateUser("123", { age: 30 }); // OK: only update age
updateUser("123", { name: "Bob", email: "bob@example.com" }); // OK
```

## Required<T>

Makes all properties of `T` required.

```typescript
interface PartialUser {
  id?: string;
  name?: string;
  email?: string;
}

// All properties are required
type CompleteUser = Required<PartialUser>;

// Equivalent to:
type CompleteUser = {
  id: string;
  name: string;
  email: string;
};

// Use case: Ensure all fields are present
function createUser(user: Required<PartialUser>): User {
  if (!user.id || !user.name || !user.email) {
    throw new Error("Missing required fields");
  }
  return user as User;
}
```

## Readonly<T>

Makes all properties of `T` readonly.

```typescript
interface User {
  id: string;
  name: string;
}

type ReadonlyUser = Readonly<User>;

const user: ReadonlyUser = {
  id: "123",
  name: "Alice"
};

// Error: Cannot assign to 'name' because it is a read-only property
// user.name = "Bob";

// Use case: Immutable data structures
function ImmutableUser(data: Readonly<User>): Readonly<User> {
  return Object.freeze({ ...data });
}
```

## ReadonlyArray<T>

Read-only array type.

```typescript
const numbers: ReadonlyArray<number> = [1, 2, 3];

// Error: Property 'push' does not exist on type 'ReadonlyArray<number>'
// numbers.push(4);

// Error: Index signature in type 'ReadonlyArray<number>' only permits reading
// numbers[0] = 10;

// Shorthand: readonly T[]
const readonly: readonly number[] = [1, 2, 3];
```

## Record<K, T>

Creates an object type with keys `K` and values `T`.

```typescript
// Basic usage
type PageInfo = Record<"home" | "about" | "contact", { title: string }>;

const pages: PageInfo = {
  home: { title: "Home" },
  about: { title: "About Us" },
  contact: { title: "Contact" }
};

// Dynamic keys
type UserRole = "admin" | "user" | "guest";
type Permissions = Record<UserRole, string[]>;

const rolePermissions: Permissions = {
  admin: ["read", "write", "delete"],
  user: ["read", "write"],
  guest: ["read"]
};

// Use case: Configuration objects
type Config = Record<string, string | number | boolean>;

const config: Config = {
  apiUrl: "https://api.example.com",
  timeout: 5000,
  debug: true
};
```

## Pick<T, K>

Selects specific properties `K` from `T`.

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  age: number;
  createdAt: Date;
}

// Pick only name and email
type PublicUser = Pick<User, "name" | "email">;

// Equivalent to:
type PublicUser = {
  name: string;
  email: string;
};

// Use case: Public API responses
function toPublicUser(user: User): PublicUser {
  return {
    name: user.name,
    email: user.email
  };
}

// Dynamic pick
type UserKeys = keyof User;
type IdAndName = Pick<User, "id" | "name">;
```

## Omit<T, K>

Removes specific properties `K` from `T`.

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  password: string;
}

// Remove password
type SafeUser = Omit<User, "password">;

// Equivalent to:
type SafeUser = {
  id: string;
  name: string;
  email: string;
};

// Use case: Remove sensitive data
function sanitizeUser(user: User): SafeUser {
  const { password, ...safe } = user;
  return safe;
}

// Multiple properties
type UserWithoutMetadata = Omit<User, "id" | "createdAt">;
```

## Exclude<T, U>

Excludes types from `T` that are assignable to `U`.

```typescript
type T = Exclude<string | number | boolean, string>;
// Result: number | boolean

type T2 = Exclude<"a" | "b" | "c", "a" | "b">;
// Result: "c"

// Use case: Filter out specific types
type NonString = Exclude<string | number | boolean, string>;
// Result: number | boolean

// Practical example
type EventPayloads =
  | { type: "click"; x: number; y: number }
  | { type: "keypress"; key: string }
  | { type: "focus" };

type ClickEvents = Exclude<EventPayloads, { type: "keypress" } | { type: "focus" }>;
// Result: { type: "click"; x: number; y: number }
```

## Extract<T, U>

Extracts types from `T` that are assignable to `U`.

```typescript
type T = Extract<string | number | boolean, string | number>;
// Result: string | number

type T2 = Extract<"a" | "b" | "c", "a" | "b">;
// Result: "a" | "b"

// Use case: Extract specific event types
type ClickEvent = Extract<
  EventPayloads,
  { type: "click" }
>;
// Result: { type: "click"; x: number; y: number }
```

## NonNullable<T>

Excludes `null` and `undefined` from `T`.

```typescript
type T = NonNullable<string | null | undefined>;
// Result: string

type T2 = NonNullable<number | null | undefined>;
// Result: number

// Use case: Ensure value is present
function processValue(value: NonNullable<string | null>): string {
  return value.toUpperCase(); // Safe: value cannot be null
}

// Practical example
type Response<T> = {
  data: NonNullable<T>;
  error: null;
} | {
  data: null;
  error: string;
};
```

## ReturnType<T>

Extracts the return type of a function type.

```typescript
function getUser() {
  return { id: 1, name: "Alice" };
}

type User = ReturnType<typeof getUser>;
// Result: { id: number; name: string }

// Async functions
async function fetchUser(id: string) {
  return { id, name: "Alice" };
}

type FetchUserResult = ReturnType<typeof fetchUser>;
// Result: Promise<{ id: string; name: string }>

// Use case: Get return type without defining it
function createValidator<T extends (...args: any[]) => any>(
  fn: T
): (args: Parameters<T>) => ReturnType<T> {
  return (args) => fn(...args);
}
```

## Parameters<T>

Extracts the parameter types of a function type.

```typescript
function createUser(name: string, age: number, email: string) {
  return { name, age, email };
}

type CreateUserParams = Parameters<typeof createUser>;
// Result: [name: string, age: number, email: string]

// Use case: Call function with parameters array
function callWithParams<T extends (...args: any[]) => any>(
  fn: T,
  params: Parameters<T>
): ReturnType<T> {
  return fn(...params);
}

callWithParams(createUser, ["Alice", 30, "alice@example.com"]);
```

## ConstructorParameters<T>

Extracts the parameter types of a constructor function.

```typescript
class User {
  constructor(
    public name: string,
    public age: number
  ) {}
}

type UserConstructorParams = ConstructorParameters<typeof User>;
// Result: [name: string, age: number]

// Use case: Create instance with parameters array
function createInstance<T extends new (...args: any[]) => any>(
  Constructor: T,
  params: ConstructorParameters<T>
): InstanceType<T> {
  return new Constructor(...params);
}

const user = createInstance(User, ["Alice", 30]);
```

## InstanceType<T>

Extracts the instance type of a constructor function.

```typescript
class User {
  constructor(
    public name: string,
    public age: number
  ) {}
}

type UserInstance = InstanceType<typeof User>;
// Result: User

// Use case: Factory functions
function createInstance<T extends new (...args: any[]) => any>(
  Constructor: T,
  ...args: ConstructorParameters<T>
): InstanceType<T> {
  return new Constructor(...args);
}
```

## ThisParameterType<T>

Extracts the type of the `this` parameter for a function type.

```typescript
function toHex(this: Number): string {
  return this.toString(16);
}

type ToHexThis = ThisParameterType<typeof toHex>;
// Result: Number

// Use case: Bind function with correct this type
function bind<T extends (...args: any[]) => any>(
  fn: T,
  thisArg: ThisParameterType<T>
): T {
  return fn.bind(thisArg);
}
```

## OmitThisParameter<T>

Removes the `this` parameter from a function type.

```typescript
function toHex(this: Number): string {
  return this.toString(16);
}

type ToHex = OmitThisParameter<typeof toHex>;
// Result: () => string

// Use case: Create function without this parameter
const boundToHex: OmitThisParameter<typeof toHex> = toHex.bind(15);
```

## ThisType<T>

Marker interface for specifying the type of `this` in object literals.

```typescript
interface State {
  name: string;
  age: number;
}

interface Methods {
  logName(this: State): void;
  logAge(this: State): void;
}

const state: State & ThisType<State & Methods> = {
  name: "Alice",
  age: 30,
  logName() {
    console.log(this.name); // this is State & Methods
  },
  logAge() {
    console.log(this.age);
  }
};

state.logName(); // OK
state.logAge(); // OK
```

## Uppercase/Lowercase/Capitalize/Uncapitalize

String manipulation types.

```typescript
type Greeting = "hello, world";

type Upper = Uppercase<Greeting>;
// Result: "HELLO, WORLD"

type Lower = Lowercase<Upper>;
// Result: "hello, world"

type Capitalized = Capitalize<Greeting>;
// Result: "Hello, world"

type Uncapitalized = Uncapitalize<Capitalized>;
// Result: "hello, world"

// Use case: Event handler naming
type Event = "click" | "focus" | "blur";

type HandlerName = `on${Capitalize<Event>}`;
// Result: "onClick" | "onFocus" | "onBlur"
```

## Combining Utility Types

### DeepPartial

Make all nested properties optional:

```typescript
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object
    ? T[P] extends Array<infer U>
      ? Array<DeepPartial<U>>
      : DeepPartial<T[P]>
    : T[P];
};

interface Config {
  server: {
    host: string;
    port: number;
  };
  database: {
    url: string;
    pool: {
      min: number;
      max: number;
    };
  };
}

type PartialConfig = DeepPartial<Config>;

const config: PartialConfig = {
  server: {
    host: "localhost"
    // port is optional
  }
  // database is optional
};
```

### DeepReadonly

Make all nested properties readonly:

```typescript
type DeepReadonly<T> = {
  readonly [P in keyof T]: T[P] extends object
    ? T[P] extends Function
      ? T[P]
      : DeepReadonly<T[P]>
    : T[P];
};

interface Config {
  server: {
    host: string;
    port: number;
  };
}

type ReadonlyConfig = DeepReadonly<Config>;

const config: ReadonlyConfig = {
  server: {
    host: "localhost",
    port: 3000
  }
};

// config.server.host = "example.com"; // Error: readonly
```

### DeepRequired

Make all nested properties required:

```typescript
type DeepRequired<T> = {
  [P in keyof T]-?: T[P] extends object
    ? T[P] extends Array<infer U>
      ? Array<DeepRequired<U>>
      : DeepRequired<T[P]>
    : T[P];
};
```

## Practical Examples

### API Response Types

```typescript
interface User {
  id: string;
  name: string;
  email: string;
  password: string;
  createdAt: Date;
}

// Public user (without password)
type PublicUser = Omit<User, "password">;

// Create user input (without id and createdAt)
type CreateUserInput = Omit<User, "id" | "createdAt">;

// Update user input (all fields optional)
type UpdateUserInput = Partial<CreateUserInput>;

// Response types
type UserResponse = PublicUser;
type UsersListResponse = PublicUser[];
type UserCreateResponse = PublicUser;
type UserUpdateResponse = PublicUser;
```

### Form State Types

```typescript
interface FormState<T> {
  values: T;
  errors: Record<keyof T, string | null>;
  touched: Record<keyof T, boolean>;
}

interface LoginForm {
  email: string;
  password: string;
}

type LoginFormState = FormState<LoginForm>;

const formState: LoginFormState = {
  values: {
    email: "",
    password: ""
  },
  errors: {
    email: null,
    password: null
  },
  touched: {
    email: false,
    password: false
  }
};
```

## Best Practices

1. **Use utility types to reduce duplication**:
   ```typescript
   // Bad
   interface PublicUser {
     name: string;
     email: string;
   }

   // Good
   type PublicUser = Omit<User, "id" | "password">;
   ```

2. **Combine utility types for complex transformations**:
   ```typescript
   type UpdateInput = Partial<Omit<User, "id">>;
   ```

3. **Prefer `Omit` over `Pick` for exclusion**:
   ```typescript
   // Good: More explicit intent
   type Safe = Omit<User, "password">;

   // Less clear: Have to list all other fields
   type Safe = Pick<User, "id" | "name" | "email">;
   ```

4. **Use `Readonly` for immutable data**:
   ```typescript
   function getConfig(): Readonly<Config> {
     return Object.freeze(config);
   }
   ```

## Common Mistakes

### Mistake 1: Forgetting `Partial` is shallow

```typescript
interface Config {
  server: {
    host: string;
    port: number;
  };
}

// server properties are still required
const config: Partial<Config> = {
  server: {
    host: "localhost"
    // Error: port is missing
  }
};

// Solution: Use DeepPartial (see above)
```

### Mistake 2: Not using utility types for CRUD operations

```typescript
// Bad: Creating separate types
interface CreateUser {
  name: string;
  email: string;
}

interface UpdateUser {
  name?: string;
  email?: string;
}

// Good: Use utility types
type CreateUser = Omit<User, "id">;
type UpdateUser = Partial<CreateUser>;
```

## Exercises

1. Create a `DeepPartial` utility type
2. Create a `DeepReadonly` utility type
3. Use utility types to define CRUD operation types for an entity
4. Create a `Nullable<T>` utility type
5. Create utility types for API request/response handling

## Next Lesson

Now that you understand utility types, let's explore **[2.3 Conditional Types](./03-conditional-types.md)** to learn how to create types that branch based on type properties.
