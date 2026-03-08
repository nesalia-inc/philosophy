---
name: Feature Pull Request
about: Template for new features and functionality
title: 'feat: '
labels: feature
assignees: ''
---

<!-- One sentence explaining what this PR does -->
## Summary



<!-- Closes #[issue-number] -->
## Related Issue



## Type of Change

- [ ] **New feature** (non-breaking change)
- [ ] **Bug fix** (non-breaking change)
- [ ] **Breaking change** (fix or feature that breaks existing functionality)
- [ ] **Refactor** (code quality, no behavior change)
- [ ] **Documentation** (docs only)
- [ ] **Performance** (performance improvement)
- [ ] **Test** (adding/updating tests)

---

## Files Added
```bash
# List new files
```

## Files Modified
```bash
# List modified files
```

## Key Changes
<!-- Bullet points of main changes -->


---

## Test Plan

### Unit Tests
- [ ] All new functions have unit tests
- [ ] Edge cases covered
- [ ] Tests use real implementations, not mocks

### Integration Tests
- [ ] API endpoints tested end-to-end
- [ ] Database operations verified
- [ ] Error paths tested

### Manual Testing
- [ ] Tested locally
- [ ] Screenshots/recordings attached (for UI changes)

### Test Commands
```bash
npm run test
npm run test:integration
```

---

## Code Quality Checklist

### Philosophy Alignment
- [ ] All functions returning success/error use `Result<T, E>`
- [ ] No silent failures (uncaught errors)
- [ ] Errors are user-visible or logged
- [ ] Error types are explicit (not `any`)
- [ ] No `null` or `undefined` in type signatures
- [ ] Optional values use `Option<T>`

### Function Quality
- [ ] Functions under 50 lines
- [ ] Single responsibility per function
- [ ] Pure functions when possible
- [ ] No code duplication

### TypeScript
- [ ] No `any` types
- [ ] Strict mode enabled
- [ ] Proper type exports
- [ ] `npm run typecheck` passes

### Linting
- [ ] `npm run lint` passes
- [ ] No console.log left in production code

---

## Dependencies

### New Dependencies
```json
// List new dependencies added
```

### Database Changes
```sql
-- Schema changes, migrations
```

### Environment Variables
```bash
# New env vars needed
```

---

## Security Review

- [ ] No secrets or API keys committed
- [ ] Input validation on all user inputs
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CSRF protection (if applicable)

---

## Performance Considerations

- [ ] No N+1 queries
- [ ] Database queries optimized
- [ ] Caching considered (if applicable)
- [ ] Memory usage reasonable

---

## Documentation

- [ ] README updated (if needed)
- [ ] API documentation updated (if public API)
- [ ] CHANGELOG.md updated
- [ ] Migration guide (if breaking change)

---

## Deployment Steps
<!-- List manual steps if needed -->


## Rollback Plan
<!-- How to undo if issues -->


## Monitoring
<!-- What to watch after deploy -->


---

## Screenshots / Recordings

<!-- For UI changes, attach screenshots or recordings -->

### Before
<!-- Screenshot before changes -->

### After
<!-- Screenshot after changes -->

---

## Additional Notes
<!-- Any additional context, decisions, or trade-offs -->

