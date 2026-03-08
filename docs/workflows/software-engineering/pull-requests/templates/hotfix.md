## 🔥 HOTFIX - [Urgent Issue]

<!-- One sentence explaining the urgent fix -->

Example: HOTFIX - Fix payment processing failure

---

## 🚨 Severity & Urgency

### Severity
- [ ] 🔴 **CRITICAL** - Production broken, data loss, security breach
- [ ] 🟠 **HIGH** - Major functionality broken
- [ ] 🟡 **MEDIUM** - Significant impact on users

### Urgency
- [ ] 🔥 **IMMEDIATE** - Deploy now, skip normal process
- [ ] ⚡ **URGENT** - Deploy within hours
- [ ] ⏰ **HIGH PRIORITY** - Deploy today

---

## 🔴 Issue Summary

### What's Broken?
<!-- Describe the production issue -->


### Impact
<!-- Who/what is affected -->
- [ ] All users
- [ ] Specific feature: [Name]
- [ ] Specific region: [Name]
- [ ] Percentage of users: [%]

### Started At
<!-- When did the issue start -->
- First detected: [Timestamp]
- Confirmed: [Timestamp]

---

## 📦 Hotfix Details

### Root Cause
<!-- What caused the issue -->


### Fix Applied
<!-- Minimal fix to resolve the issue -->
```typescript
// Show the fix
```

### Files Changed
```bash
# Only critical files
src/payment/processor.ts
```

---

## ⚡ Bypassed Process

### Normal Steps Skipped
<!-- Acknowledge what was skipped -->
- [ ] Full code review
- [ ] Full test suite
- [ ] Staging deployment
- [ ] Documentation updates

### Critical Checks Done
<!-- What WAS verified -->
- [ ] Local testing passed
- [ ] Critical smoke tests passed
- [ ] No new dependencies added
- [ ] No database migrations

---

## ✅ Minimal Testing

### Smoke Tests
```bash
# Critical path tests only
npm run test:smoke
```

### Manual Verification
- [ ] Tested locally
- [ ] Verified in production (canary)
- [ ] Checked error logs

### Test Results
```
✓ Payment processing works
✓ No errors in logs
✓ Response times normal
```

---

## 🚀 Deployment

### Deployment Steps
1. Merge to main
2. Trigger immediate deployment
3. Verify fix is working
4. Monitor for 30 minutes

### Deployment Command
```bash
# One-line deploy
git push origin main && git push origin --tags
# CI/CD auto-deploys
```

### Verification
```bash
# Post-deployment checks
curl -f https://api.example.com/health
curl -f https://api.example.com/api/payments/test
```

---

## 🔄 Post-Hotfix Actions

### Required Follow-up
<!-- What must be done AFTER the hotfix -->
- [ ] Create proper PR with full tests
- [ ] Write post-mortem document
- [ ] Add regression tests
- [ ] Update documentation
- [ ] Schedule retrospective

### Post-Mortem
<!-- Link to post-mortem -->
- Post-mortem issue: #[number]
- Meeting scheduled: [Date/Time]

### Regression Tests
<!-- Tests to add to prevent recurrence -->
- [ ] Test case for [specific scenario]
- [ ] Integration test for [flow]
- [ ] Monitoring alert for [symptom]

---

## 📊 Incident Timeline

| Time | Event | Duration |
|------|-------|----------|
| 14:32 | Alert triggered: Payment failures | - |
| 14:35 | Issue confirmed in production | 3 min |
| 14:40 | Root cause identified | 5 min |
| 14:55 | Fix implemented locally | 15 min |
| 15:00 | Hotfix deployed to production | 5 min |
| 15:05 | Verified fix working | 5 min |

**Total Downtime:** 33 minutes

---

## 👥 Communication

### Notified
- [ ] Engineering team
- [ ] Product team
- [ ] Support team
- [ ] Customers (if applicable)

### Customer Communication
<!-- What customers were told -->
- Status page updated: [Link]
- Email sent: [Yes/No]
- In-app banner: [Yes/No]

---

## 📚 References

- [Alert Link](url)
- [Error Logs](url)
- [Monitoring Dashboard](url)

---

## ✋ Approval

### Approved By
- [ ] Engineering Lead: @name
- [ ] On-call Engineer: @name

### Deployment Approved
- [ ] Yes - Deploy immediately
- [ ] Yes - Deploy within [timeframe]
- [ ] No - Needs review first

---

*Hotfix deployed: 2025-03-08 15:00 UTC*
*Incident duration: 33 minutes*
*Post-mortem scheduled: 2025-03-09 10:00 UTC*
