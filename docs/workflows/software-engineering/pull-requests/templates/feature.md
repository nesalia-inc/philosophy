---
name: Feature Pull Request
about: Template for new features and functionality
title: 'feat: '
labels: feature
assignees: ''
---

## Summary
_What does this PR do in one sentence?_

**Example**: "Add user authentication with JWT tokens for secure API access"





## Motivation
_Why is this change needed? What problem does it solve?_

- Related issue: #
- Context: Why now? What's the user impact?





## Implementation
_How does this work at a high level?_

- **Key change 1**: Brief description
- **Key change 2**: Brief description
- **Key change 3**: Brief description

<details>
<summary>Technical details (click to expand)</summary>

### Files Added
<!-- Auto-detected by CI, shown for reference -->

### Files Modified
<!-- Auto-detected by CI, shown for reference -->

### Architecture decisions
- Any important architectural choices
- Trade-offs and why this approach
- Links to design docs if applicable

</details>

---

## Test Plan
_How do we verify this works correctly?_

- [ ] Unit tests pass locally (`npm run test`)
- [ ] Manual testing completed
- [ ] Edge cases covered (error paths, null handling, etc.)

<details>
<summary>Test commands (click to expand)</summary>

```bash
# Unit tests
npm run test

# Integration tests
npm run test:integration

# E2E tests (if applicable)
npm run test:e2e
```
</details>

---

## Impact Assessment

### Breaking Changes
- [ ] No breaking changes
- [ ] **Breaking changes** - see migration plan below

### Migration Guide
_If breaking changes, how do existing users upgrade?_

<!-- Example: "Update config.js to add new API_KEY field" -->

### Rollback Plan
_If something goes wrong in production, how do we undo?_

<!-- Example: "Revert commit, no data migration needed" -->

---

## Philosophy Checklist
_Only what CI cannot automatically verify_

### Design & Decisions
- [ ] Design follows project patterns (check similar features)
- [ ] Trade-offs documented in "Implementation" section
- [ ] Performance implications considered

### User Impact
- [ ] User-facing changes documented
- [ ] API changes documented (if public API)
- [ ] Database migrations are reversible

### Security & Privacy
- [ ] Input validation on all user inputs
- [ ] No secrets or sensitive data logged
- [ ] Security review completed (if applicable)

<!--
💡 CI automatically validates:
✓ Type checking (no implicit any, proper types)
✓ Linting (code style, no console.log)
✓ Tests (coverage > 80%)
✓ Code patterns (Result<T, E>, Option<T>, no throws)
✓ Function length (< 50 lines)

Focus this checklist on what requires human judgment.
-->

---

## Additional Context
_Anything else reviewers should know?_

- Relevant links (design docs, discussions, PRs in other repos)
- Areas you'd like specific feedback on
- Potential follow-up work identified

---

*This template focuses on what CI can't verify: design decisions, user impact, and trade-offs. automated checks handle code quality and patterns.*
