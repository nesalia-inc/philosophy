## 💥 BREAKING CHANGE

<!-- One sentence explaining the breaking change -->

Example: BREAKING CHANGE - Replace callback-based API with Promise-based API

---

## ⚠️ Breaking Change Notice

### Migration Required
- [ ] 🔴 **Breaking change** - Consumers must update their code
- [ ] 🟡 **Deprecation** - Old API still works but will be removed
- [ ] 🟢 **Addition only** - No breaking changes

### Release Version
- Target release: v2.0.0
- Breaking since: v1.0.0

---

## 📦 What Changed?

### API Changes
<!-- List the breaking changes -->
- `authenticate()` now returns `Promise<Result<User, AuthError>>` instead of using callbacks
- `User.id` is now `UUID` instead of `number`
- Removed `deprecatedMethod()` - use `newMethod()` instead

### Before (Old API)
```typescript
// ❌ OLD API (removed)
import { authenticate } from 'my-lib'

authenticate(user, password, (err, user) => {
  if (err) {
    console.error('Authentication failed', err)
  } else {
    console.log('Authenticated', user)
  }
})
```

### After (New API)
```typescript
// ✅ NEW API
import { authenticate } from 'my-lib'

const result = await authenticate(user, password)

if (!result.ok) {
  console.error('Authentication failed', result.error)
} else {
  console.log('Authenticated', result.value)
}
```

---

## 🔄 Migration Guide

### Step-by-Step Migration

#### 1. Update Imports
```bash
# No import changes needed
# Same package, new API
```

#### 2. Replace Callbacks with Async/Await
```typescript
// Before
function processData(data, callback) {
  authenticate(data.user, data.pass, (err, user) => {
    if (err) return callback(err)
    callback(null, user)
  })
}

// After
async function processData(data): Promise<Result<User, ProcessError>> {
  const authResult = await authenticate(data.user, data.pass)

  if (!authResult.ok) {
    return Result.err({
      type: 'auth_failed',
      cause: authResult.error
    })
  }

  return Result.ok(authResult.value)
}
```

#### 3. Update Type References
```typescript
// Before
interface User {
  id: number
  name: string
}

// After
interface User {
  id: UUID  // Changed from number
  name: string
}
```

### Migration Script
<!-- Automated migration if available -->
```bash
# Run automated codemod
npx my-lib-codemod@2 migrate ./src

# Or manual migration
npm install my-lib@2
# Update your code
npm run typecheck  # Check for errors
```

---

## 📊 Impact Assessment

### Scope of Change
- [ ] 🔴 **Wide impact** - Most consumers affected
- [ ] 🟡 **Medium impact** - Some consumers affected
- [ ] 🟢 **Narrow impact** - Few consumers affected

### Estimated Migration Effort
<!-- Time for consumers to migrate -->
- Small projects: ~1 hour
- Medium projects: ~4 hours
- Large projects: ~1-2 days

### Affected Consumers
<!-- Who needs to migrate -->
- Estimated projects affected: ~150
- Internal projects: 12
- External projects: ~138

---

## ✅ Testing

### Backwards Compatibility Tests
- [ ] Old API no longer works (as expected)
- [ ] New API works correctly
- [ ] Migration examples tested

### Test Coverage
```bash
# Test new API
npm run test

# Test migration examples
npm run test:migration
```

---

## 📚 Documentation

### Documentation Updates
- [ ] README updated with new API
- [ ] Migration guide published
- [ ] Changelog updated
- [ ] Code examples updated
- [ ] Breaking changes documented

### Migration Resources
- [Migration Guide](./docs/migration-v2.md)
- [API Reference](./docs/api-v2.md)
- [Examples](./examples/v2/)

---

## 📅 Deprecation Timeline

| Date | Milestone |
|------|-----------|
| 2025-03-08 | v2.0.0 released with breaking changes |
| 2025-04-08 | v1.x deprecated (1 month notice) |
| 2025-06-08 | v1.x end of life (3 months notice) |
| 2025-09-08 | v1.x no longer supported |

### Support Policy
- **v1.x**: Security patches only until 2025-06-08
- **v2.x**: Full support until 2026-03-08

---

## 🚀 Deployment

### Deployment Strategy
- [ ] 🔄 Gradual rollout (canary)
- [ ] 📊 Feature flags for gradual migration
- [ ] 🎯 Big bang release

### Rollback Plan
```bash
# If issues arise, rollback to v1.x
git checkout v1.10.0
npm publish --tag legacy

# Update consumers to use legacy tag
npm install my-lib@legacy
```

---

## 🤔 Why This Change?

### Problems with Old API
<!-- Why we're breaking things -->
- Callbacks make error handling difficult
- No type safety on errors
- Hard to test
- Doesn't align with modern async patterns

### Benefits of New API
<!-- What consumers gain -->
- Better error handling with Result<T, E>
- Type-safe errors
- Easier testing
- Modern async/await syntax
- Better IDE support

---

## 💬 Feedback & Questions

### Get Help
- [GitHub Discussions](link)
- [Discord Community](link)
- [Support Email](mailto:support@example.com)

### Known Issues
<!-- Document any migration issues -->
- Issue #123: Some consumers struggle with async migration
- Issue #124: TypeScript version requirements

---

## ✋ Approval

### Required Approvals
- [ ] Tech Lead: Breaking changes approved
- [ ] Product: Business impact reviewed
- [ ] Documentation: Migration guide complete

### Risk Assessment
- Risk level: [Low/Medium/High]
- Mitigation: [What we're doing to reduce risk]

---

*Breaking Change Version: 2.0.0*
*Migration Deadline: 2025-06-08*
*Support Ends: 2025-09-08*
*Affected Consumers: ~150 projects*
