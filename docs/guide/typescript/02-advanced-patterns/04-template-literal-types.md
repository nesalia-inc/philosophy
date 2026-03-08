# 2.4 Template Literal Types

> **Learning Goal**: Use template literal types for type-level string manipulation, validation, and type-safe APIs.

## What are Template Literal Types?

Template literal types allow you to work with string types at the type level, similar to how template literals work in JavaScript. They enable powerful string transformations and validations.

```typescript
type Greeting = `hello ${string}`;
const valid: Greeting = "hello world"; // OK
const invalid: Greeting = "goodbye world"; // Error
```

## Basic Template Literal Types

### String Concatenation

```typescript
type World = "world";
type Greeting = `hello ${World}`;
// Result: "hello world"

type Event = "click";
type EventHandler = `on${Capitalize<Event>}`;
// Result: "onClick"
```

### Union Types in Template Literals

```typescript
type Color = "red" | "blue" | "green";
type CssClass = `.bg-${Color}`;
// Result: ".bg-red" | ".bg-blue" | ".bg-green"

type Size = "sm" | "md" | "lg";
type ButtonClass = `btn-${Size}`;
// Result: "btn-sm" | "btn-md" | "btn-lg"
```

### Multiple Placeholders

```typescript
type Size = "sm" | "md" | "lg";
type Color = "red" | "blue" | "green";
type ButtonClass = `btn-${Size}-${Color}`;
// Result: "btn-sm-red" | "btn-sm-blue" | "btn-sm-green" | "btn-md-red" | ... (9 combinations)

type Direction = "top" | "right" | "bottom" | "left";
type Spacing = `m${Direction}` | `p${Direction}`;
// Result: "mt" | "mr" | "mb" | "ml" | "pt" | "pr" | "pb" | "pl"
```

## String Manipulation Types

### Uppercase

```typescript
type Greeting = "hello";
type LoudGreeting = Uppercase<Greeting>;
// Result: "HELLO"

type Event = "click";
type Handler = `on${Uppercase<Event>}`;
// Result: "onCLICK"

// With unions
type Events = "click" | "focus" | "blur";
type Handlers = `on${Uppercase<Events>}`;
// Result: "onCLICK" | "onFOCUS" | "onBLUR"
```

### Lowercase

```typescript
type Greeting = "HELLO";
type QuietGreeting = Lowercase<Greeting>;
// Result: "hello"

type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type LowerMethod = Lowercase<HttpMethod>;
// Result: "get" | "post" | "put" | "delete"
```

### Capitalize

```typescript
type Greeting = "hello";
type CapitalizedGreeting = Capitalize<Greeting>;
// Result: "Hello"

type Event = "click";
type Handler = `on${Capitalize<Event>}`;
// Result: "onClick"

// With unions
type Events = "click" | "focus" | "blur";
type Handlers = `on${Capitalize<Events>}`;
// Result: "onClick" | "onFocus" | "onBlur"
```

### Uncapitalize

```typescript
type Greeting = "Hello";
type UncapitalizedGreeting = Uncapitalize<Greeting>;
// Result: "hello"

type Handler = "onClick";
type Event = Uncapitalize<Handler extends `on${infer E}` ? E : never>;
// Result: "click"
```

## Advanced Patterns

### Type-Safe Event Handlers

```typescript
type Event = "click" | "focus" | "blur";
type EventHandler = `on${Capitalize<Event>}`;

interface EventEmitter {
  on<K extends Event>(
    event: K,
    handler: (data: EventData[K]) => void
  ): void;
  emit<K extends Event>(
    event: K,
    data: EventData[K]
  ): void;
}

interface EventData {
  click: { x: number; y: number };
  focus: { target: HTMLElement };
  blur: { target: HTMLElement };
}

const emitter: EventEmitter = {
  on(event, handler) {
    // Implementation
  },
  emit(event, data) {
    // Implementation
  }
};

// Type-safe usage
emitter.on("click", (data) => {
  console.log(data.x, data.y); // OK
});

emitter.emit("click", { x: 10, y: 20 }); // OK
// emitter.emit("click", { x: 10 }); // Error: missing 'y'
```

