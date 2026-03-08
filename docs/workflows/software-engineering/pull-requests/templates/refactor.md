---
name: Refactor Pull Request
about: Template for code refactoring (no behavior changes)
title: 'refactor: '
labels: refactor
assignees: ''
---

## Summary
_What is being refactored in one sentence?_

**Example**: "Extract user authentication logic into dedicated service module"

---

## Motivation
_Why refactor this code? What problem does it solve?_

- **Problem**: What was wrong with the current code
- **Benefit**: What improvement this brings

**Example**: "Authentication logic was duplicated across 5 controllers, making changes error-prone"



---

## Refactoring Approach
_How was the code restructured?_

- **Technique**: Extract method / Rename / Move / etc.
- **Scope**: Which files/modules are affected

<details>
<summary>Before & After comparison (click to expand)</summary>

### Before (Problematic Code)
```typescript
// Show the problematic code
```

### After (Refactored Code)
```typescript
// Show the improved code
```

</details>

---

## Behavior Verification
_How do we know behavior hasn't changed?_

### Test Results
- [ ] All existing tests pass (no changes to test assertions)
- [ ] No new test failures
- [ ] Integration tests pass

<details>
<summary>Test commands (click to expand)</summary>

```bash
# Full test suite
npm run test

# Integration tests
npm run test:integration

# E2E tests (if applicable)
npm run test:e2e
```
</details>

### Behavior Contract
- [ ] **No behavior changes** - Outputs are identical
- [ ] **No API changes** - Public signatures unchanged
- [ ] **No performance regression** - Benchmarks comparable

---

## Impact Assessment

### Breaking Changes
- [ ] No breaking changes
- [ ] **Internal changes only** - External behavior unchanged

### Consumers Affected
- [ ] No consumers affected (internal refactor)
- [ ] **API changed** - migration guide provided below

### Migration Guide
_If consumers need to update their code:_

```typescript
// Show migration example
```

---

## Quality Metrics
_Measurable improvements (optional but recommended)_

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| Lines of code | | | |
| Cyclomatic complexity | | | |
| Function count | | | |
| Test coverage | | | |
| `any` types | | | |

---

## Additional Context

- Related issue: #
- Design discussion: #
- Areas you'd like specific feedback on

<!--
💡 Refactoring checklist:
✓ No behavior changes (verified by tests)
✓ All tests pass
✓ Code is more maintainable
✓ Philosophy patterns applied

CI automatically validates code quality and patterns.
Focus on proving behavior is unchanged.
-->
