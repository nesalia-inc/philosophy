---
name: Security Fix Pull Request
about: Template for security vulnerability fixes
title: 'security: '
labels: security
assignees: ''
---

<!-- One sentence explaining the security issue -->
## Security Fix



---

## Severity

- [ ] **Critical** (immediate threat)
- [ ] **High** (significant risk)
- [ ] **Medium** (moderate risk)
- [ ] **Low** (minor risk)

### CVE / Security Bulletin
- CVE: [CVE-Number]
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

---

## Description
<!-- Explain the vulnerability -->


---

## Attack Scenario
<!-- How an attacker could exploit this -->
1.
2.
3.

---

## Impact
<!-- What could happen -->

---

## Root Cause
<!-- What code was vulnerable -->
```typescript
// Show vulnerable code
```

---

## Fix Implementation
<!-- How it was fixed -->
```typescript
// Show secure code
```

---

## Files Changed
```bash
# List files
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
# Results
npm audit
npm run test:security
```

### Test Cases
<!-- Specific security tests -->
```typescript
it('', async () => {

})
```

---

## Prevention

### Code Review Practices
<!-- How to prevent this in the future -->

### Tooling
<!-- Tools to prevent similar issues -->
- [ ] ESLint plugin for security
- [ ] SAST in CI pipeline
- [ ] Dependency scanning (Dependabot)

### Monitoring
<!-- How to detect similar issues -->

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

---

## Deployment

### Deployment Strategy
- [ ] **Emergency deployment**
- [ ] **Scheduled maintenance**
- [ ] **Rolling deployment**

### Deployment Steps
1.
2.
3.

### Verification
```bash
# Post-deployment verification command
```

---

## Impact Assessment

### Users Affected
<!-- Who was vulnerable -->

### Data Impact
- [ ] No data exposed
- [ ] Some data may be exposed
- [ ] Significant data exposure

---

## Post-Mortem

### Lessons Learned
<!-- What will change -->

### Action Items
- [ ] Write post-mortem document
- [ ] Update security guidelines
- [ ] Train team

---

*CVSS Score: [score]*
*Public Disclosure: [date]*
