---
name: Hotfix Pull Request
about: Template for emergency production fixes
title: 'HOTFIX: '
labels: hotfix, urgent
assignees: ''
---

## HOTFIX - [Urgent Issue]

<!-- One sentence explaining the urgent fix -->



---

## Severity & Urgency

### Severity
- [ ] **CRITICAL** - Production broken, data loss, security breach
- [ ] **HIGH** - Major functionality broken
- [ ] **MEDIUM** - Significant impact on users

### Urgency
- [ ] **IMMEDIATE** - Deploy now, skip normal process
- [ ] **URGENT** - Deploy within hours
- [ ] **HIGH PRIORITY** - Deploy today

---

## Issue Summary

### What's Broken?
<!-- Describe the production issue -->


### Impact
- [ ] All users
- [ ] Specific feature: [Name]
- [ ] Percentage of users: [%]

### Started At
- First detected: [Timestamp]
- Confirmed: [Timestamp]

---

## Root Cause
<!-- What caused the issue -->


---

## Fix Applied
<!-- Minimal fix to resolve the issue -->
```typescript
// Show the fix
```

---

## Files Changed
```bash
# Only critical files
```

---

## Bypassed Process

### Normal Steps Skipped
- [ ] Full code review
- [ ] Full test suite
- [ ] Staging deployment
- [ ] Documentation updates

### Critical Checks Done
- [ ] Local testing passed
- [ ] Critical smoke tests passed
- [ ] No new dependencies added
- [ ] No database migrations

---

## Minimal Testing

### Smoke Tests
```bash
npm run test:smoke
```

### Manual Verification
- [ ] Tested locally
- [ ] Verified in production (canary)
- [ ] Checked error logs

### Test Results
```
✓ [Test result]
✓ [Test result]
```

---

## Deployment Steps
1.
2.
3.
4.

---

## Deployment Command
```bash
# One-line deploy
```

---

## Verification
```bash
# Post-deployment checks
```

---

## Post-Hotfix Actions

### Required Follow-up
- [ ] Create proper PR with full tests
- [ ] Write post-mortem document
- [ ] Add regression tests
- [ ] Update documentation
- [ ] Schedule retrospective

### Post-Mortem
- Post-mortem issue: #[number]
- Meeting scheduled: [Date/Time]

### Regression Tests
- [ ] Test case for [specific scenario]
- [ ] Integration test for [flow]
- [ ] Monitoring alert for [symptom]

---

## Incident Timeline

| Time | Event | Duration |
|------|-------|----------|
| [time] | [event] | [duration] |
| [time] | [event] | [duration] |

**Total Downtime:** [duration]

---

## Communication

### Notified
- [ ] Engineering team
- [ ] Product team
- [ ] Support team
- [ ] Customers (if applicable)

### Customer Communication
- Status page updated: [Link]
- Email sent: [Yes/No]

---

## References
- [Alert Link](url)
- [Error Logs](url)
- [Monitoring Dashboard](url)

---

## Approval

### Approved By
- [ ] Engineering Lead: @name
- [ ] On-call Engineer: @name

### Deployment Approved
- [ ] Yes - Deploy immediately
- [ ] Yes - Deploy within [timeframe]

---

*Hotfix deployed: [date]*
*Incident duration: [duration]*
*Post-mortem scheduled: [date]*
