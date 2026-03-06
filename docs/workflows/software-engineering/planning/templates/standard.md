# Standard Template

For work 1-3 days.

---

## Template

```markdown
## Plan

### What
[One sentence describing what to build]

### Files
**Create:**
- `src/features/auth/types.ts`
- `src/features/auth/service.ts`

**Modify:**
- `src/index.ts`

### Dependencies
- Internal: `@nesalia/pkg`
- External: `package-x`

### API
- `POST /api/auth/login`
- `POST /api/auth/logout`

### Security
- [ ] Input validation
- [ ] Password hashing
- [ ] Authentication required

### Tests
- [ ] Unit tests
- [ ] Integration tests

### Impact
- [ ] What might conflict

### Acceptance Criteria
- [ ] Criterion 1 - testable
- [ ] Criterion 2 - testable

### How to Test
1. Run dev
2. Test feature
3. Verify result
```

---

## Example

```markdown
## Plan

### What
Add JWT-based user authentication with login/logout.

### Files
**Create:**
- src/features/auth/types.ts
- src/features/auth/service.ts
- tests/features/auth.test.ts

**Modify:**
- src/index.ts

### Dependencies
- @nesalia/crypto (bcrypt for passwords)
- @nesalia/jwt (token handling)

### API
- POST /api/auth/login - {email, password} → {token, user}
- POST /api/auth/logout - clears token

### Security
- [x] Input validation - zod schemas
- [x] Password hashing - bcrypt
- [x] Authentication required - JWT middleware

### Tests
- [ ] Unit tests for service
- [ ] Integration tests for API

### Impact
- [ ] Dashboard (checks user login) - will use new auth

### Acceptance Criteria
- [ ] Login with valid credentials returns token
- [ ] Login with invalid credentials returns 401

### How to Test
1. npm run dev
2. POST /api/auth/login
3. Verify response has token
```

---

## When to Use

Use Standard for:
- New features
- Medium bug fixes
- API changes
- Small refactors

---

## Checklist

- [ ] What is clear
- [ ] Files listed (create + modify)
- [ ] Dependencies identified
- [ ] API defined
- [ ] Security considered
- [ ] Tests planned
- [ ] Impact analyzed
- [ ] Acceptance criteria testable
- [ ] How to test documented

---

*Document version: 1.0*
*Last updated: 2026-03-06*
