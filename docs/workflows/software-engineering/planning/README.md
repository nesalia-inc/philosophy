# Planning System

How to create, store, and manage development plans.

---

## The Problem

Without a stored plan:

```
Discussion with Agent
    │
    │ Plan discussed
    │
    ▼
Discussion ends
    │
    │ Plan lost
    │
    ▼
Next time = start over
```

Every feature requires 2-4x rework because the agent doesn't know exactly what's expected.

---

## The Solution

Store the plan in the issue itself:

```
GitHub Issue #42: Add user auth
    │
    ├── Description: what needs to be done
    ├── Plan (comment): how to do it
    ├── Code (commits): implementation
    └── PR: verification
```

---

## Why Plans Matter

### Without Plan

```
Issue: "Add user auth"
   │
   ▼
Agent: "I'll add auth"
   │
   ▼
Agent makes assumptions about:
   - What "auth" means
   - How to implement it
   - What files to create
   │
   ▼
Agent produces code
   │
   ▼
You review: "That's not what I wanted"
   │
   ▼
Rework x 2-4 times
```

### With Plan

```
Issue: "Add user auth"
   │
   ▼
Plan approved by you (5-15 min)
   │
   ▼
Agent knows exactly what to do
   │
   ▼
Agent produces code
   │
   ▼
You review: "Perfect"
   │
   ▼
Done
```

---

## The Workflow

```
1. ISSUE created
       │
       ▼
2. YOU write PLAN (5-15 min)
       │
       ▼
3. YOU review and approve PLAN
       │
       ▼
4. AGENT executes PLAN
       │
       ▼
5. Agent creates PR
       │
       ▼
6. YOU review PR
       │
       ▼
7. Merge (no rework)
```

---

## Plan Format

### Template

```markdown
## Plan

### What
[One sentence describing what to build]

### Files to Create
- `src/features/auth/types.ts`    - User, AuthResult types
- `src/features/auth/service.ts` - authenticate(), createToken()
- `tests/features/auth.test.ts`

### Files to Modify
- `src/index.ts` - Export new module
- `src/db/schema.ts` - Add users table

### Dependencies
- `@nesalia/crypto` - Password hashing
- `@nesalia/jwt` - Token generation

### API Changes
- `POST /api/auth/login` - User login
- `POST /api/auth/logout` - User logout
- `GET /api/auth/me` - Get current user

### Acceptance Criteria
- [ ] User can login with email/password
- [ ] JWT token returned on login
- [ ] Protected routes require valid token
- [ ] Invalid credentials return error

### How to Test
1. Run `npm run dev`
2. POST /api/auth/login with {email, password}
3. Verify token in response
4. Test protected route with token
```

---

## Storage Location

### Recommended: GitHub Issue Comment

Store the plan as a comment in the issue:

```
Issue #42
    │
    └── Comment: "## Plan"
                  └ The complete plan
```

**Why**:
- Always accessible
- Searchable
- Version history
- Linked to the work

### Alternative: GitHub Project

Use a project board:

```
| To Plan | Planned | In Progress | Done |
|---------|---------|-------------|------|
| Issue A | Issue B | Issue C     |      |
```

---

## When to Use Plans

### Always Use Plans For

- New features
- Large refactors
- API changes
- Anything requiring 1+ day of work

### Can Skip For

- Quick fixes (< 1 hour)
- Documentation updates
- Small bug fixes

---

## Plan Checklist

Before approving a plan, ensure:

- [ ] **What** is clear - one sentence description
- [ ] **Files** listed - create and modify
- [ ] **Dependencies** identified - internal packages to use
- [ ] **API** defined - if applicable
- [ ] **Criteria** testable - how to verify success
- [ ] **Testing** documented - how to verify it works

---

## For Agents

### Agent Reading Order

When starting work, agents must:

1. Read the Issue description
2. Read the Plan (in comments)
3. Read `.ai/rules.md`
4. Read `.ai/context.md`

### Agent Rules

- NEVER start coding before reading the plan
- If plan is unclear, ask for clarification
- Follow the plan exactly
- If plan needs changes, propose in comment

---

## Quality Indicators

### Good Plan

- Clear description of what to build
- Specific files to create/modify
- Identified dependencies
- Testable acceptance criteria
- How to test documented

### Bad Plan

- Vague: "Add authentication"
- Missing files
- No acceptance criteria
- No testing instructions

---

## Examples

### Example: Good Plan

```
## Plan

### What
Add JWT-based user authentication with login/logout.

### Files to Create
- src/features/auth/types.ts    - User, AuthResult, LoginInput
- src/features/auth/service.ts - authenticate(), createToken(), verifyToken()
- src/features/auth/middleware.ts - auth middleware
- tests/features/auth.test.ts

### Files to Modify
- src/index.ts - Export auth module

### Dependencies
- @nesalia/crypto (bcrypt for passwords)
- @nesalia/jwt (token handling)

### API
- POST /api/auth/login - {email, password} → {token, user}
- POST /api/auth/logout - clears token
- GET /api/auth/me - returns current user

### Acceptance Criteria
- [ ] Login with valid credentials returns token
- [ ] Login with invalid credentials returns 401
- [ ] Protected routes return 401 without token
- [ ] Token expires after 1 hour

### How to Test
1. npm run dev
2. POST /api/auth/login
3. Verify response has token
4. GET /api/auth/me with token
5. Verify user data returned
```

### Example: Bad Plan

```
## Plan

Add user authentication.
```

---

## Troubleshooting

### Agent Asks "What should I do?"

```
Check the issue description AND the plan comment.
If unclear, ask for clarification before coding.
```

### Plan Changes During Implementation

```
1. Agent comments: "Plan needs update: [reason]"
2. You review and approve new plan
3. Agent continues with updated plan
```

### Plan vs Implementation Mismatch

```
1. Agent didn't follow the plan
2. Request changes - "Please follow the plan"
3. Agent corrects
```

---

## Summary

| Step | Who | Time |
|------|-----|------|
| 1. Create issue | Anyone | 5 min |
| 2. Write plan | You | 5-15 min |
| 3. Approve plan | You | 2 min |
| 4. Execute | Agent | varies |
| 5. Review PR | You | 10 min |

**Time saved**: 2-4x rework eliminated

---

*Document version: 1.0*
*Last updated: 2026-03-06*
