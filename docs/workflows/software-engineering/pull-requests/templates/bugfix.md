---
name: Bug Fix Pull Request
about: Template for bug fixes
title: 'fix: '
labels: bugfix
assignees: ''
---

## Summary
_What bug is being fixed in one sentence?_

**Example**: "Fix login redirect loop when user has expired session"

Fixes #

---

## Bug Description
_What was the bug? What symptoms did users experience?_

**Before**: What was happening (brief)

**After**: What happens now (brief)

---

## Root Cause
_Why was this happening?_

<!-- Example: "Missing null check when user session expires" -->



<details>
<summary>Technical analysis (click to expand)</summary>

### Before (Broken Code)
```typescript
// Show the problematic code
```

### After (Fixed Code)
```typescript
// Show the fix
```

</details>

---

## Fix Verification
_How do we know this fix works?_

- [ ] Can reproduce original bug
- [ ] Fix resolves the issue
- [ ] Regression test added
- [ ] Edge cases covered

### Test Cases Added
<!-- Specific tests to prevent this bug from returning -->

```typescript
it('handles expired session without redirect loop', () => {
  // test code
})
```

---

## Impact Assessment

### Users Affected
_Who experienced this bug?_

<!-- Example: "Users with sessions > 24h old (approximately 15% of daily active users)" -->

### Severity
- [ ] **Critical** (production down, data loss)
- [ ] **High** (major functionality blocked)
- [ ] **Medium** (workaround exists)
- [ ] **Low** (cosmetic, minor impact)

### Breaking Changes
- [ ] No breaking changes
- [ ] **Behavior change** - users may notice

---

## Deployment

### Urgency
- [ ] **Immediate** (critical/hotfix)
- [ ] **Next release** (standard)
- [ ] **Scheduled window** (low priority)

### Rollback Plan
_If something goes wrong, how do we undo?_

<!-- Example: "Revert commit, no data migration needed" -->

---

## Additional Context

- Link to bug report: #
- Related issues: #
- Areas you'd like specific feedback on

<!--
💡 CI automatically validates:
✓ Type checking
✓ Linting
✓ All tests pass
✓ Code patterns (Result<T, E>, Option<T>)

Focus on explaining the bug and ensuring it doesn't come back.
-->
