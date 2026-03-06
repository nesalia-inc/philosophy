# Documentation Standards

How to create and maintain documentation.

---

## Why Documentation Matters

| Without Docs | With Docs |
|--------------|------------|
| "How does this work?" | Read the docs |
| Tribal knowledge | Shared knowledge |
| Onboarding takes weeks | Onboarding takes days |
| Same questions repeated | Self-service |

---

## Documentation Types

### 1. README

**Purpose**: Quick start and overview

**Location**: `README.md` (root)

**Contents**:

```markdown
# Project Name

One sentence describing what this project does.

## Quick Start

\`\`\`bash
npm install
npm run dev
\`\`\`

## Features

- Feature 1
- Feature 2

## API

- `/api/users` - Manage users
- `/api/auth` - Authentication

## Tech Stack

- Next.js 14
- TypeScript
- Drizzle ORM

## Environment Variables

\`\`\`
DATABASE_URL=...
AUTH_SECRET=...
\`\`\`

## Contributing

[Link to contribution guidelines]

## License

MIT
```

### 2. Feature Documentation

**Purpose**: Deep dive into specific features

**Location**: `docs/features/feature-name.md`

**Contents**:

```markdown
# Feature Name

## Overview
What this feature does and why.

## Usage

\`\`\`typescript
import { featureName } from './feature'

const result = featureName(input)
\`\`\`

## API

### functionName(input)
- `input`: Description

Returns: Description

## Examples

### Basic
\`\`\`
\`\`\`

### Error Cases
\`\`\`
\`\`\`
```

### 3. Architecture Documentation

**Purpose**: How the system works

**Location**: `docs/architecture.md`

**Contents**:

```markdown
# Architecture

## Overview
System-level description.

## Components

### Component 1
Description and responsibilities.

### Component 2
Description and responsibilities.

## Data Flow

1. User action
2. API receives request
3. Process
4. Database
5. Response
```

---

## Code Documentation

### When to Document

Document **complex** code, not simple code:

```typescript
// ❌ BAD: Obvious, no docs needed
const add = (a: number, b: number): number => a + b

// ✅ GOOD: Complex logic needs docs
/**
 * Calculates compound interest with monthly contributions.
 *
 * @param principal - Initial amount
 * @param rate - Annual interest rate (e.g., 0.05 for 5%)
 * @param years - Number of years
 * @param monthlyContribution - Amount added each month
 *
 * @returns Total amount after compounding
 */
const compoundInterest = (principal, rate, years, monthlyContribution) => {
  // Complex calculation
}
```

### Type Documentation

Document complex types:

```typescript
/**
 * Result of a database query operation.
 *
 * @typeParam T - The successfully returned data type
 * @typeParam E - The error type
 */
type QueryResult<T, E = Error> =
  | { ok: true; value: T }
  | { ok: false; error: E }
```

---

## Documentation Structure

### Repository Structure

```
docs/
├── README.md                 # Index
├── getting-started.md       # Quick start
├── architecture/            # System architecture
│   ├── overview.md
│   └── data-flow.md
├── features/               # Feature docs
│   ├── authentication.md
│   └── payments.md
├── api/                    # API reference
│   ├── endpoints.md
│   └── authentication.md
├── guides/                 # How-to guides
│   └── deployment.md
└── contributing.md         # Contribution guide
```

---

## Writing Guidelines

### Be Concise

```markdown
❌ BAD:
In order to properly configure the application, you will need to set
the following environment variables in your .env file. This is required
for the application to function correctly.

✅ GOOD:
Set these environment variables in `.env`:
```

### Use Code Examples

```markdown
✅ GOOD:
Create a user:

\`\`\`typescript
const user = await createUser({
  email: 'test@example.com',
  name: 'Test User'
})
\`\`\`
```

### Keep Updated

- Update docs when code changes
- Remove outdated info
- Version significant changes

---

## Documentation Tools

### Tools to Consider

| Tool | Use Case |
|------|----------|
| GitHub Markdown | Basic docs |
| VitePress | Static sites |
| Docusaurus | Complex docs |
| TypeDoc | API docs |
| Storybook | Component docs |

### API Documentation

Use tools that read code:

```typescript
// TypeDoc generates docs from code
/**
 * Get user by ID
 * @param id - User ID
 * @returns User or null
 */
async function getUser(id: string): Promise<User | null>
```

---

## README Template

Use this template for new projects:

```markdown
# Project Name

[One sentence describing the project]

## Quick Start

\`\`\`bash
# Install
npm install

# Development
npm run dev

# Production
npm run build
npm start
\`\`\`

## Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| DATABASE_URL | PostgreSQL connection | Yes |
| API_SECRET | Secret for API | Yes |

## Features

- Feature 1
- Feature 2

## API

### GET /api/resource
Description

## Development

\`\`\`bash
# Run tests
npm test

# Lint
npm run lint
\`\`\`

## License

MIT
```

---

## Quick Reference

| Type | Location | When Updated |
|------|----------|--------------|
| README | Root | New features, changes |
| Feature docs | `docs/features/` | Feature development |
| API docs | `docs/api/` | Endpoint changes |
| Architecture | `docs/architecture.md` | System changes |
| Contributing | CONTRIBUTING.md | Process changes |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
