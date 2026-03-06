# Quick Fix Template

For work < 1 hour.

---

## Template

```markdown
## Plan

### What
[One sentence - what to fix]

### Solution
[How to fix it in one sentence]

### Files
- `src/file.ts`

### Test
How to verify it works.
```

---

## Example

```markdown
## Plan

### What
Fix login bug with special characters in password.

### Solution
Use parameterized query to prevent SQL injection.

### Files
- src/features/auth/service.ts

### Test
Login with password "pass'word" succeeds.
```

---

## When to Use

Use Quick Fix for:
- Bug fixes < 1 hour
- Small typos
- Quick config changes

---

## Checklist

- [ ] What is clear
- [ ] Solution makes sense
- [ ] Files listed
- [ ] How to test documented

---

*Document version: 1.0*
*Last updated: 2026-03-06*
