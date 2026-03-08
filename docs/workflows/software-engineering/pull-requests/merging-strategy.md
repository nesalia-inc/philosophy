# Merging Strategy

Guide to pull request merge strategies, when to use them, and best practices.

---

## Overview

Choosing the right merge strategy is crucial for maintaining a clean git history and ensuring code quality. This guide covers the different strategies and when to use each.

---

## Merge Strategies Comparison

```
┌─────────────────────────────────────────────────────────────────────┐
│                     MERGE STRATEGY COMPARISON                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  Strategy        History Clean   Preserves Context   Best For       │
│  ─────────────   ──────────────  ──────────────────  ────────────   │
│  Squash & Merge  ✓ Yes           ✗ No                Most PRs       │
│  Merge Commit    ✗ No            ✓ Yes               Large features  │
│  Rebase & Merge  ✓ Yes           ✗ No                Maintainers    │
│  Fast-forward    ✓ Yes           ✓ Yes                Trunk-based    │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Default Strategy: Squash and Merge

### What It Does

```bash
# Before: 10 commits on feature branch
feature/user-auth ──► c1 ──► c2 ──► ... ──► c10

# After: 1 commit on main
main ──► feat: add user authentication
```

All commits from the feature branch are combined into a single commit on the main branch.

### When to Use

```bash
✅ 99% of pull requests
✅ Feature branches
✅ Bug fix branches
✅ Refactor branches
✅ Documentation changes
✅ Small to medium PRs
```

### Why It's Our Default

```bash
✅ Clean git history
✅ One feature = one commit
✅ Easy to revert
✅ Easy to bisect
✅ Encourages clean commits
✅ Review feedback incorporated
```

### How to Do It

```bash
# Via GitHub UI (recommended)
1. Go to PR
2. Click "Merge pull request"
3. Select "Squash and merge"
4. Click confirm

# Via CLI (if you have merge permissions)
gh pr merge 123 --squash --subject "feat: add user authentication"
```

### Commit Message Format

```bash
# Squash commit should follow conventional commits
feat: add user authentication with JWT

- Implement login/logout
- Add session management
- Add tests

Closes #123
```

---

## Strategy: Merge Commit

### What It Does

```bash
# Before: 10 commits on feature branch
feature/user-auth ──► c1 ──► c2 ──► ... ──► c10

# After: Merge commit on main
main ──► Merge pull request #123
          │
          └─► feature/user-auth (c1...c10)
```

Preserves all original commits and creates a merge commit.

### When to Use

```bash
✅ Very large features (1000+ lines)
✅ Features with complex history
✅ When commit history is valuable
✅ When reverting might be needed
✅ Educational/visible work
```

### When NOT to Use

```bash
❌ Most PRs (use squash instead)
❌ When commits are messy
❌ When commits are "WIP" style
❌ When PR has lots of fixup commits
```

### How to Do It

```bash
# Via GitHub UI
1. Go to PR
2. Click "Merge pull request"
3. Select "Merge commit"
4. Click confirm

# Via CLI
gh pr merge 123 --merge
```

---

## Strategy: Rebase and Merge

### What It Does

```bash
# Before: 10 commits on feature branch
feature/user-auth ──► c1 ──► c2 ──► ... ──► c10

# After: 10 commits on main (replayed)
main ──► c1' ──► c2' ──► ... ──► c10'
```

Replays commits onto the main branch, preserving all individual commits.

### When to Use

```bash
✅ Maintainers cleaning up history
✅ When commits are already perfect
✅ When you want to preserve work-in-progress
✅ Personal projects
✅ Open source projects
```

### When NOT to Use

```bash
❌ Most team PRs (use squash instead)
❌ When commits are messy
❌ When PR has fixup commits
❌ When you want clean history
```

### Dangers

```bash
⚠️  Rewrites commit history
⚠️  Can cause conflicts
⚠️  Changes commit hashes
⚠️  Can break others' branches
⚠️  Harder to revert
```

### How to Do It

```bash
# Via GitHub UI
1. Go to PR
2. Click "Merge pull request"
3. Select "Rebase and merge"
4. Click confirm

