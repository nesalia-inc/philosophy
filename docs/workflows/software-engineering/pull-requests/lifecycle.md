# Pull Request Lifecycle

Complete guide to PR stages, timelines, and SLA policies.

---

## Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                      PULL REQUEST LIFECYCLE                         │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  DRAFT → READY → REVIEW → CHANGES → APPROVED → MERGED → DEPLOYED   │
│                                                                      │
│  Each stage has:                                                    │
│  - Purpose                                                          │
│  - Entry criteria                                                   │
│  - Exit criteria                                                    │
│  - SLA targets                                                      │
│  - Owner responsibilities                                           │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Stage 1: Draft

### Purpose

Work-in-progress PRs that are not ready for review.

```
┌─────────────────────────────────────────┐
│              DRAFT PR                    │
├─────────────────────────────────────────┤
│                                         │
│  • Work in progress                     │
│  • No review requested yet              │
│  • Can be incomplete                    │
│  • Safe to push frequently              │
│                                         │
└─────────────────────────────────────────┘
```

### When to Use

```bash
✅ Starting a new feature
✅ Work is incomplete
✅ Want feedback on approach
✅ Collaborating with others
✅ Large feature split into parts
```

### Entry Criteria

```bash
✅ Branch created from main
✅ At least one commit pushed
✅ PR created (marked as draft)
```

### Exit Criteria

```bash
✅ All code complete
✅ All tests passing
✅ Ready for review
```

### Best Practices

```bash
# 1. Mark as draft on GitHub
gh pr create --draft

# 2. Work openly
# Push frequently, get early feedback

# 3. Update description as you go
# Keep PR description current

# 4. Don't let drafts linger
# Aim to convert to ready within 1-2 days
```

### Duration

```bash
Target: < 2 days
Warning: > 3 days
Critical: > 5 days
```

---

## Stage 2: Ready for Review

### Purpose

PR is complete and ready for thorough review.

```
┌─────────────────────────────────────────┐
│          READY FOR REVIEW                │
├─────────────────────────────────────────┤
│                                         │
│  • Code complete                        │
│  • Tests passing                        │
│  • Description filled out               │
│  • Reviewers requested                  │
│  • CI checks running/passed             │
│                                         │
└─────────────────────────────────────────┘
```

### Entry Criteria

```bash
✅ Draft → Ready conversion
✅ All code committed
✅ Tests passing locally
✅ PR template filled out
✅ CI checks in progress or passed
```

### Checklist Before Marking Ready

```bash
# Code quality
✅ Code follows .ai/rules.md
✅ No any types
✅ Result<T, E> pattern used
✅ Option<T> pattern used
✅ Functions < 50 lines

# Testing
✅ All new code has tests
✅ Tests pass locally
✅ Coverage adequate

# Documentation
✅ PR description complete
✅ Complex code has comments
✅ API docs updated (if needed)

# CI
✅ Lint passes
✅ Typecheck passes
✅ Tests pass
✅ Build succeeds
```

### Actions

```bash
# 1. Convert draft to ready
gh pr ready 123

# 2. Request reviewers
gh pr edit 123 --add-reviewer @reviewer1 @reviewer2

# 3. Set labels
gh pr edit 123 --add-label "feature,needs-review"

# 4. Post in team chat
# "PR #123 is ready for review"
```

### Duration

```bash
# How long PR should wait for review
Target: < 24 hours for first review
Warning: > 48 hours
Critical: > 72 hours
```

---

## Stage 3: Review in Progress

### Purpose

Reviewers are examining the code, asking questions, and providing feedback.

```
┌─────────────────────────────────────────┐
│          REVIEW IN PROGRESS              │
├─────────────────────────────────────────┤
│                                         │
│  • Reviewers assigned                   │
│  • Code being examined                 │
│  • Questions and comments               │
│  • Author responding                    │
│                                         │
└─────────────────────────────────────────┘
```

### Entry Criteria

```bash
✅ Ready for review
✅ Reviewers requested
✅ CI checks passing
```

### Reviewer Responsibilities

```bash
# Review timeline
Within 24 hours: First review pass
Within 12 hours: Response to author questions

# Review quality
✅ Thorough examination
✅ Clear, actionable feedback
✅ Philosophy alignment check
✅ Test verification
✅ Security review (if applicable)
```