### Type-Safe CSS Classes

```typescript
type Color = "red" | "blue" | "green" | "yellow";
type Shade = "100" | "200" | "300" | "400" | "500" | "600" | "700" | "800" | "900";

type TextColor = `text-${Color}-${Shade}`;
type BgColor = `bg-${Color}-${Shade}`;

const textClass: TextColor = "text-blue-500"; // OK
const bgClass: BgColor = "bg-red-700"; // OK

// const invalid: TextColor = "text-purple-500"; // Error: 'purple' is not in Color
```

### Type-Safe Routing

```typescript
type Routes =
  | "/"
  | "/users"
  | "/users/:id"
  | "/users/:userId/posts/:postId"
  | "/posts/:id";

// Extract route parameters
type ExtractRouteParams<T> =
  T extends `${infer Start}:${infer Param}/${infer Rest}`
    ? { [K in Param | keyof ExtractRouteParams<`/${Rest}`>]: string }
    : T extends `${infer Start}:${infer Param}`
      ? { [K in Param]: string }
      : {};

type T1 = ExtractRouteParams<"/users/:id">;
// Result: { id: string }

type T2 = ExtractRouteParams<"/users/:userId/posts/:postId">;
// Result: { userId: string; postId: string }

type T3 = ExtractRouteParams<"/">;
// Result: {}

// Type-safe route function
function navigate<T extends Routes>(
  route: T,
  params?: ExtractRouteParams<T>
): string {
  if (!params) return route;

  return Object.entries(params).reduce(
    (acc, [key, value]) => acc.replace(`:${key}`, value),
    route
  );
}

navigate("/users/:id", { id: "123" }); // OK: "/users/123"
navigate("/users/:userId/posts/:postId", { userId: "123", postId: "456" }); // OK
// navigate("/users/:id", {}); // Error: missing id
// navigate("/users/:id", { id: "123", extra: "foo" }); // Error: extra property
```

### Type-Safe Object Paths

```typescript
type Path<T> = T extends object
  ? {
      [K in keyof T]: K extends string
        ? T[K] extends object
          ? `${K}` | `${K}.${Path<T[K]>}`
          : K
        : never;
    }[keyof T]
  : never;

interface Config {
  server: {
    host: string;
    port: number;
    ssl: {
      enabled: boolean;
      cert: string;
    };
  };
  database: {
    url: string;
  };
}

type ConfigPath = Path<Config>;
// Result: "server" | "database" | "server.host" | "server.port" | "server.ssl" | "server.ssl.enabled" | "server.ssl.cert" | "database.url"

// Type-safe path getter
function get<T, P extends Path<T>>(obj: T, path: P): any {
  return path.split(".").reduce((acc: any, key) => acc?.[key], obj);
}

const config: Config = {
  server: {
    host: "localhost",
    port: 3000,
    ssl: {
      enabled: true,
      cert: "/path/to/cert"
    }
  },
  database: {
    url: "postgres://localhost"
  }
};

get(config, "server.host"); // OK: string
get(config, "server.ssl.enabled"); // OK: boolean
// get(config, "server.invalid"); // Error
```

### Type-Safe Getter/Setter Names

```typescript
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

type Setters<T> = {
  [K in keyof T as `set${Capitalize<string & K>}`]: (value: T[K]) => void;
};

interface User {
  name: string;
  age: number;
  email: string;
}

type UserGetters = Getters<User>;
// Result: {
//   getName: () => string;
//   getAge: () => number;
//   getEmail: () => string;
// }

type UserSetters = Setters<User>;
// Result: {
//   setName: (value: string) => void;
//   setAge: (value: number) => void;
//   setEmail: (value: string) => void;
// }

type UserAccessors = Getters<T> & Setters<T>;
```

### String Type Guards

