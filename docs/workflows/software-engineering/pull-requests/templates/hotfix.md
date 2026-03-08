---
name: Hotfix Pull Request
about: Template for emergency production fixes
title: 'hotfix: '
labels: hotfix, urgent
assignees: ''
---

## 🔥 HOTFIX
_Urgent production fix_

**Summary**: What's broken and what's fixed (one sentence)

**Example**: "Fix payment processing crash caused by null pointer exception"

---

## Incident

### Severity
- [ ] **CRITICAL** - Production down, data loss, security breach
- [ ] **HIGH** - Major functionality broken, significant user impact
- [ ] **MEDIUM** - Workaround exists but degraded experience

### Impact
- **Users affected**: <!-- Example: "All users attempting payments (approximately 100/hour)" -->
- **Started**: <!-- Timestamp when first detected -->
- **Confirmed**: <!-- Timestamp when confirmed -->

### Root Cause
_What caused this issue?_

<!-- Brief explanation of the root cause -->



---

## Fix Applied

### The Fix
```typescript
// Show the minimal fix
```

### Why This Fix Works
<!-- Brief explanation of how the fix resolves the issue -->



---

## Verification

### Testing Done
- [ ] Local smoke tests passed
- [ ] Staging/canary verified (if applicable)
- [ ] Production monitoring confirms fix

### Test Results
```
✓ [Smoke test result]
✓ [Verification step]
```

<details>
<summary>Smoke test commands (click to expand)</summary>

```bash
# Quick smoke tests
npm run test:smoke

# Critical path verification
npm run test:critical
```
</details>

---

## Deployment

### Plan
1. **Deploy to production**: <!-- When/How -->
2. **Verify**: <!-- What checks to run -->
3. **Monitor**: <!-- What to watch for -->

### Rollback Plan
_If something goes wrong, how do we revert?_

<!-- Example: "Revert commit, no data migration needed" -->

---

## Post-Hotfix Follow-up

### Required Actions
- [ ] **Create proper PR** with full tests and documentation
- [ ] **Write post-mortem** - What happened, why, how to prevent
- [ ] **Add regression tests** to prevent recurrence
- [ ] **Schedule retrospective** if severe

### Post-Mortem
- Post-mortem issue: #
- Meeting scheduled: <!-- Date/Time -->

---

## References

- Alert: <!-- Link to monitoring alert -->
- Error logs: <!-- Link to logs -->
- Related issue: #

---

## Approval

- [ ] Engineering Lead approval obtained
- [ ] On-call engineer notified
- [ ] Deployment approved: **Immediate** / Within <!-- timeframe -->

---

*Hotfix deployed: [date]*
*Incident duration: [duration]*
*Post-mortem due: [date]*

<!--
🔥 Hotfix process:
✓ Minimal fix only (address root cause)
✓ Skip normal process when urgent
✓ Document everything thoroughly
✓ Follow up with proper PR + post-mortem
-->