### Author Responsibilities

```bash
# Response timeline
Within 12 hours: Respond to comments
Within 24 hours: Address feedback

# Response quality
✅ Answer all questions
✅ Address or explain all feedback
✅ Push updates promptly
✅ Re-request review when ready
```

### Duration

```bash
Target: 1-2 rounds of review
Typical: 2-3 days
Warning: > 5 days
Critical: > 7 days
```

---

## Stage 4: Changes Requested

### Purpose

Reviewer found issues that need to be addressed.

```
┌─────────────────────────────────────────┐
│         CHANGES REQUESTED                │
├─────────────────────────────────────────┤
│                                         │
│  • Feedback provided                    │
│  • Issues identified                    │
│  • Changes needed                       │
│  • Re-review required                   │
│                                         │
└─────────────────────────────────────────┘
```

### Entry Criteria

```bash
✅ Review completed
✅ Changes requested by reviewer
✅ PR not approved
```

### Types of Feedback

```bash
# Must fix (blocking)
❌ Logic errors
❌ Security issues
❌ Pattern violations
❌ Missing tests
❌ Breaking changes without documentation

# Should fix (strongly suggested)
💡 Performance issues
💡 Code organization
💡 Naming improvements
💡 Better approaches

# Nice to have (optional)
🎨 Style preferences
🎨 Minor optimizations
🎨 Documentation improvements
```

### Author Actions

```bash
# 1. Address feedback
git add -A
git commit -m "fix: address review feedback"

# 2. Push updates
git push

# 3. Respond to each comment
# "Done" or explain why not

# 4. Re-request review
gh pr edit 123 --add-reviewer @reviewer
```

### Duration

```bash
Target: < 24 hours to address
Typical: Same day
Warning: > 48 hours
Critical: > 72 hours
```

---

## Stage 5: Approved

### Purpose

All reviewers approved the PR, ready to merge.

```
┌─────────────────────────────────────────┐
│             APPROVED                    │
├─────────────────────────────────────────┤
│                                         │
│  • All reviewers approved               │
│  • All feedback addressed               │
│  • CI checks passing                    │
│  • Ready to merge                      │
│                                         │
└─────────────────────────────────────────┘
```

### Entry Criteria

```bash
✅ All requested reviewers approved
✅ All blocking feedback addressed
✅ CI checks passing
✅ No unresolved conversations
```

### Pre-Merge Checklist

```bash
# Final verification
✅ All approvals obtained
✅ CI checks green
✅ Philosophy compliance verified
✅ Tests adequate
✅ Documentation complete
✅ No merge conflicts
✅ Branch up to date with main
```

### Merge Ownership

```bash
# Who can merge?
✅ Author (if not self-approval)
✅ Reviewer
✅ Maintainer

# When to merge?
✅ Immediately after approval
✅ During business hours (for risky changes)
✅ After planned time (for scheduled deployments)
```

### Duration

```bash
Target: < 4 hours after approval
Typical: Same day
Warning: > 1 day
Critical: > 2 days
```

---

## Stage 6: Merged

### Purpose

PR has been merged into main branch.

```
┌─────────────────────────────────────────┐
│              MERGED                     │
├─────────────────────────────────────────┤
│                                         │
│  • Squash and merged                    │
│  • Branch deleted                       │
│  • Issue closed                         │
│  • CI/CD running                        │
│                                         │
└─────────────────────────────────────────┘
```

### Entry Criteria

```bash
✅ Approved
✅ Merge strategy selected
✅ Merge executed
```

### Post-Merge Actions

```bash
# Automatic (CI/CD)
✅ Branch deleted
✅ Issue closed
✅ Deployment triggered

# Manual (author)
✅ Verify deployment
✅ Monitor for issues
✅ Notify team (if needed)
✅ Update documentation (if missed)
```

### Merge Strategies

```bash
# Default: Squash and merge
gh pr merge 123 --squash

# Exceptions:
# Large features: Merge commit
gh pr merge 123 --merge

# Hotfixes: Merge commit
gh pr merge 789 --merge
```

---

## Stage 7: Deployed

### Purpose

Changes are live in production.

```
┌─────────────────────────────────────────┐
│             DEPLOYED                    │
├─────────────────────────────────────────┤
│                                         │
│  • Deployed to staging/production       │
│  • Monitoring active                    │
│  • Verification complete               │
│                                         │
└─────────────────────────────────────────┘
```