```typescript
type StartsWith<T, S extends string> = T extends `${S}${string}` ? true : false;

type T1 = StartsWith<"hello world", "hello">; // true
type T2 = StartsWith<"hello world", "world">; // false

type EndsWith<T, S extends string> = T extends `${string}${S}` ? true : false;

type T3 = EndsWith<"hello world", "world">; // true
type T4 = EndsWith<"hello world", "hello">; // false

type Includes<T, S extends string> = T extends `${string}${S}${string}`
  ? true
  : false;

type T5 = Includes<"hello world", "llo wo">; // true
type T6 = Includes<"hello world", "xyz">; // false
```

## Practical Examples

### Type-Safe Translation Keys

```typescript
type TranslationKey =
  | "common.save"
  | "common.cancel"
  | "common.delete"
  | "user.profile"
  | "user.settings"
  | "auth.login"
  | "auth.logout";

function t(key: TranslationKey, params?: Record<string, string>): string {
  // Implementation
  return key;
}

t("common.save"); // OK
t("user.profile"); // OK
// t("invalid.key"); // Error
```

### Type-Safe Action Creators

```typescript
type ActionPrefix = "user" | "post" | "comment";
type ActionName = "fetch" | "create" | "update" | "delete";
type ActionType = `${Capitalize<ActionPrefix>}/${Uppercase<ActionName>}`;

type Actions = {
  [K in ActionType]: K extends `${infer P}/${infer N}`
    ? { type: K; payload?: any }
    : never;
}[ActionType];

// Result: {
//   type: "USER/FETCH" | "USER/CREATE" | "USER/UPDATE" | "USER/DELETE" |
//         "POST/FETCH" | "POST/CREATE" | "POST/UPDATE" | "POST/DELETE" |
//         "COMMENT/FETCH" | "COMMENT/CREATE" | "COMMENT/UPDATE" | "COMMENT/DELETE";
//   payload?: any;
// }

function createAction<T extends ActionType>(
  type: T,
  payload?: any
): { type: T; payload?: any } {
  return { type, payload };
}

const action = createAction("User/Fetch", { id: "123" });
// action.type is "USER/FETCH"
```

### Type-Safe Internationalization

```typescript
type Locale = "en" | "fr" | "es" | "de";

type TranslationKey<K extends string> =
  | `common.${K}`
  | `user.${K}`
  | `auth.${K}`;

type TranslationKeys = TranslationKey<"save" | "cancel" | "delete" | "login">;
// Result: "common.save" | "common.cancel" | "common.delete" |
//         "user.save" | "user.cancel" | "user.delete" |
//         "auth.save" | "auth.cancel" | "auth.delete" |
//         "auth.login"

const translations: Record<Locale, Record<TranslationKeys, string>> = {
  en: {
    "common.save": "Save",
    "common.cancel": "Cancel",
    "common.delete": "Delete",
    "user.save": "Save User",
    "user.cancel": "Cancel User",
    "user.delete": "Delete User",
    "auth.save": "Save",
    "auth.cancel": "Cancel",
    "auth.delete": "Delete",
    "auth.login": "Login"
  },
  fr: {
    "common.save": "Enregistrer",
    "common.cancel": "Annuler",
    "common.delete": "Supprimer",
    "user.save": "Enregistrer l'utilisateur",
    "user.cancel": "Annuler l'utilisateur",
    "user.delete": "Supprimer l'utilisateur",
    "auth.save": "Enregistrer",
    "auth.cancel": "Annuler",
    "auth.delete": "Supprimer",
    "auth.login": "Connexion"
  },
  es: {
    "common.save": "Guardar",
    "common.cancel": "Cancelar",
    "common.delete": "Eliminar",
    "user.save": "Guardar Usuario",
    "user.cancel": "Cancelar Usuario",
    "user.delete": "Eliminar Usuario",
    "auth.save": "Guardar",
    "auth.cancel": "Cancelar",
    "auth.delete": "Eliminar",
    "auth.login": "Iniciar sesión"
  },
  de: {
    "common.save": "Speichern",
    "common.cancel": "Abbrechen",
    "common.delete": "Löschen",
    "user.save": "Benutzer speichern",
    "user.cancel": "Benutzer abbrechen",
    "user.delete": "Benutzer löschen",
    "auth.save": "Speichern",
    "auth.cancel": "Abbrechen",
    "auth.delete": "Löschen",
    "auth.login": "Anmelden"
  }
};
```

