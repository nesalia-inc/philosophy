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

### Impact Analysis

#### What Might This Conflict With?
- [ ] Existing auth system
- [ ] API rate limiting
- [ ] Database schema
- [ ] Other features using similar patterns

#### What Depends on This?
- [ ] Feature X (uses permissions)
- [ ] Feature Y (checks roles)

#### What Could Go Wrong?
1. Conflicts with existing auth → How to check?
2. Database migration needed → Is there one?
3. Breaks existing features → Which ones to test?

#### Similar Existing Features
- Look at: `src/features/auth/service.ts`
- Look at: `src/features/roles/service.ts`

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

## Why Impact Analysis Matters

The brain cannot see all interactions at once. You think feature by feature, but the system is a network:

```
Feature A ───┬───► Feature B
             │
             └──► Feature C
                   │
                   └──► Feature D
```

You don't see that B, C and D exist or how they interact until you start coding.

**Impact Analysis prevents**:
- "Wait, this conflicts with existing roles system"
- "Wait, this doesn't work with API rate limiting"
- "Wait, I need to change the database schema"

---

## Impact Analysis Checklist

Before approving any plan, answer these questions:

### 1. What Might This Conflict With?

| Question | Why It Matters |
|----------|----------------|
| Does it use existing X? | Don't duplicate logic |
| Does it modify existing Y? | Could break existing features |
| Does it use similar patterns? | Could cause confusion |

### 2. What Depends on This?

| Question | Why It Matters |
|----------|----------------|
| What features use this? | Need to update them too |
| What will break if I change this? | Regression testing needed |

### 3. What Could Go Wrong? (Pre-mortem)

Ask: "If this fails, why?"

| Scenario | Prevention |
|----------|------------|
| Conflicts with auth | Check existing auth code first |
| Database migration needed | Plan migration upfront |
| Breaks existing features | List features to test |

### 4. Similar Existing Features

| Question | Why It Matters |
|----------|----------------|
| Is there similar code? | Don't reinvent |
| How did they handle X? | Follow existing patterns |

---

## Impact Analysis Example

### Before (No Impact Analysis)

```
## Plan

### What
Add permissions system.

### Files to Create
- src/features/permissions/service.ts
```

**Result**: During implementation, discover it conflicts with roles system → rework.

### After (With Impact Analysis)

```
## Plan

### What
Add permissions system.

### Files to Create
- src/features/permissions/service.ts

### Impact Analysis

#### What Might This Conflict With?
- [x] Existing roles system (src/features/roles/)
  - Solution: Permissions extend roles, not replace
- [ ] API rate limiting
  - No conflict expected

#### What Depends on This?
- Billing feature uses roles → needs to check permissions too

#### What Could Go Wrong?
1. Conflicts with roles → Check src/features/roles/ first
2. Migration needed → Include migration in plan

#### Similar Existing Features
- Look at: src/features/roles/service.ts (for patterns)
```

**Result**: Before coding, you know the constraints → no rework.

---

## How to Use This as a Checklist

For every plan, fill this out:

```
## Impact Analysis

### 1. Conflicts
What existing features might conflict?
- [ ] Feature A: reason
- [ ] Feature B: reason

### 2. Dependencies
What depends on this?
- [ ] Feature X: will need update

### 3. Risks
What could go wrong?
1. X → Prevention: Y
2. A → Prevention: B

### 4. Similar
Existing code to reference:
- src/features/X
- src/features/Y
```

If you can't answer these → don't approve the plan yet.

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

### Impact Analysis Checklist

- [ ] **Conflicts** identified - what might conflict?
- [ ] **Dependencies** mapped - what depends on this?
- [ ] **Risks** listed - what could go wrong?
- [ ] **Similar** code referenced - existing code to look at

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

### Impact Analysis

#### What Might This Conflict With?
- [x] Existing sessions (src/lib/sessions.ts)
  - Solution: Use JWT, keep sessions for non-API
- [ ] Database schema - Users table exists, no conflict

#### What Depends on This?
- [ ] Dashboard (checks user login) - will use new auth
- [ ] API routes (already use JWT) - no changes needed

#### What Could Go Wrong?
1. Conflicts with session system → Solution: JWT only for API
2. Token expiry issues → Solution: 1 hour expiry

#### Similar Existing Features
- Look at: src/lib/auth.ts (existing auth logic to reuse)

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
