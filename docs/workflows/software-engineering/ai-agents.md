# AI Agent Integration

How to effectively work with AI agents across multiple repositories.

---

## The Model

```
YOU (Architect)
    │
    ├── "Add user auth to repo X"
    │
    ▼
┌─────────────────────────────────────┐
│         AGENT ON REPO X             │
│                                     │
│  1. Read project context           │
│  2. Read issue requirements        │
│  3. Plan implementation            │
│  4. Generate code                  │
│  5. Generate tests                 │
│  6. Create PR                      │
└─────────────────────────────────────┘
    │
    ▼
  PR for review
```

---

## The Problem

Without proper setup, each agent:

- Interprets requirements differently
- Writes code in different styles
- Makes different architectural choices
- Doesn't know project conventions

**Result**: Inconsistent code across 25 repositories.

---

## The Solution: Project-Born Context

Every repository contains everything an agent needs:

```
.repo/
├── .ai/                    # AI configuration
│   ├── rules.md            # Coding standards (24 rules)
│   ├── prompts/           # Workflow prompts
│   │   ├── new-feature.md
│   │   ├── bug-fix.md
│   │   └── review.md
│   └── context.md         # Project overview
├── .github/               # GitHub workflows
├── src/                   # Source code
└── docs/                  # Documentation
```

**When agent starts on any repo** → it reads `.ai/` and knows everything.

---

## AI Configuration Files

### .ai/rules.md

Your 24 coding rules:

```markdown
# Coding Rules

## 1. No any Types
Never use `any` or `unknown`. Use specific types.

✅ GOOD:
type User = { id: string; email: string }
const getUser = (id: string): Result<User, NotFound> => ...

❌ BAD:
const data: any = getData()
```

## 2. Errors Are Not Thrown
Use Result/Option for error handling.

... (all 24 rules)
```

### .ai/prompts/new-feature.md

Standard prompt for new features:

```markdown
# New Feature Prompt

You are implementing a new feature. Follow these steps:

## 1. Read Context
- Read `.ai/rules.md` - Understand coding standards
- Read `README.md` - Understand the project
- Read existing similar code - Follow patterns

## 2. Plan
Before coding, write an implementation plan:
- Files to create
- Files to modify
- Tests to add

## 3. Implement
Follow these rules:
- Use specific types, never `any`
- Return Result<T, E> for errors
- Name functions explicitly
- Keep functions single-purpose
- Write tests alongside code

## 4. Verify
Before submitting:
- Run `npm run ci` - All checks must pass
- Ensure tests cover edge cases
```

### .ai/prompts/bug-fix.md

Standard prompt for bug fixes:

```markdown
# Bug Fix Prompt

You are fixing a bug. Follow these steps:

## 1. Understand the Bug
- Read the issue description
- Reproduce locally if possible
- Identify root cause

## 2. Fix
- Fix the underlying issue
- Add test to prevent regression

## 3. Verify
- Run `npm run ci`
- Ensure no new issues introduced
```

### .ai/context.md

Project overview:

```markdown
# Project Context

## Project Name
[Name]

## What This Does
[One sentence]

## Tech Stack
- Frontend: Next.js 14, React, TypeScript
- Backend: Next.js API Routes
- Database: PostgreSQL + Drizzle ORM

## Key Patterns
- Collections pattern for database
- Result<T, E> for error handling
- Feature-based directory structure

## Important Files
- `src/features/` - Feature modules
- `src/db/` - Database configuration
- `src/lib/` - Utilities
```

---

## Agent Workflow

### Starting a New Task

```markdown
## Task
[Copy-paste the issue description]

