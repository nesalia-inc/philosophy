---
name: Breaking Change Pull Request
about: Template for breaking API changes
title: 'BREAKING CHANGE: '
labels: breaking-change
assignees: ''
---

## BREAKING CHANGE

<!-- One sentence explaining the breaking change -->



---

## Breaking Change Notice

### Migration Required
- [ ] **Breaking change** - Consumers must update their code
- [ ] **Deprecation** - Old API still works but will be removed
- [ ] **Addition only** - No breaking changes

### Release Version
- Target release: vX.X.X
- Breaking since: vX.X.X

---

## What Changed?
<!-- List the breaking changes -->


---

## Before (Old API)
```typescript
// OLD API (removed)
```

---

## After (New API)
```typescript
// NEW API
```

---

## Migration Guide

### Step-by-Step Migration

#### 1. [Step title]
```typescript
// Show migration example
```

#### 2. [Step title]
```typescript
// Show migration example
```

---

### Migration Script
<!-- Automated migration if available -->
```bash
# Run automated codemod
```

---

## Impact Assessment

### Scope of Change
- [ ] **Wide impact** - Most consumers affected
- [ ] **Medium impact** - Some consumers affected
- [ ] **Narrow impact** - Few consumers affected

### Estimated Migration Effort
<!-- Time for consumers to migrate -->
- Small projects: ~[time]
- Medium projects: ~[time]
- Large projects: ~[time]

### Affected Consumers
<!-- Who needs to migrate -->
- Estimated projects affected: ~[number]
- Internal projects: [number]
- External projects: ~[number]

---

## Testing

### Backwards Compatibility Tests
- [ ] Old API no longer works (as expected)
- [ ] New API works correctly
- [ ] Migration examples tested

### Test Coverage
```bash
npm run test
npm run test:migration
```

---

## Documentation Updates
- [ ] README updated with new API
- [ ] Migration guide published
- [ ] Changelog updated
- [ ] Code examples updated
- [ ] Breaking changes documented

---

## Migration Resources
- [Migration Guide](./docs/migration-vX.md)
- [API Reference](./docs/api-vX.md)
- [Examples](./examples/vX/)

---

## Deprecation Timeline

| Date | Milestone |
|------|-----------|
| [date] | vX.X.X released with breaking changes |
| [date] | v[old].x deprecated ([time] notice) |
| [date] | v[old].x end of life ([time] notice) |
| [date] | v[old].x no longer supported |

### Support Policy
- **v[old].x**: Security patches only until [date]
- **v[new].x**: Full support until [date]

---

## Deployment

### Deployment Strategy
- [ ] **Gradual rollout** (canary)
- [ ] **Feature flags** for gradual migration
- [ ] **Big bang** release

### Rollback Plan
```bash
# If issues arise, rollback to v[old].x
```

---

## Why This Change?

### Problems with Old API
<!-- Why we're breaking things -->


### Benefits of New API
<!-- What consumers gain -->


---

## Feedback & Questions

### Get Help
- [GitHub Discussions](link)
- [Discord Community](link)
- [Support Email](mailto:support@example.com)

### Known Issues
<!-- Document any migration issues -->
- Issue #[number]: [description]

---

## Required Approvals
- [ ] Tech Lead: Breaking changes approved
- [ ] Product: Business impact reviewed
- [ ] Documentation: Migration guide complete

---

## Risk Assessment
- Risk level: [Low/Medium/High]
- Mitigation: [What we're doing to reduce risk]

---

*Breaking Change Version: X.X.X*
*Migration Deadline: [date]*
*Support Ends: [date]*
*Affected Consumers: ~[number] projects*
