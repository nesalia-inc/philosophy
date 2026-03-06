# Documentation Standards

Comprehensive guide to creating high-quality documentation for OSS, internal developers, and AI agents.

---

## Table of Contents

1. [Philosophy](./philosophy.md)
2. [Multi-Level Documentation](./multi-level.md)
3. [External (OSS)](./external/)
4. [Internal](./internal/)
5. [Agent Configuration](./agent-config/)
6. [Templates](./templates/)
7. [Maintenance](./maintenance.md)

---

## The Big Picture

Every package we develop is designed to be **Open Source**. Our documentation must be at the level of Next.js, React, and other industry-standard tools.

This means **documentation is an integral part of the product**, not an afterthought.

---

## Three Levels of Documentation

| Level | Audience | Question | Purpose |
|-------|----------|---------|---------|
| **External (OSS)** | Developers using our packages | "How do I use this?" | Integration |
| **Internal** | Developers working on the code | "How does this work?" | Contribution |
| **Agent** | AI agents on any project | "What exists and how do I use it?" | Automation |

### The Package Dependency Problem

```
Project A (working on)
    │
    ▼ uses
┌─────────────────────────────────────┐
│        Package B (we built)         │
│                                     │
│  ├── Doc External: How to use       │  ← External developers
│  ├── Doc Internal: How it works    │  ← Internal developers
│  └── Doc Agent: Everything          │  ← All AI agents
└─────────────────────────────────────┘
```

When an agent works on Project A, it must know:
- Package B exists
- How to use it
- All its options
- How it connects to other packages

---

## The Central Catalog

All packages must be documented in a **central catalog** so agents can discover and use them:

```
docs/
├── index.md                 # List of all packages
├── packages/               # Agent documentation for each package
│   ├── auth.md
│   ├── database.md
│   └── ui.md
└── architecture/           # How packages connect together
    └── overview.md
```

This catalog is the **single source of truth** for all AI agents.

---

## Core Principles

### 1. Progressive Disclosure

Never overwhelm. Reveal information when needed:

```
Level 1: Quick Start (5 min)
    │
    ▼
Level 2: Tutorial (30 min)
    │
    ▼
Level 3: Deep Dive (hours)
```

### 2. Action-Oriented

Write to answer questions before they're asked:

```
❌ BAD:
"The authentication system supports OAuth2."

✅ GOOD:
"Add OAuth2 login in 3 steps:"
1. Configure provider
2. Add callback route
3. Use the auth() helper
```

### 3. Copy-Paste Ready

Every code example should work when copied:

```
❌ BAD:
"You need to set up the database connection."

✅ GOOD:
```typescript
const db = database({
  connectionString: process.env.DATABASE_URL
})
```
```

### 4. Maintainable > Perfect

```
Doc perfect but outdated = dangerous
Doc imperfect but current = usable
```

### 5. Honest About Limitations

```
❌ BAD:
"Our system is enterprise-ready"

✅ GOOD:
"Currently supports 1000 users. Enterprise scale coming Q3."
```

---

## Documentation Structure

### For Users

```
docs/
├── README.md              # What is this project?
├── quick-start.md         # Start in 5 minutes
├── tutorial.md            # Learn by example
├── concepts/              # Understand the "why"
│   ├── architecture.md
│   └── patterns.md
├── api/                   # Complete reference
│   ├── overview.md
│   └── endpoints.md
├── guides/                # Specific tasks
│   └── deployment.md
└── troubleshooting.md     # Solve problems
```

### For Agents

```
.ai/
├── rules.md               # Coding rules (24 rules)
├── prompts/               # Workflow prompts
│   ├── new-feature.md
│   ├── bug-fix.md
│   └── review.md
└── context.md             # Project overview
```

---

## Quick Reference

| Principle | User Docs | Agent Docs |
|-----------|-----------|------------|
| Tone | Welcoming, clear | Direct, explicit |
| Examples | "Do X" | "Must follow X" |
| Ambiguity | Acceptable | Forbidden |
| Context | Progressive | Complete |
| Updates | When features change | When rules change |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
