# Chaotic Module Analysis

What happens when a module does too many things without clear structure.

---

## The File: `next.ts`

~300 lines that do:
- Type definitions and re-exports
- Environment detection
- Debug logging
- Path validation
- File watching
- Collection loading
- Next.js plugin creation
- Type guards

---

## The Problem: No Clear Purpose

When you open this file, you can't answer:

1. **What is this module supposed to do?**
2. **What is the main entry point?**
3. **What is the data flow?**

The file is a list of loosely related functions, not a cohesive module.

---

## What Went Wrong

### 1. No Single Responsibility

This file tries to be:
- A type definitions file
- An environment detector
- A file watcher
- A config loader
- A Next.js plugin

That's 5 different responsibilities.

### 2. No Structure

```
File structure:
├── Types
├── Re-exports
├── Helper functions
├── Main function (withCollections)
├── Deprecated function (withCollectionsSync)
├── More helper functions
├── More exports
```

There's no grouping, no hierarchy, no flow.

### 3. Implementation Hidden

Even if you understand the purpose, you can't easily trace:
- How does `withCollections` work?
- What does it return?
- What are the dependencies?

---

## The Fix: Structure First

### Step 1: Define the Module's Purpose

```typescript
// WHAT THIS MODULE DOES:
// Provides Next.js integration for collections
// - Loads collections from config file
// - Watches for changes in development
// - Injects collections into Next.js config
```

### Step 2: Separate Concerns

```
next/
├── index.ts          // Main exports
├── env.ts            // Environment detection
├── loader.ts         // Collection loading
├── watcher.ts        // File watching
├── plugin.ts         // Next.js plugin
└── types.ts          // Type definitions
```

### Step 3: Document the Flow

```typescript
// Data flow:
// 1. User calls withCollections(nextConfig, options)
// 2. Load collections from configPath
// 3. Configure webpack if hotReload enabled
// 4. Return merged config
```

---

### Step 4: Use Try Monad

The module catches exceptions but returns empty — caller doesn't know it failed.

```typescript
// Current: catches everything, returns empty
try {
  return await loadCollectionsFromConfig(...)
} catch (error) {
  debugLog(debug, `Failed:`, error)
  return {}  // ← caller doesn't know it failed
}
```

Use a Try monad that wraps the entire operation:

```typescript
// Try monad: wraps sync or async functions
const Try = {
  // Sync version
  of: <T>(fn: () => T): Try<T> => {
    try {
      return { ok: true, value: fn() }
    } catch (e) {
      return { ok: false, error: e as Error }
    }
  },

  // Async version
  async: <T>(fn: () => Promise<T>): Promise<Try<T>> => {
    try {
      return { ok: true, value: await fn() }
    } catch (e) {
      return { ok: false, error: e as Error }
    }
  }
}

// Define error types
type ConfigError =
  | { type: 'invalid_path'; path: string }
  | { type: 'load_failed'; reason: string }

// Use Try monad
const loadCollections = async (path): Promise<Try<Collections>> =>
  Try.async(async () => {
    const validated = validatePath(path)
    if (!validated.ok) {
      throw new Error(validated.error)
    }
    return await import(validated.value)
  })

// Caller handles both success and failure
const result = await loadCollections('./config')
if (result.ok) {
  console.log(result.value)  // ← collections
} else {
  console.error(result.error)  // ← Error with reason
}
```

The Try monad makes errors explicit in the type — the caller **must** handle them.

---

## The Rule

Before writing a module, answer:

1. **What does this module do?** (one sentence)
2. **What is the entry point?**
3. **What is the data flow?**

If you can't answer these, the module does too much.

---

## Checklist

| Check | Question |
|-------|----------|
| Purpose | Can you explain what this module does in one sentence? |
| Entry | What's the main function? |
| Flow | Can you trace data through the module? |
| Size | Is this module doing more than one thing? |
| Structure | Are related things grouped together? |

---

*Document version: 1.0*
*Last updated: 2026-03-05*
