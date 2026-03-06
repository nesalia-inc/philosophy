# Internal Documentation

Documentation for developers who work on the packages themselves.

---

## Purpose

**Question**: "How does this work internally?"

**Audience**: Internal developers who modify, fix, or extend the packages.

---

## Structure

Each package should have:

```
@nesalia/package-name/
├── docs/
│   └── internals/
│       ├── architecture.md     # How it works
│       ├── code-flow.md       # Detailed execution flow
│       ├── security.md        # Security considerations
│       ├── testing.md         # How to test
│       └── contributing.md   # How to add features
```

---

## architecture.md Template

```markdown
# Architecture

## Overview

[One paragraph describing the overall architecture]

## Components

### Component 1

Description of what this component does.

```typescript
// Location: src/component-1.ts
```

### Component 2

Description of what this component does.

## Data Flow

```
User Action
    │
    ▼
API Request
    │
    ▼
Validation
    │
    ▼
Business Logic
    │
    ▼
Database
    │
    ▼
Response
```

## Key Decisions

| Decision | Rationale |
|----------|-----------|
| Using X | Because Y |
| Not using Z | Because W |
```

---

## code-flow.md Template

```markdown
# Code Flow

Detailed explanation of how code executes.

## Authentication Flow

### Step 1: Request Received

```typescript
// src/api/auth.ts
export const authenticate = (req: Request) => {
  // 1. Extract credentials
  const credentials = req.body
  // 2. Validate input
  // 3. Check password
  // 4. Generate token
  // 5. Return response
}
```

### Step 2: Password Verification

```typescript
// src/lib/password.ts
export const verifyPassword = async (password, hash) => {
  // Uses bcrypt
  // Returns boolean
}
```

### Step 3: Token Generation

```typescript
// src/lib/token.ts
export const generateToken = (user) => {
  // Signs JWT
  // Returns token string
}
```

## Key Files

| File | Responsibility |
|------|---------------|
| `src/api/` | HTTP handlers |
| `src/lib/` | Core logic |
| `src/db/` | Database operations |
```

---

## security.md Template

```markdown
# Security Considerations

## Authentication

- Passwords hashed with bcrypt (cost factor 12)
- JWT tokens expire after 1 hour
- Refresh tokens rotated on use

## Authorization

- Role-based access control (RBAC)
- Permissions checked at service layer

## Data Protection

- SQL injection prevented via parameterized queries
- XSS prevented via output encoding
- CSRF tokens on all state-changing operations

## Secrets

- Never commit secrets to git
- Use environment variables
- Rotate secrets regularly
```

---

## testing.md Template

```markdown
# Testing Guide

## Test Structure

```
tests/
├── unit/           # Unit tests
├── integration/    # Integration tests
└── fixtures/      # Test data
```

## Running Tests

```bash
# All tests
npm test

# Unit only
npm run test:unit

# With coverage
npm run test:coverage
```

## Writing Tests

### Unit Test Example

```typescript
describe('authenticate', () => {
  it('returns user on valid credentials', async () => {
    const result = await authenticate({
      email: 'test@example.com',
      password: 'password123'
    })

    expect(result.ok).toBe(true)
    expect(result.value.email).toBe('test@example.com')
  })
})
```

### Integration Test Example

```typescript
describe('auth API', () => {
  it('authenticates user', async () => {
    const response = await request(app)
      .post('/api/auth/login')
      .send({ email: 'test@example.com', password: 'password123' })

    expect(response.status).toBe(200)
    expect(response.body.token).toBeDefined()
  })
})
```

## Mocking

Use `vitest.mock()` for external dependencies:

```typescript
vi.mock('@nesalia/db', () => ({
  db: {
    users: {
      find: vi.fn()
    }
  }
}))
```
```

---

## contributing.md Template

```markdown
# Contributing Guide

## Getting Started

1. Fork the repository
2. Clone your fork
3. Install dependencies: `npm install`
4. Run tests: `npm test`

## Development Workflow

```bash
# Create feature branch
git checkout -b feature/my-feature

# Make changes
# Write tests

# Run tests
npm test

# Commit
git commit -m "feat: add my feature"

# Push
git push origin feature/my-feature
```

## Adding a New Feature

### Step 1: Define Types

Add types in `src/types.ts`:

```typescript
export type NewFeatureInput = {
  field1: string
  field2: number
}

export type NewFeatureError =
  | { type: 'invalid_input' }
  | { type: 'database_error' }
```

### Step 2: Implement Logic

Add function in `src/features/new-feature.ts`:

```typescript
export const newFeature = (input: NewFeatureInput):
  Result<Output, NewFeatureError> => {
  // Implementation
}
```

### Step 3: Add Tests

Add tests in `tests/features/new-feature.test.ts`

### Step 4: Update Documentation

- Update API reference
- Add example if needed

## Release Process

1. Update version: `npm version minor`
2. Update changelog
3. Create GitHub release
4. Publish to npm

## Code Style

Follow `.ai/rules.md`:
- No `any` types
- Use Result<T, E> for errors
- Single responsibility functions
- Explicit naming
```

---

## Quality Checklist

- [ ] New developer can understand architecture
- [ ] Code flow is documented
- [ ] Security considerations explained
- [ ] Testing approach is clear
- [ ] Contributing guide is complete
- [ ] Can add new feature without asking questions

---

*Document version: 1.0*
*Last updated: 2026-03-06*
