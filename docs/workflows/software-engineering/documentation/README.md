# Documentation Standards

Comprehensive guide to creating high-quality documentation for both users and AI agents.

---

## Table of Contents

1. [Philosophy](./philosophy.md)
2. [User Documentation](./user-docs/)
3. [Agent Configuration](./agent-config/)
4. [Templates](./templates/)
5. [Maintenance](./maintenance.md)

---

## Overview

Documentation serves two distinct audiences with different needs:

| Audience | Primary Question | Needs |
|----------|-----------------|-------|
| **Users** | "How do I use this?" | Quick starts, examples, troubleshooting |
| **AI Agents** | "How do I implement this?" | Rules, patterns, complete context |

Good documentation addresses both without compromise.

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
