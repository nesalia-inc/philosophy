# Feature Development Workflow

How to develop features from issue to production - the complete workflow.

---

## Overview

```
ISSUE ──► PLAN ──► CODE ──► TESTS ──► REVIEW ──► PR ──► MERGE ──► RELEASE
  │        │        │        │         │        │        │         │
  ▼        ▼        ▼        ▼         ▼        ▼        ▼         ▼
What?  How?   Imple-  Verif-  Validate Auto-   Inte-   Version    Live
                 ment   ication         matique grate  dated
```

---

## Step 1: Issue Creation

Before any code is written, create a properly structured issue.

See: [Issue Management](./issue-management.md)

### Quick Issue Template

```markdown
## Description
[One sentence describing what needs to be built]

## Acceptance Criteria
- [ ] User can [action]
- [ ] System returns [expected result]
- [ ] Error case [handled]

## Technical Notes
[Links, context, constraints]
```

---

## Step 2: Planning

### Agent Task

When assigned an issue, the AI agent should:

1. **Read the project context**
   - `.ai/rules.md` - Coding standards
   - `.ai/prompts/new-feature.md` - Workflow
   - Recent PRs for patterns

2. **Understand the codebase**
   - Find related code
   - Identify patterns to follow

3. **Plan the implementation**
   - List files to create/modify
   - Identify dependencies
   - Plan tests

4. **Write a plan in a comment**

### Plan Template

```markdown
## Implementation Plan

### Files to Create
- `src/features/auth/types.ts`
- `src/features/auth/service.ts`

### Files to Modify
- `src/index.ts` - Export new module
- `src/db/schema.ts` - Add tables

### Tests to Add
- `tests/auth/login.test.ts`
- `tests/auth/logout.test.ts`

### Approach
1. Define types first
2. Implement core logic
3. Add error handling
4. Write tests
```

---

## Step 3: Implementation

### Coding Standards

All code must follow `.ai/rules.md`:

```typescript
// ❌ BAD: Using any
const process = (data: any): any => { }

// ✅ GOOD: Specific types
type UserInput = { email: string; password: string }
type AuthResult = Result<User, AuthError>

const authenticate = (input: UserInput): AuthResult => { }
```

### File Organization

```
src/
├── features/           # Feature-based organization
│   └── feature-name/
│       ├── types.ts   # Types and interfaces
│       ├── service.ts # Business logic
│       ├── api.ts     # API routes (if applicable)
│       └── index.ts   # Public exports
├── shared/            # Shared utilities
│   ├── db/           # Database
│   ├── utils/        # Helper functions
│   └── types/        # Common types
└── index.ts          # Main exports
```

### Naming Conventions

```typescript
// ❌ BAD: Ambiguous names
const process()
const createUser()
const config()

// ✅ GOOD: Explicit names
const authenticateUser()
const user = (data: UserData): User
const databaseConfig()
```

---

## Step 4: Testing

### Test Structure

```
tests/
├── features/
│   └── feature-name/
│       ├── service.test.ts
│       └── integration.test.ts
├── shared/
│   └── utils/
│       └── helper.test.ts
└── fixtures/
    └── mock-data.ts
```

### Test Coverage Requirements

- **Unit tests**: Core business logic
- **Integration tests**: Database operations
- **Error cases**: All error paths

### Test Example

```typescript
import { describe, it, expect } from 'vitest'
import { authenticateUser } from './service'

describe('authenticateUser', () => {
  it('returns user on valid credentials', () => {
    const result = authenticateUser({
      email: 'test@example.com',
      password: 'correct-password'
    })

    expect(result.ok).toBe(true)
    expect(result.value.email).toBe('test@example.com')
  })

  it('returns error on invalid credentials', () => {
    const result = authenticateUser({
      email: 'test@example.com',
      password: 'wrong-password'
    })

    expect(result.ok).toBe(false)
    expect(result.error.type).toBe('invalid_credentials')
  })
})
```

---

## Step 5: Code Review

Before creating a PR, ensure:

1. **Self-review completed**
   - All acceptance criteria met
   - Tests pass locally
   - No lint errors

2. **Documentation updated**
   - README if needed
   - API docs if public API changed

See: [Code Review Guidelines](./code-review.md)

---

## Step 6: Pull Request

See: [Pull Request Process](./pull-request-process.md)

### PR Checklist

- [ ] Code follows `.ai/rules.md`
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No lint errors
- [ ] TypeScript compiles
- [ ] All tests pass
- [ ] CI pipeline green

---

## Step 7: Merge

After approval:

1. **Squash and merge** (preferred)
2. **Delete branch** (cleanup)
3. **Issue automatically closed**

---

## Step 8: Release

See: [Release Process](./release-process.md)

---

## Complete Example

### Issue

```
Title: Add password reset feature should
Description: Users be able to reset their password via email
```

### Agent Response

```markdown
## Implementation Plan

### Files to Create
- `src/features/auth/password-reset/types.ts`
- `src/features/auth/password-reset/service.ts`
- `src/features/auth/password-reset/email.ts`
- `tests/features/auth/password-reset.test.ts`

### Files to Modify
- `src/features/auth/index.ts` - Export new module

### Approach
1. Define types (PasswordResetRequest, PasswordResetToken)
2. Implement generateResetToken() service
3. Implement sendResetEmail()
4. Implement validateToken() and resetPassword()
5. Add tests for all functions
```

### After Implementation

```bash
# All checks pass
npm run ci
# ✓ lint passed
# ✓ typecheck passed
# ✓ 12 tests passed
# ✓ build succeeded

# PR created with description
# CI runs automatically
# Code review requested
```

---

## Troubleshooting

### Agent Stuck

If the agent can't proceed:

1. **Clarify the requirements** - Provide more context
2. **Point to similar features** - Link existing code
3. **Break down the task** - Split into smaller steps

### Tests Failing

1. **Read the error** - Understand what's broken
2. **Fix the implementation** - Not the test
3. **Check edge cases** - Null, undefined, errors

### Code Review Blocked

1. **Address each comment** - Don't ignore
2. **Reply to questions** - Explain your logic
3. **Request re-review** - After changes

---

*Document version: 1.0*
*Last updated: 2026-03-06*
