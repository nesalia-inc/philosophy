# Complex Template

For work > 1 week. Full checklist.

---

## Template

```markdown
## Plan: [Feature Name]

### 1. What
[One sentence describing what to build]

### 2. Files
**Create:**
- `src/features/auth/types.ts` - User, AuthResult types

**Modify:**
- `src/index.ts` - Export new module

### 3. Dependencies
- Internal: `@nesalia/crypto` - Password hashing
- External: `package-x` - Description

### 4. API Changes
- `POST /api/auth/login` - User login

---

### 5. Database
- [ ] Migration needed?
- [ ] New table? Columns?
- [ ] Indexes needed?
- [ ] Query performance considered?
- [ ] Foreign keys?

### 6. Security
- [ ] Input validation
- [ ] Authentication required?
- [ ] Authorization/permissions?
- [ ] No secrets in code?
- [ ] SQL injection prevention?
- [ ] XSS prevention?

### 7. Error Handling
- [ ] Error types defined?
- [ ] Error messages user-friendly?
- [ ] Logging for debugging?
- [ ] 404, 401, 500 handled?

### 8. Testing
- [ ] Unit tests: file.test.ts
- [ ] Integration tests: file.integration.test.ts
- [ ] E2E tests needed?
- [ ] Test existing affected code?

### 9. Documentation
- [ ] README updated?
- [ ] API docs updated?
- [ ] Migration guide needed?
- [ ] Environment variables documented?

### 10. Configuration
- [ ] New env vars?
- [ ] Default values?
- [ ] Validation?

### 11. Breaking Changes
- [ ] API change?
- [ ] Database migration?
- [ ] Config change?
- [ ] Migration path?

### 12. Performance
- [ ] N+1 queries?
- [ ] Indexes added?
- [ ] Caching strategy?
- [ ] Pagination?

### 13. Monitoring
- [ ] Logging added?
- [ ] Metrics?
- [ ] Alerts for failures?

---

### 14. Impact Analysis

#### What Might This Conflict With?
- [ ] Existing system X - reason

#### What Depends on This?
- [ ] Feature Z - will need update

#### What Could Go Wrong?
1. Issue → Prevention
2. Problem → Solution

#### Similar Existing Features
- Look at: `src/features/auth/service.ts`

---

### 15. Acceptance Criteria
- [ ] Criterion 1 - testable
- [ ] Criterion 2 - testable

### 16. How to Test
1. Run `npm run dev`
2. POST /api/auth/login
3. Verify response
```

---

## When to Use

Use Complex for:
- Large features (> 1 week)
- Architecture changes
- Security features
- Multi-team work
- Anything affecting multiple systems

---

## Checklist Categories

| Category | Checks |
|----------|--------|
| **Database** | Migration, tables, indexes, performance |
| **Security** | Validation, auth, secrets, injection |
| **Error Handling** | Types, messages, logging |
| **Testing** | Unit, integration, affected code |
| **Documentation** | README, API, migration, env |
| **Configuration** | Env vars, defaults, validation |
| **Breaking Changes** | API, DB, config, migration |
| **Performance** | N+1, indexes, caching |
| **Monitoring** | Logging, metrics, alerts |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