## Combining with Conditional Types

### Smart String Parsing

```typescript
type ParseColor<T> =
  T extends `#${infer R}`
    ? R extends `${infer A}${infer B}${infer C}${infer D}${infer E}${infer F}`
      ? { red: number; green: number; blue: number }
      : never
    : never;

type T1 = ParseColor<"#FF0000">;
// Result: { red: number; green: number; blue: number }

type T2 = ParseColor<"invalid">;
// Result: never
```

### Extract Information from Strings

```typescript
type ExtractEmailParts<T> =
  T extends `${infer Local}@${infer Domain}`
    ? { local: Local; domain: Domain }
    : never;

type T1 = ExtractEmailParts<"user@example.com">;
// Result: { local: "user"; domain: "example.com" }

type ExtractUrlParts<T> =
  T extends `https://${infer Domain}${infer Path}`
    ? { protocol: "https"; domain: Domain; path: Path }
    : T extends `http://${infer Domain}${infer Path}`
      ? { protocol: "http"; domain: Domain; path: Path }
      : never;

type T2 = ExtractUrlParts<"https://example.com/path">;
// Result: { protocol: "https"; domain: "example.com"; path: "/path" }
```

## Best Practices

1. **Use template literal types for string-based APIs**:
   ```typescript
   // Good: Type-safe event handlers
   type EventHandler = `on${Capitalize<Event>}`;

   // Good: Type-safe CSS classes
   type ColorClass = `text-${Color}-${Shade}`;
   ```

2. **Capitalize utility names** for consistency:
   ```typescript
   type GetterName = `get${Capitalize<keyof T>}`;
   type SetterName = `set${Capitalize<keyof T>}`;
   ```

3. **Use intrinsic string types** for transformations:
   ```typescript
   type UppercaseEvent = Uppercase<Event>;
   type LowercaseMethod = Lowercase<Method>;
   ```

4. **Combine with conditional types** for complex logic:
   ```typescript
   type ExtractPathParams<T> =
     T extends `${infer Start}:${infer Param}/${infer Rest}`
       ? { [K in Param | keyof ExtractPathParams<`/${Rest}`>]: string }
       : T extends `${infer Start}:${infer Param}`
         ? { [K in Param]: string }
         : {};
   ```

## Common Mistakes

### Mistake 1: Not Using Capitalize for Names

```typescript
// Bad: Inconsistent naming
type Handler = `on${Event}`; // "onclick" instead of "onClick"

// Good: Use Capitalize
type Handler = `on${Capitalize<Event>}`; // "onClick"
```

### Mistake 2: Not Considering All Combinations

```typescript
// Bad: May miss combinations
type Button = "primary" | "secondary";
type Size = "small" | "large";
type ButtonClass = `btn-${Button}`; // Missing size

// Good: Include all combinations
type ButtonClass = `btn-${Button}-${Size}`;
```

### Mistake 3: Over-Complicating Template Literals

```typescript
// Bad: Hard to understand
type Complex<T> = T extends `${infer A}-${infer B}-${infer C}-${infer D}` ? D extends `${infer E}${infer F}` ? ... : never : never;

// Good: Break into smaller types
type Part1<T> = T extends `${infer A}-${infer B}-${infer C}` ? C : never;
type Part2<T> = T extends `${infer A}-${infer B}` ? B : never;
```

## Exercises

1. Create a type-safe CSS class system for a UI library
2. Create a type-safe routing system with parameter extraction
3. Create type-safe action creators for a Redux-like state management
4. Create a type-safe translation key system
5. Create a type-safe event emitter using template literal types

## Next Lesson

Now that you understand template literal types, let's explore **[2.5 Type Guards and Narrowing](./05-type-guards.md)** to learn about runtime type checking.