# Via CLI
gh pr merge 123 --rebase
```

---

## Strategy: Fast-Forward

### What It Does

```bash
# Before: Direct ancestry
feature ──► c1 ──► c2 ──► c3
                 /
main ────────────► c0

# After: Just moves branch pointer
feature ────────────────┐
                       │
main ──────────────────┘ c3
```

Simply moves the main branch pointer forward if there are no divergent commits.

### When to Use

```bash
✅ Trunk-based development
✅ When no one else has committed
✅ Small teams with strict workflow
✅ Personal projects
```

### Requirements

```bash
⚠️  Requires no commits on main since branch
⚠️  Usually requires branch protection to be disabled
⚠️  Rare in team environments
```

### How to Do It

```bash
# Via Git (not available in GitHub UI for PRs)
git checkout main
git merge --ff-only feature/branch
```

---

## Decision Tree

```
                         Start
                           │
                           ▼
                  Is this a hotfix?
                    │           │
                   Yes          No
                    │           │
                    ▼           ▼
            Use Merge Commit   │
                    │           │
                    │           ▼
                    │    Is PR > 1000 lines?
                    │       │           │
                    │      Yes          No
                    │       │           │
                    │       ▼           ▼
                    │  Use Merge    Is commit history
                    │     Commit     valuable?
                    │       │           │
                    │       │     Yes     │     No
                    │       │      │      │      │
                    │       │      │      │      │
                    │       │      └──────┴──────┘
                    │       │              │
                    │       │              ▼
                    │       │      Use Squash and Merge
                    │       │              │
                    └───────┴──────────────┘
```

---

## Strategy by PR Type

### Feature PRs

```bash
# Default: Squash and Merge
gh pr merge 123 --squash

# Exception: Very large feature
gh pr merge 123 --merge
```

### Bug Fix PRs

```bash
# Always: Squash and Merge
gh pr merge 456 --squash

# One commit = one bug fix
```

### Hotfix PRs

```bash
# Use: Merge Commit
gh pr merge 789 --merge

# Why?
# - Clear what was done
# - Easy to revert
# - Preserves urgency
```

### Refactor PRs

```bash
# Default: Squash and Merge
gh pr merge 234 --squash

# One commit = one refactor
```

### Documentation PRs

```bash
# Default: Squash and Merge
gh pr merge 567 --squash

# Clean history is good
```

### Breaking Change PRs

```bash
# Use: Merge Commit
gh pr merge 890 --merge

# Why?
# - Clear what changed
# - Easier to see evolution
# - Easier to revert if needed
```

---

## Pre-Merge Checklist

Before clicking merge, verify:

```bash
✅ CI checks are green
   - Lint passes
   - Typecheck passes
   - Tests pass
   - Build succeeds

✅ Approval obtained
   - At least 1 approval
   - All reviewers approved
   - No blocking comments

✅ Philosophy aligned
   - Result<T, E> pattern used
   - Option<T> pattern used
   - No any types
   - Functions < 50 lines
   - Real tests, not mocks

✅ Documentation complete
   - PR description filled out
   - Code comments added (if needed)
   - API docs updated (if public API)
   - README updated (if needed)

✅ Tests adequate
   - New features have tests
   - Bug fixes have regression tests
   - Coverage not decreased
   - Tests are real, not mocks

✅ Branch up to date
   - Synced with main
   - No merge conflicts
   - Rebased if needed
```

---

## Post-Merge Actions

### Immediately After Merge

```bash
# 1. Delete branch (GitHub does this automatically)
# Or manually:
git branch -d feature/branch
git push origin --delete feature/branch

# 2. Pull latest main
git checkout main
git pull origin main

