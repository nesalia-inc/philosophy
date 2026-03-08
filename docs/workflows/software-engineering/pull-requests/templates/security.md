## Security Fix

<!-- One sentence explaining the security issue -->

Example: Fix SQL injection vulnerability in user search

---

## Severity

- [ ] **Critical** (immediate threat)
- [ ] **High** (significant risk)
- [ ] **Medium** (moderate risk)
- [ ] **Low** (minor risk)

### CVE / Security Bulletin
- CVE: CVE-2025-XXXXX
- Security advisory: [Link]

---

## Vulnerability Details

### Vulnerability Type
- [ ] **SQL Injection**
- [ ] **XSS (Cross-Site Scripting)**
- [ ] **CSRF (Cross-Site Request Forgery)**
- [ ] **Authentication bypass**
- [ ] **Data leak**
- [ ] **DoS vulnerability**
- [ ] **Insecure configuration**
- [ ] **Dependency vulnerability**

### Description
<!-- Explain the vulnerability -->


### Attack Scenario
<!-- How an attacker could exploit this -->
1. Attacker sends malicious input to `/api/users/search`
2. Input is not sanitized
3. Attacker can execute arbitrary SQL

### Impact
<!-- What could happen -->
- All user data exposed
- Database can be modified/deleted
- Authentication bypass possible

---

## Security Fix

### Root Cause
<!-- What code was vulnerable -->
```typescript
// VULNERABLE CODE
app.get('/api/users/search', (req, res) => {
  const query = req.query.q
  // Direct interpolation - SQL injection!
  const sql = `SELECT * FROM users WHERE name = '${query}'`
  db.execute(sql, (err, results) => {
    res.json(results)
  })
})
```

### Fix Implementation
<!-- How it was fixed -->
```typescript
// SECURE CODE
app.get('/api/users/search', async (req, res): Promise<Result<void, ApiError>> => {
  const query = req.query.q as string

  // Validate input
  if (!query || query.length > 100) {
    return res.status(400).json({
      error: 'Invalid query parameter'
    })
  }

  // Use parameterized query
  const result = await db.execute<Result<User[], DbError>>(
    'SELECT * FROM users WHERE name = ?',
    [query]
  )

  if (!result.ok) {
    return res.status(500).json({
      error: 'Database error'
    })
  }

  res.json(result.value)
})
```

### Files Changed
```bash
src/api/users/search.ts    # Fix implemented
tests/api/users.test.ts    # Security tests added
```

---

## Security Testing

### Vulnerability Tests
- [ ] SQL injection test cases added
- [ ] XSS test cases added
- [ ] Authentication bypass tests
- [ ] Input validation tests

### Security Scan Results
```bash
# Run security tools
npm audit
npm run test:security
npm run sast

# Results
✓ No vulnerabilities found
✓ SQL injection prevented
✓ Input validation working
```

### Test Cases
<!-- Specific security tests -->
```typescript
describe('User Search Security', () => {
  it('prevents SQL injection', async () => {
    const maliciousQuery = "'; DROP TABLE users; --"
    const response = await request(app)
      .get(`/api/users/search?q=${encodeURIComponent(maliciousQuery)}`)

    expect(response.status).toBe(400) // Blocked
    expect(response.body.error).toContain('Invalid')
  })

  it('sanitizes XSS attempts', async () => {
    const xssPayload = '<script>alert("XSS")</script>'
    const response = await request(app)
      .get(`/api/users/search?q=${encodeURIComponent(xssPayload)}`)

    expect(response.text).not.toContain('<script>')
  })
})
```

---

## Prevention

### Code Review Practices
<!-- How to prevent this in the future -->
- All database queries must use parameterized queries
- All user inputs must be validated and sanitized
- Security review required for all API changes
- Automated security scanning in CI/CD

### Tooling
<!-- Tools to prevent similar issues -->
- [ ] ESLint plugin for security
- [ ] SAST in CI pipeline
- [ ] Dependency scanning (Dependabot)
- [ ] Pre-commit hooks for security

### Monitoring
<!-- How to detect similar issues -->
- Log all input validation failures
- Alert on suspicious query patterns
- Monitor database error rates

---

## Disclosure

### Disclosure Policy
- [ ] **Public disclosure**
- [ ] **Private disclosure**
- [ ] **Coordinated disclosure**

### Affected Parties
<!-- Who needs to know -->
- [ ] Users: [Notification plan]
- [ ] Customers: [Email/Push notification]
- [ ] Security team: [Ticket created]
- [ ] Public: [Advisory published]

### Communication
<!-- How to communicate -->
- Security advisory: [Link]
- Migration guide: [Link]
- Upgrade instructions: [Link]

---

## Deployment

### Deployment Strategy
- [ ] **Emergency deployment**
- [ ] **Scheduled maintenance**
- [ ] **Rolling deployment**

### Deployment Steps
1. Deploy fix to production
2. Clear all caches
3. Monitor for exploit attempts
4. Verify fix is working

### Verification
```bash
# Post-deployment verification
curl https://api.example.com/api/users/search?q="'; DROP TABLE users; --"
# Should return 400 Bad Request
```

---

## Impact Assessment

### Users Affected
<!-- Who was vulnerable -->
- All users using search functionality
- Attack window: [Date] to [Date]
- Confirmed exploits: [Number]

### Data Impact
- [ ] No data exposed
- [ ] Some data may be exposed
- [ ] Significant data exposure

---

## Post-Mortem

### Lessons Learned
<!-- What will change -->
- Add mandatory security review for all API changes
- Implement automated SQL injection detection
- Add security testing to onboarding

### Action Items
- [ ] Write post-mortem document
- [ ] Update security guidelines
- [ ] Train team on SQL injection prevention

---

*Severity: High*
*CVSS Score: 8.5*
*Public Disclosure: 2025-03-15*
