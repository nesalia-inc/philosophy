---
name: Bug Fix Pull Request
about: Template for bug fixes
title: 'fix: '
labels: bugfix
assignees: ''
---

<!-- One sentence explaining which bug is fixed -->
## Bug Fix Summary



Fixes #[issue-number]

---

## Bug Description
<!-- Describe the bug symptoms -->


---

## Expected Behavior
<!-- What should happen -->


---

## Actual Behavior
<!-- What was happening -->


---

## Root Cause
<!-- What was causing the bug -->


---

## Type of Change

- [ ] **Bug fix** (non-breaking)
- [ ] **Breaking change** (fix that changes behavior)
- [ ] **Hotfix** (urgent production fix)

---

## Files Changed
```bash
# List files
```

---

## Reproduction Steps
<!-- Steps to reproduce the original bug -->
1.
2.
3.

---

## Fix Verification
- [ ] Can reproduce original bug
- [ ] Bug is fixed with this PR
- [ ] Regression tests added
- [ ] Edge cases covered

---

## Test Cases
<!-- Specific tests added -->
```typescript
it('', () => {

})
```

---

## Root Cause Analysis

### Before (Broken Code)
```typescript
// Show the problematic code
```

### After (Fixed Code)
```typescript
// Show the fix
```

---

## Regression Prevention

### Tests Added
<!-- New tests to prevent this bug from coming back -->


### Monitoring
<!-- How to detect if this comes back -->


---

## Impact Assessment

### Users Affected
<!-- Who was impacted by this bug -->

### Severity
- [ ] **Critical** (blocks functionality)
- [ ] **High** (major impact)
- [ ] **Medium** (minor impact)
- [ ] **Low** (cosmetic)

---

## Deployment

### Urgency
- [ ] Deploy immediately
- [ ] Deploy in next release
- [ ] Deploy in scheduled window

### Rollback Plan
```bash
# If issues arise
```

