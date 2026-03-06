# Agent Configuration

Documentation written for AI agents.

---

## Why Agents Need Different Docs

| Humans | Agents |
|--------|--------|
| Can ask questions | Can't clarify |
| Learn by doing | Need complete context |
| Understand ambiguity | Interpret literally |
| Skip sections | Follow exactly |

---

## The .ai/ Directory

Every project should have:

```
.ai/
├── rules.md           # Coding rules (the law)
├── prompts/           # Workflow instructions
│   ├── new-feature.md
│   ├── bug-fix.md
│   └── review.md
└── context.md         # Project overview
```

---

## rules.md

The single source of truth for code quality.

### Structure

```markdown
# Coding Rules

## 1. Rule Name
Description with examples

## 2. Rule Name
Description with examples
```

### Example Rules

```markdown
## 1. No any Types

Never use \`any\` or \`unknown\`. Use specific types.

✅ GOOD:
\`\`\`typescript
type User = { id: string; email: string }
const getUser = (id: string): Result<User, NotFound> => ...
\`\`\`

❌ BAD:
\`\`\`typescript
const data: any = getData()
\`\`\`

## 2. Errors Are Not Thrown

Use Result<T, E> for error handling.

✅ GOOD:
\`\`\`typescript
if (!user) return Result.err({ type: 'not_found' })
return Result.ok(user)
\`\`\`

❌ BAD:
\`\`\`typescript
if (!user) throw new Error('not found')
\`\`\`
```

### Required Rules

Your rules.md must include:

1. No `any` types
2. Result/Option for errors
3. Single responsibility
4. Explicit naming
5. No god functions
6. Test requirements

---

## prompts/

Workflow instructions for different tasks.

### new-feature.md

```markdown
# New Feature Prompt

You are implementing a new feature.

## Steps

1. Read context
   - Read \`.ai/rules.md\`
   - Read \`.ai/context.md\`
   - Read existing similar code

2. Plan
   - Write implementation plan
   - List files to create/modify
   - Plan tests

3. Implement
   - Follow \`.ai/rules.md\` exactly
   - Write tests alongside code

4. Verify
   - Run \`npm run ci\`
   - Fix any issues

5. Create PR
   - Fill PR template
   - Link to issue
```

### bug-fix.md

```markdown
# Bug Fix Prompt

You are fixing a bug.

## Steps

1. Understand
   - Read issue description
   - Reproduce if possible
   - Identify root cause

2. Fix
   - Fix the issue
   - Add test to prevent regression

3. Verify
   - Run \`npm run ci\`
   - Ensure no new issues
```

### review.md

```markdown
# Code Review Prompt

You are reviewing code.

## Checklist

- [ ] Follows \`.ai/rules.md\`
- [ ] Tests included
- [ ] No obvious bugs
- [ ] Security considerations

## Process

1. Read the PR description
2. Check CI status
3. Review each changed file
4. Leave specific feedback
5. Approve or request changes
```

---

## context.md

Project overview for agents.

### Template

```markdown
# Project Context

## Name
[Project name]

## What This Does
[One sentence]

## Tech Stack
- Frontend: [tech]
- Backend: [tech]
- Database: [tech]

## Key Patterns
- [Pattern 1]
- [Pattern 2]

## Important Files
- \`src/features/\` - Feature modules
- \`src/lib/\` - Utilities
- \`tests/\` - Test files

## Conventions
- File naming: [convention]
- Test naming: [convention]
- Import ordering: [convention]
```

---

## Agent Workflow

### Starting a Task

1. Read `.ai/rules.md` ← MANDATORY
2. Read `.ai/context.md`
3. Read relevant prompt
4. Understand codebase
5. Plan
6. Implement
7. Test
8. Verify (CI)
9. Create PR

### What Agents Must NOT Do

- Start coding before reading rules
- Use `any` types
- Throw errors
- Create god functions
- Skip tests

---

## Quality Indicators

### Good Agent Config

- Rules are explicit (no interpretation needed)
- Examples are concrete
- Context is complete
- Workflows are step-by-step

### Bad Agent Config

- "Use best practices"
- "Follow existing patterns"
- Incomplete context
- Ambiguous instructions

---

## Quick Reference

| File | Purpose | Updated When |
|------|---------|--------------|
| `.ai/rules.md` | Code quality rules | Coding standards change |
| `.ai/prompts/new-feature.md` | Feature workflow | Process changes |
| `.ai/prompts/bug-fix.md` | Bug fix workflow | Process changes |
| `.ai/context.md` | Project overview | Project structure changes |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
