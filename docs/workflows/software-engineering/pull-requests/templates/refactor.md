---
name: Refactor Pull Request
about: Template for code refactoring
title: 'refactor: '
labels: refactor
assignees: ''
---

<!-- One sentence explaining what is refactored -->
## Refactor Summary



---

## Motivation

### Why refactor?
<!-- Explain the problem with current code -->

### What problem does it solve?
<!-- Benefits of this refactor -->

---

## Type of Change

- [ ] **Refactor** (no behavior change)
- [ ] **Architecture** (structural change)

### Behavior Contract
- [ ] **No behavior changes** - Output is identical
- [ ] **No API changes** - Public signatures unchanged
- [ ] **Tests updated** - Tests verify same behavior

---

## Refactoring Approach
<!-- Explain the refactoring technique used -->


---

## Before & After Comparison

### Before (Code with Issues)
```typescript
// Show the problematic code
```

### After (Refactored Code)
```typescript
// Show the improved code
```

---

## Files Refactored
```bash
# List files
```

---

## Test Strategy

### Refactoring Tests
- [ ] All existing tests still pass
- [ ] No test behavior changed
- [ ] Tests verify same outputs

### New Tests
- [ ] Tests for new patterns
- [ ] Tests for edge cases
- [ ] Integration tests updated

### Regression Check
```bash
npm run test
npm run test:integration
```

---

## Metrics

### Code Quality Improvements
<!-- Measurable improvements -->
- Cyclomatic complexity: [before] → [after]
- Lines of code: [before] → [after]
- Test coverage: [before] → [after]%
- `any` types: [before] → [after]

---

## Migration Path

### Backwards Compatibility
- [ ] Fully compatible (no changes needed)
- [ ] Minor breaking changes (documented)
- [ ] Major breaking changes (migration guide provided)

### Migration Required
<!-- If consumers need to update code -->
```typescript
// Show migration example
```

---

## Documentation

- [ ] README updated with new patterns
- [ ] Migration guide provided (if needed)
- [ ] Code examples updated
- [ ] JSDoc updated

---

## Open Questions
<!-- Design decisions that need discussion -->

