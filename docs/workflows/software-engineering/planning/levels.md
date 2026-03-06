# Plan Levels

Don't use the full checklist for every issue. Use the right level:

| Level | When | Time to create |
|-------|------|----------------|
| **Quick Fix** | < 1 hour work | 2 min |
| **Standard** | 1-3 days work | 5-10 min |
| **Complex** | > 1 week | 15-30 min |

---

## Level 1: Quick Fix (< 1 hour)

For small fixes, typos, quick improvements.

**Use for**:
- Bug fixes < 1 hour
- Small typos
- Quick config changes

**Time to create**: ~2 minutes

---

## Level 2: Standard (1-3 days)

For normal features and moderate work.

**Use for**:
- New features
- Medium bug fixes
- Small refactors
- API changes

**Time to create**: ~5-10 minutes

---

## Level 3: Complex (> 1 week)

For large features, big refactors, anything requiring multiple people.

**Use for**:
- Large features (> 1 week)
- Architecture changes
- Security features
- Multi-team work

**Time to create**: ~15-30 minutes

---

## Quick Reference

| Level | Time | Use For |
|-------|------|---------|
| Quick Fix | 2 min | < 1 hour |
| Standard | 5-10 min | 1-3 days |
| Complex | 15-30 min | > 1 week |

---

## Examples

### Quick Fix

```
## Plan

### What
Fix login bug with special characters.

### Solution
Use parameterized query for username.

### Files
- src/features/auth/service.ts

### Test
Login with "user'name" works.
```

### Standard

```
## Plan

### What
Add user authentication.

### Files
- Create: src/features/auth/service.ts
- Modify: src/index.ts

### API
- POST /api/auth/login

### Security
- [x] Validate input
- [x] Hash passwords

### Tests
- [ ] Unit tests
- [ ] Integration tests

### Acceptance Criteria
- [ ] Login works
```

### Complex

Full template with everything - see [Templates](./templates/)

---

*Document version: 1.0*
*Last updated: 2026-03-06*