# 3. Update local branches
# Rebase your other branches on updated main
git checkout feature/other-branch
git rebase main

# 4. Clean up
git fetch origin --prune
```

### Verify Deployment

```bash
# For production deployments:
1. Check CI/CD pipeline
2. Verify staging environment
3. Monitor for errors
4. Check key metrics

# For hotfixes:
1. Verify production immediately
2. Monitor error logs
3. Check system health
```

---

## Reverting Merges

### Squash and Merge Revert

```bash
# Easy to revert - one commit
git revert <commit-hash>
git push origin main

# Or via GitHub:
gh pr view <commit-hash>
gh pr create --title "Revert: feat: add user authentication"
```

### Merge Commit Revert

```bash
# Also easy to revert
git revert -m 1 <merge-commit-hash>
git push origin main

# The -m 1 specifies the main branch parent
```

### Rebase and Merge Revert

```bash
# Harder - multiple commits to revert
git revert <c10-hash> <c9-hash> ... <c1-hash>
git push origin main

# Or revert the whole range:
git revert <oldest-hash>..<newest-hash>
```

### Revert Strategy

```bash
# For simple reverts:
1. Create revert PR
2. Use same merge strategy as original
3. Get approval
4. Merge

# For complex reverts:
1. Discuss in issue first
2. Plan the revert
3. Create revert PR
4. Fix forward if needed
```

---

## Common Issues

### Merge Conflicts

```bash
# Before merging:
1. Update branch
git checkout feature/branch
git fetch origin main
git rebase origin/main

2. Resolve conflicts
# Edit conflicted files
git add <files>
git rebase --continue

3. Push
git push origin feature/branch --force-with-lease

4. Now PR can be merged
```

### "Can't Merge" in GitHub

```bash
# Causes:
- Branch is behind main
- Merge conflicts exist
- Branch protection rules not met

# Solutions:
1. Update branch (see above)
2. Get required approvals
3. Wait for CI to pass
```

### Accidental Wrong Merge Strategy

```bash
# If you used wrong strategy:

# Option 1: Revert and redo
git revert <merge-commit>
# Redo with correct strategy

# Option 2: If squash was used but merge wanted:
# You can't easily convert - just revert and redo

# Option 3: If merge was used but squash wanted:
# Same - revert and redo
```

---

## Best Practices

### For Mergers

```bash
✅ Use squash and merge by default
✅ Verify CI is green before merging
✅ Ensure proper approvals
✅ Delete branches after merge
✅ Write good commit messages
✅ Revert if issues arise
```

### For Authors

```bash
✅ Keep commits clean
✅ Write good commit messages
✅ Don't include WIP commits in final PR
✅ Squash fixup commits before requesting review
✅ Update commit message during merge if needed
```

### For Teams

```bash
✅ Agree on default strategy
✅ Document exceptions
✅ Train team on tools
✅ Review merge history regularly
✅ Use branch protection rules
```

---

## Branch Protection Settings

### Recommended Configuration

```yaml
# .github/branch-protection.yml
main:
  # Require PR before merging
  require_pull_request: true

  # Require approvals
  required_approving_review_count: 1

  # Dismiss stale reviews
  dismiss_stale_reviews_on_push: true

  # Require status checks
  required_status_checks:
    - lint
    - typecheck
    - test
    - build

  # Require branches to be up to date
  require_branch_up_to_date: true

  # Limit who can merge
  restrict_pushes: true
  restrict_pushes_to:
    - main-maintainers

  # Don't allow force pushes
  allow_force_pushes: false

  # Don't allow deletions
  allow_deletions: false
```

---

## Related Documentation

- [Branching Strategy](./branching-strategy.md) - Branch workflows
- [Review Guidelines](./review-guidelines.md) - How to review
- [PR Lifecycle](./lifecycle.md) - PR stages and policies
- [Quality Gates](./quality-gates.md) - Quality requirements

---

*Document version: 1.0*
*Last updated: 2025-03-08*