### Entry Criteria

```bash
✅ Merged to main
✅ CI/CD deployment complete
✅ Health checks passing
```

### Post-Deployment

```bash
# Verification
✅ Smoke tests pass
✅ Key features work
✅ No errors in logs
✅ Metrics normal

# Monitoring
✅ Error rates
✅ Response times
✅ User feedback
✅ System health
```

### Rollback Plan

```bash
# If issues detected:
1. Identify the problem
2. Assess severity
3. Rollback if critical
4. Fix and redeploy

# Rollback methods
git revert <commit-hash>
# Or
# Deploy previous version
```

---

## SLA Policies

### Response Time Targets

```bash
┌─────────────────────────────────────────────────────────┐
│                   SERVICE LEVEL AGREEMENTS              │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  STAGE                     TARGET        WARNING        │
│  ──────────────────────    ─────────     ─────────      │
│  First review              < 24h         > 48h           │
│  Response to feedback      < 12h         > 24h           │
│  Address feedback          < 24h         > 48h           │
│  Re-review                 < 24h         > 48h           │
│  Merge after approval      < 4h          > 1 day         │
│  Total PR lifetime         < 5 days      > 10 days       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Escalation Paths

```bash
# If SLAs are breached:

1. First review > 48h
   → Tag tech lead
   → Reassign reviewer

2. Response > 24h
   → Ping in team chat
   → Escalate to lead

3. Merge > 1 day
   → Any maintainer can merge
   → Author can request merge

4. PR > 10 days
   → Automatic stale label
   → Consider closing
```

---

## Lifecycle Metrics

### Track These Metrics

```bash
# Team metrics
- Average time to first review
- Average time to merge
- Average PR size (lines)
- Number of review rounds
- Rejection rate
- Stale PR rate

# Individual metrics
- Reviews given per week
- Reviews received per week
- Average review response time
- PR approval rate
```

### Goals

```bash
🎯 Time to first review: < 24 hours
🎯 Time to merge: < 5 days
🎯 PR size: < 400 lines
🎯 Review rounds: 2-3
🎯 Rejection rate: < 40%
🎯 Stale PR rate: < 5%
```

---

## Stale PRs

### What Is a Stale PR?

```bash
# A PR is stale if:
- No activity for 7+ days
- Not in draft state
- No pending responses from author
```

### Handling Stale PRs

```bash
# Automated (GitHub bot)
1. Add "stale" label after 7 days
2. Close after 14 days
3. Comment before closing

# Manual
1. Ping author
2. Ask if still relevant
3. Offer to take over
4. Close if unresponsive
```

### Preventing Stale PRs

```bash
✅ Don't create PRs and disappear
✅ Respond to feedback promptly
✅ Keep PRs small and focused
✅ Mark as WIP if not ready
✅ Close PRs that are no longer needed
```

---

## Emergency PRs

### Hotfix Lifecycle (Accelerated)

```bash
# Hotfix process (bypasses normal stages)

DRAFT → READY → APPROVED → MERGED → DEPLOYED
  │        │         │         │         │
  └────────┴─────────┴─────────┴─────────┘
        All within < 2 hours

# Bypassed:
- Extended review
- Multiple rounds
- Some non-critical checks
```

### Hotfit SLAs

```bash
🔥 Critical: < 30 minutes end-to-end
⚡ Urgent: < 2 hours end-to-end
⏰ High priority: < 4 hours end-to-end
```

---

## PR Lifecycle Dashboard

### What to Monitor

```bash
# Daily checks
- PRs waiting > 24h for review
- PRs with changes requested > 48h
- PRs approved but not merged > 4h
- PRs with no activity > 3 days

# Weekly checks
- Average time to merge
- PR size trends
- Review participation
- Stale PR rate

# Monthly checks
- SLA compliance rate
- Team velocity
- Quality metrics
- Process improvements
```

---

## Related Documentation

- [Pull Request Templates](./templates/README.md) - PR templates
- [Review Guidelines](./review-guidelines.md) - How to review
- [Branching Strategy](./branching-strategy.md) - Branch workflows
- [Merging Strategy](./merging-strategy.md) - How to merge

---

*Document version: 1.0*
*Last updated: 2025-03-08*