## Instructions
1. Read .ai/rules.md
2. Read .ai/prompts/[feature/bug].md
3. Read .ai/context.md
4. Understand the codebase
5. Plan implementation
6. Implement
7. Test
8. Create PR
```

### What Agent Should Do

1. **Read context files** - `.ai/rules.md`, `.ai/context.md`
2. **Explore codebase** - Find similar implementations
3. **Write plan** - Share before coding
4. **Implement** - Follow rules
5. **Test** - Write tests
6. **Verify** - Run CI
7. **Create PR** - With proper description

### What Agent Should NOT Do

- Start coding without reading rules
- Use `any` types
- Throw errors (use Result)
- Create god functions
- Skip tests

---

## Trust Through Verification

### If CI Passes → Code is Valid

```
Agent pushes code
       │
       ▼
┌──────────────────┐
│   CI Pipeline    │
│                  │
│  1. Lint         │
│  2. TypeCheck   │
│  3. Tests       │
│  4. Build       │
└────────┬─────────┘
         │
         ▼
   ┌─────────┐
   │ ALL     │
   │ PASSED  │
   └────┬────┘
        │
        ▼
   ✓ Ready for review
```

### If CI Fails → Agent Fixes

```
Agent pushes code
       │
       ▼
┌──────────────────┐
│   CI Pipeline    │
│                  │
│  1. Lint       ✗ │
└────────┬─────────┘
         │
         ▼
   Agent receives error
         │
         ▼
   Agent fixes issues
         │
         ▼
   Agent re-runs CI
         │
         ▼
   ... (repeat until pass)
```

---

## Working with Multiple Agents

### Different Repos, Same Context

```
Agent A on Repo 1     Agent B on Repo 2     Agent C on Repo 3
     │                     │                     │
     ▼                     ▼                     ▼
Read .ai/rules.md    Read .ai/rules.md    Read .ai/rules.md
     │                     │                     │
     ▼                     ▼                     ▼
Follows same rules  Follows same rules  Follows same rules
     │                     │                     │
     ▼                     ▼                     ▼
Consistent code     Consistent code     Consistent code
```

### Managing Multiple Agents

Track what each agent is doing:

```markdown
## Active Agents

| Agent | Repository | Task | Status |
|-------|------------|------|--------|
| Agent-1 | repo-a | Auth feature | In Progress |
| Agent-2 | repo-b | Bug fix | Review |
| Agent-3 | repo-c | API endpoint | Done |
```

---

## Prompt Templates

### Initial Task

```markdown
## Context
You are working on [repo-name].

## Issue
[Copy issue content]

## Your Task
1. Read `.ai/rules.md`
2. Read `.ai/context.md`
3. Read the relevant prompt in `.ai/prompts/`
4. Implement the feature/fix the bug
5. Write tests
6. Run `npm run ci`
7. Create a PR

## Important
- Always follow .ai/rules.md
- Use Result<T, E> for error handling
- Never use `any` type
- Write tests for all new code
```

### Follow-up

```markdown
## Previous Context
[Reference the original task]

## New Request
[What needs to be changed]

## What Changed
- [List changes requested in review]
```

---

## Common Issues

### Agent Using Wrong Patterns

**Problem**: Agent doesn't follow rules.

**Solution**: Remind agent:

```markdown
Remember:
- Read .ai/rules.md
- Use specific types, not any
- Return Result for errors
- Keep functions single-purpose
```

### Agent Stuck

**Problem**: Agent can't proceed.

**Solution**: Provide more context:

```markdown
Look at these files for reference:
- src/features/auth/service.ts (similar pattern)
- src/features/users/api.ts (similar API structure)
```

### Agent Making Assumptions

**Problem**: Agent assumes requirements.

**Solution**: Be explicit:

```markdown
Requirements:
- [Exact requirement 1]
- [Exact requirement 2]
Do NOT assume anything beyond this.
```

---

## Quick Reference

| File | Purpose |
|------|---------|
| `.ai/rules.md` | 24 coding rules |
| `.ai/context.md` | Project overview |
| `.ai/prompts/new-feature.md` | Feature workflow |
| `.ai/prompts/bug-fix.md` | Bug fix workflow |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
