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
