# Pull Request Process

How to create, submit, and manage pull requests.

---

## Overview

```
CODE COMPLETE ──► PR CREATED ──► REVIEW ──► CHANGES ──► APPROVED ──► MERGED
      │               │            │           │            │           │
      ▼               ▼            ▼           ▼            ▼           ▼
  Feature        Template     Feedback   Addressed   Approved   Integrated
  implemented    filled       given      comments    ✓          to main
```

---

## Creating a Pull Request

### PR Template

All repositories use `.github/PULL_REQUEST_TEMPLATE.md`:

```markdown
## Summary
<!-- Brief description of what this PR does -->

## Changes
<!-- List of specific changes -->

- Added `src/features/auth/service.ts`
- Modified `src/index.ts` to export new module

## Test Plan
<!-- How to test these changes -->

- [ ] Run `npm test` - all tests pass
- [ ] Test login flow manually
- [ ] Test error cases

## Checklist
- [ ] Follows .ai/rules.md
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No lint errors
- [ ] TypeScript compiles
- [ ] All CI checks pass
```

---

## Before Creating PR

### Self-Check

Run these locally before pushing:

```bash
# 1. Lint
npm run lint
# ✓ No errors

# 2. Type check
npm run typecheck
# ✓ No errors

# 3. Tests
npm run test
# ✓ 42 tests passed

# 4. Build
npm run build
# ✓ Build successful
```

### Complete CI Pipeline

```bash
# All checks combined
npm run ci
# ✓ lint passed
# ✓ typecheck passed
# ✓ tests passed
# ✓ build succeeded
```

---

## PR Best Practices

### Title Format

```
<type>: <description>

Examples:
feat: add user authentication
fix: resolve login redirect loop
docs: update API documentation
refactor: simplify database queries
chore: update dependencies
```

Types:
- `feat` - New feature
- `fix` - Bug fix
- `docs` - Documentation
- `refactor` - Code refactoring
- `test` - Tests
- `chore` - Maintenance

### Description

1. **What** does this PR do?
2. **Why** is it needed?
3. **How** does it work?

### Linking Issues

Close issues automatically:

```markdown
Closes #123
Fixes #456
```

---

## PR Workflow

### Step 1: Create Branch

```bash
# Create feature branch
git checkout -b feature/user-authentication

# Or for bug fixes
git checkout -b fix/login-redirect
```

### Step 2: Commit Changes

```bash
# Stage changes
git add -A

# Commit with descriptive message
git commit -m "feat: add user authentication

- Implement login/logout
- Add session management
- Add tests

Closes #42"
```

### Step 3: Push

```bash
# Push branch
git push origin feature/user-authentication
```

### Step 4: Create PR

1. Go to GitHub
2. Click "Compare & pull request"
3. Fill template
4. Request review

---

## Review Process

### For Reviewer

1. **Check CI status** - Must be green
2. **Read the changes** - Understand what was done
3. **Test locally** - Pull and run (optional but recommended)
4. **Review code** - Check for issues
5. **Leave feedback** - Clear, actionable comments

### Review Checklist

```
CODE QUALITY
[ ] Follows .ai/rules.md
[ ] No any types
[ ] Proper error handling (Result/Option)
[ ] Clear naming

TESTS
[ ] Tests included
[ ] Edge cases covered
[ ] Tests pass

DOCS
[ ] README updated (if needed)
[ ] Code comments (if complex)

SECURITY
[ ] No secrets exposed
[ ] Input validation
[ ] SQL injection prevention
```

---

## Addressing Feedback

### When Reviewer Comments

1. **Read all comments** - Understand everything
2. **Reply to questions** - Clarify your logic
3. **Make changes** - Fix what's needed
4. **Push updates** - `git add -A && git commit && git push`
5. **Re-request review** - Tag reviewer

### Handling Disagreements

If you disagree with feedback:

1. **Explain your reasoning** - Don't dismiss
2. **Provide context** - Show why you did it that way
3. **Discuss** - Reach understanding
4. **Compromise** - Find middle ground

---

## Merging

### When Approved

1. **Squash and merge** (preferred)

```
Before:
- feat: add login
- feat: add logout
- fix: typo

After (squashed):
- feat: add user authentication
```

2. **Delete branch** - Cleanup

3. **Close issue** - If linked

---

## Common Issues

### CI Failing

```bash
# Check what's failing
npm run ci

# Fix issues
# Lint errors? Fix them
# Type errors? Fix them
# Test failures? Fix implementation

# Push fixes
git add -A
git commit -m "fix: resolve lint errors"
git push
```

### Merge Conflicts

```bash
# Update main
git fetch origin
git checkout main
git pull

# Switch to branch
git checkout feature/your-branch

# Rebase or merge
git rebase main
# OR
git merge main

# Resolve conflicts
# Then:
git add -A
git commit -m "merge: resolve conflicts"
git push
```

---

## Quick Reference

| Action | Command |
|--------|---------|
| Create branch | `git checkout -b type/description` |
| Commit | `git commit -m "type: description"` |
| Push | `git push origin branch-name` |
| Update branch | `git rebase main` |
| Squash merge | (GitHub UI button) |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
