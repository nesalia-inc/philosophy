# Multi-Level Documentation Architecture

Understanding the three levels of documentation for every package.

---

## The Three Levels

```
┌─────────────────────────────────────────────────────────┐
│                    OSS (Public)                         │
│  Documentation like Next.js, React                      │
│  "How to use this package"                             │
│                                                         │
│  → External developers who install our packages        │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│              Internal (Organisation)                     │
│  Documentation of internal workings                    │
│  "How this works under the hood"                      │
│                                                         │
│  → Developers who modify the packages                  │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                    Agents IA                            │
│  Complete knowledge + package connections               │
│  "What exists, how it connects, how to use"            │
│                                                         │
│  → AI agents working on any project using our packages │
└─────────────────────────────────────────────────────────┘
```

---

## Why This Matters

### Without Multi-Level Documentation

```
Agent on Project A needs to use Package B:
    │
    ▼
Agent doesn't know Package B exists
    │
    ▼
Agent invents its own solution OR asks human
    │
    ▼
Inconsistent code OR blocked
```

### With Multi-Level Documentation

```
Agent on Project A needs to use Package B:
    │
    ▼
Agent reads central catalog
    │
    ▼
Agent finds Package B, reads agent doc
    │
    ▼
Agent knows exactly how to use it
    │
    ▼
Consistent, working code
```

---

## Level 1: External (OSS)

**For**: Developers who use our packages

**Question**: "How do I use this package?"

**Location**: Each package has its own `docs/` folder

```
@nesalia/auth/
├── README.md              # Quick start + overview
├── installation.md       # How to install
├── guides/               # How to do specific things
│   ├── oauth.md
│   └── session.md
├── api/                   # Complete API reference
│   ├── authenticate.md
│   └── create-user.md
└── examples/              # Copy-paste examples
```

### What It Contains

- Installation instructions
- Quick start (5 minutes)
- Common use cases with examples
- Complete API reference
- Troubleshooting
- Migration guides (if applicable)

### Quality Standard

Must be as good as:
- Next.js documentation
- React documentation
- Stripe documentation

---

## Level 2: Internal

**For**: Developers who work on the packages themselves

**Question**: "How does this work internally?"

**Location**: Each package has `docs/internals/`

```
@nesalia/auth/
├── docs/
│   ├── internals/
│   │   ├── architecture.md     # How it works
│   │   ├── auth-flow.md        # Detailed flow
│   │   ├── security.md         # Security considerations
│   │   └── contributing.md     # How to contribute
```

### What It Contains

- Architecture explanation
- Code flow diagrams
- Security considerations
- Testing approach
- How to add new features
- Release process

### Quality Standard

A new developer should be able to:
- Understand the codebase
- Fix bugs
- Add new features
- Not break existing functionality

---

## Level 3: Agent

**For**: AI agents working on any project

**Question**: "What exists and how do I use it?"

**Location**: Central catalog in `docs/packages/`

```
docs/
├── packages/
│   ├── auth.md            # Complete agent doc
│   ├── database.md
│   └── ui.md
```

### What It Contains

Every agent document must include:

```markdown
# @nesalia/auth

## Overview
One sentence describing what this package does.

## Exports

### authenticate(credentials)
- Input: `{ email: string, password: string }`
- Output: `Result<User, AuthError>`
- Errors: `invalid_credentials | user_not_found | account_locked`

### createUser(data)
- Input: `{ email: string, password: string, name?: string }`
- Output: `Result<User, CreateUserError>`
- Errors: `email_taken | invalid_email | weak_password`

## Options/Config

### auth(config)
```typescript
type AuthConfig = {
  jwtSecret: string
  jwtExpiry: number        // default: 3600
  providers: Provider[]    // default: ['email']
  callbacks: {
    onLogin?: (user) => void
    onLogout?: (user) => void
  }
}
```

## Dependencies
- Uses: `@nesalia/crypto` for password hashing
- Uses: `@nesalia/jwt` for tokens

## Related Packages
- `@nesalia/db` - User storage
- `@nesalia/ui` - Auth UI components
```

### Quality Standard

An agent should be able to:
- Discover this package exists
- Know every function and its signature
- Understand all configuration options
- Know error types
- Understand how it connects to other packages

---

## The Central Catalog

The central catalog is the **single source of truth** for all agents.

### Structure

```
docs/
├── index.md                # "All available packages"
├── packages/              # Agent docs (one per package)
│   ├── auth.md
│   ├── database.md
│   ├── ui.md
│   └── ...
└── architecture/          # How packages connect
    └── overview.md
```

### index.md Template

```markdown
# Available Packages

All packages developed by Nesalia.

## Authentication

| Package | Description | Version |
|---------|-------------|---------|
| `@nesalia/auth` | Complete auth solution | 1.0.0 |
| `@nesalia/crypto` | Password hashing | 1.0.0 |

## Database

| Package | Description | Version |
|---------|-------------|---------|
| `@nesalia/db` | Database ORM | 1.0.0 |
| `@nesalia/migrations` | Migration tools | 1.0.0 |

## UI

| Package | Description | Version |
|---------|-------------|---------|
| `@nesalia/ui` | Component library | 1.0.0 |
| @nesalia/forms | Form components | 1.0.0 |

---

For detailed documentation, see [Packages](./packages/)
```

---

## Package Connection Documentation

Agents need to understand how packages work together:

```markdown
# Architecture Overview

## Package Dependencies

```
@nesalia/auth
    │
    ├── uses @nesalia/crypto
    ├── uses @nesalia/jwt
    └── uses @nesalia/db (optional)

@nesalia/ui
    │
    └── uses @nesalia/forms
```

## Data Flow

1. User submits form (UI)
2. Form validation (forms)
3. Auth check (auth)
4. Database operation (db)
5. Response to user

## Common Patterns

- All packages return `Result<T, E>`
- All packages use TypeScript
- Configuration via environment variables
```

---

## Summary

| Level | Audience | Location | Purpose |
|-------|----------|----------|---------|
| External | Package users | Package `docs/` | How to use |
| Internal | Package developers | Package `docs/internals/` | How it works |
| Agent | AI agents | Central `docs/packages/` | Everything needed |

The central catalog is the key: it allows any agent on any project to discover and use all packages.

---

*Document version: 1.0*
*Last updated: 2026-03-06*
