# Code Review Guidelines

How to conduct effective code reviews.

---

## Why Code Review Matters

Code review is the last line of defense before code enters the codebase.

| Without Review | With Review |
|----------------|-------------|
| Bugs ship to production | Issues caught early |
| Inconsistent code | Consistent standards |
| Knowledge stays siloed | Team knowledge sharing |
| Technical debt grows | Quality maintained |

---

## Review Checklist

### Code Quality

- [ ] **Follows .ai/rules.md** - All 24 rules applied
- [ ] **No `any` types** - Specific types only
- [ ] **Proper error handling** - Uses Result/Option
- [ ] **Clear naming** - Explicit, descriptive names
- [ ] **Single responsibility** - Functions do one thing
- [ ] **DRY** - No code duplication

### Functionality

- [ ] **Works as intended** - Meets acceptance criteria
- [ ] **Edge cases handled** - Null, undefined, errors
- [ ] **Security** - No vulnerabilities
- [ ] **Performance** - No obvious issues

### Testing

- [ ] **Tests included** - Unit + integration
- [ ] **Edge cases tested** - Error paths
- [ ] **Tests pass** - CI green
- [ ] **Coverage acceptable** - Critical paths covered

### Documentation

- [ ] **README updated** - If public API changed
- [ ] **Code comments** - Complex logic explained
- [ ] **Types documented** - Complex types have comments

---

## Review Types

### Quick Review (< 30 min)

For small changes:

- Bug fixes
- Documentation
- Small features
- Dependency updates

**Focus**: Does it work? Is it safe?

### Deep Review (> 30 min)

For large changes:

- New features
- Complex refactoring
- Security changes
- Architecture changes

**Focus**: Everything in checklist

---

## How to Review

### Step 1: Understand the Context

1. Read the PR description
2. Check linked issues
3. Understand what problem is solved

### Step 2: Check CI Status

```
GitHub PR → Checks → All green?
                               │
                    ┌──────────┴──────────┐
                    ▼                     ▼
                   YES                    NO
                    │                     │
                    ▼                     ▼
              Continue            Request fixes
              review               (don't review broken code)
```

### Step 3: Read the Code

1. **Files changed** - Review each file
2. **New files** - Full review
3. **Modified files** - Diff review
4. **Deleted files** - Ensure safe

### Step 4: Test Locally (Optional but Recommended)

```bash
# Pull the branch
git fetch origin
git checkout feature/branch-name

# Run tests
npm run ci

# Manual testing
npm run dev
# Test the feature
```

### Step 5: Leave Feedback

Use GitHub's review features:

```markdown
# APPROVE - All good, ship it!
✅ Approved

# COMMENT - Notes, not blocking
📝 Comment

# REQUEST CHANGES - Needs fixes
🔧 Changes requested
```

---

## Giving Good Feedback

### Be Specific

```markdown
❌ BAD:
"This function is bad"

✅ GOOD:
"Line 42: This function is doing 3 things.
Consider splitting into:
- validateInput()
- processData()
- saveResult()"
```

### Suggest Solutions

```markdown
❌ BAD:
"This is wrong"

✅ GOOD:
"Consider using a lookup table instead of if/else:

const STATUS_MAP = {
  [Status.Pending]: 'pending',
  [Status.Active]: 'active',
  [Status.Completed]: 'completed'
}

This is more maintainable."
```

### Praise Good Code

```markdown
✅ GOOD:
"Nice use of Result monad here!
Clean error handling."
```

---

## Common Issues to Look For

### Types

```typescript
// ❌ BAD: any
const data: any = getData()

// ✅ GOOD: Specific type
const data: UserData = getData()
```

### Error Handling

```typescript
// ❌ BAD: Throwing exceptions
if (!user) throw new Error('not found')

// ✅ GOOD: Returning Result
if (!user) return Result.err({ type: 'not_found' })
return Result.ok(user)
```

### Naming

```typescript
// ❌ BAD: Ambiguous
const process()
const config()

// ✅ GOOD: Explicit
const authenticateUser()
const databaseConfig()
```

### Functions

```typescript
// ❌ BAD: God function
const handleRequest = (req) => {
  // validate
  // process
  // save
  // respond
  // log
}

// ✅ GOOD: Single responsibility
const validateRequest = (req) => ValidationResult
const processRequest = (validated) => ProcessedData
const saveData = (processed) => SaveResult
```

### Duplication

```typescript
// ❌ BAD: Copy-paste
if (type === 'text') { /* ... */ }
if (type === 'number') { /* ... */ }
if (type === 'boolean') { /* ... */ }

// ✅ GOOD: Lookup table
const HANDLERS = {
  text: handleText,
  number: handleNumber,
  boolean: handleBoolean
}
```

---

## For AI Agents: How to Respond to Reviews

### When Changes Requested

1. **Read all comments carefully**
2. **Don't take it personally** - It's about the code
3. **Make fixes**
4. **Reply to each comment** - "Fixed in commit X"
5. **Push and re-request review**

### Example Response

```markdown
Thanks for the review!

Fixed in 3a2b1c:
- Split `processUser` into separate functions (comment 1)
- Added Result return type instead of throwing (comment 2)
- Renamed `config` to `databaseConfig` (comment 3)

Remaining question:
- Line 45: I kept the approach because X - let me know if you want me to change it.
```

---

## Review Metrics

Track reviews to improve:

| Metric | Target |
|--------|--------|
| Time to first review | < 24 hours |
| Time to merge | < 48 hours |
| Review rounds | < 3 |
| CI re-runs | < 1 |

---

## Quick Reference

| Action | When |
|--------|------|
| **Approve** | All checks pass, looks good |
| **Comment** | Notes, questions, suggestions |
| **Request changes** | Must fix before merge |
| **Approve with suggestions** | Minor improvements, optional |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
