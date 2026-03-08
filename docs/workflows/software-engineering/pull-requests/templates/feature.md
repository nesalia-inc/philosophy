## 🎯 Summary
<!-- One sentence explaining what this PR does -->

Example: Add user authentication with JWT tokens

---

## 📝 Description

### What does this PR do?
<!-- Explain the changes in simple terms -->


### Why is it needed?
<!-- Link to business value or user need -->


### How does it work?
<!-- High-level implementation approach -->


### Related Issue
Closes #[issue-number]

---

## 🔄 Type of Change

- [ ] 🚀 **New feature** (non-breaking change)
- [ ] 🐛 **Bug fix** (non-breaking change)
- [ ] 💥 **Breaking change** (fix or feature that breaks existing functionality)
- [ ] ♻️ **Refactor** (code quality, no behavior change)
- [ ] 📚 **Documentation** (docs only)
- [ ] 🎨 **Style** (formatting, no logic change)
- [ ] ⚡ **Performance** (performance improvement)
- [ ] ✅ **Test** (adding/updating tests)
- [ ] 🔧 **Configuration** (config changes)

---

## 📦 Changes Made

### Files Added
```bash
# List new files
src/features/auth/types.ts
src/features/auth/service.ts
src/features/auth/api.ts
tests/features/auth/service.test.ts
```

### Files Modified
```bash
# List modified files
src/index.ts
src/db/schema.ts
```

### Key Changes
<!-- Bullet points of main changes -->
- Implemented `authenticateUser()` with Result<User, AuthError>
- Added JWT token generation and validation
- Created POST /api/auth/login and POST /api/auth/logout endpoints
- Added comprehensive unit and integration tests

---

## ✅ Test Plan

### Unit Tests
- [ ] All new functions have unit tests
- [ ] Edge cases covered (null, undefined, errors)
- [ ] Tests use real implementations, not mocks

### Integration Tests
- [ ] API endpoints tested end-to-end
- [ ] Database operations verified
- [ ] Error paths tested

### Manual Testing
- [ ] Tested locally with `npm run dev`
- [ ] Tested in preview environment
- [ ] Screenshots/recordings attached (for UI changes)

### Test Commands
```bash
# Commands to verify
npm run test                # Unit tests
npm run test:integration   # Integration tests
npm run test:e2e          # End-to-end tests
npm run test:coverage     # Coverage report
```

### Test Results
<!-- Paste test results -->
```
Test Files  4 passed (4)
     Tests  14 passed (14)
```

---

## 🔍 Code Quality Checklist

### Result<T, E> Pattern
- [ ] All functions returning success/error use `Result<T, E>`
- [ ] No silent failures (uncaught errors)
- [ ] Errors are user-visible or logged
- [ ] Error types are explicit (not `any`)

### Option<T> Pattern
- [ ] No `null` or `undefined` in type signatures
- [ ] Optional values use `Option<T>`
- [ ] Pattern matching before unwrapping

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
- [ ] Consistent formatting with Prettier

---

## 🏗️ Architecture & Design

### Dependencies
<!-- List new dependencies added -->
```json
{
  "jsonwebtoken": "^9.0.0",
  "bcrypt": "^5.1.0"
}
```

### Database Changes
<!-- Schema changes, migrations -->
```sql
-- Add users table
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL
);

-- Migration: 001_add_users_table.sql
```

### Environment Variables
<!-- New env vars needed -->
```bash
# Add to .env.example
JWT_SECRET=your-secret-key
JWT_EXPIRES_IN=24h
```

### API Changes
<!-- New/modified endpoints -->
- `POST /api/auth/login` - Authenticate user
- `POST /api/auth/logout` - Invalidate session
- `GET /api/auth/me` - Get current user

---

## 🔒 Security Review

- [ ] No secrets or API keys committed
- [ ] Input validation on all user inputs
- [ ] SQL injection prevention
- [ ] XSS prevention
- [ ] CSRF protection (if applicable)
- [ ] Rate limiting (if applicable)
- [ ] Sensitive data logged appropriately

---

## 📊 Performance Considerations

- [ ] No N+1 queries
- [ ] Database queries optimized
- [ ] Caching considered (if applicable)
- [ ] Bundle size impact measured (frontend)
- [ ] Memory usage reasonable

---

## 📚 Documentation

- [ ] README updated (if needed)
- [ ] API documentation updated (if public API)
- [ ] JSDoc comments added for public APIs
- [ ] CHANGELOG.md updated
- [ ] Migration guide (if breaking change)

---

## 🚀 Deployment

### Deployment Steps
<!-- List manual steps if needed -->
1. Run database migrations: `npm run migrate`
2. Clear Redis cache
3. Restart services

### Rollback Plan
<!-- How to undo if issues -->
```bash
git revert <commit-hash>
npm run migrate:down
```

### Monitoring
<!-- What to watch after deploy -->
- Error logs for AuthError spikes
- Login success/failure rates
- Response time for /api/auth/*

---

## 🔗 Related Resources

- [Design Document](link/to/doc)
- [API Spec](link/to/api)
- [Figma Mockup](link/to/figma)

---

## 📸 Screenshots / Recordings

<!-- For UI changes -->
<!-- Attach screenshots or Loom recording -->

### Before
<!-- Screenshot before changes -->

### After
<!-- Screenshot after changes -->

---

## 💬 Additional Notes

<!-- Any additional context, decisions, or trade-offs -->


---

## ✋ Reviewer Focus Areas

<!-- Tag specific reviewers for certain aspects -->

**Frontend**: @reviewer-name - Focus on UI/UX
**Backend**: @reviewer-name - Focus on API and business logic
**Security**: @reviewer-name - Focus on security implications
**Database**: @reviewer-name - Focus on schema and migrations

---

*PR Size: 450 lines changed, 12 files modified*
*Estimated Review Time: 20-30 minutes*
