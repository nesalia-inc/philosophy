# Branching Strategy

Complete guide to Git branching workflows, conventions, and best practices.

---

## Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                        BRANCHING STRATEGY                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  main (protected)                                                    │
│  ├─ Always deployable                                                │
│  ├─ No direct commits                                               │
│  └─ Requires PR + review                                            │
│                                                                      │
│  feature/fix/refactor branches                                      │
│  ├─ Branch from main                                                │
│  ├─ Short-lived (< 3 days)                                          │
│  └─ Merge back via PR                                               │
│                                                                      │
│  release branches (optional)                                        │
│  ├─ For production releases                                         │
│  ├─ Stabilization period                                            │
│  └─ Merge to main + tag                                             │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Branch Types

### 🏁 Main Branch

```bash
main
├─ Protected: No direct pushes
├─ Always deployable to production
├─ Requires PR for changes
└─ Auto-deploys on merge
```

**Rules:**
- ✅ Always green (passing CI)
- ✅ Deployable at any time
- ✅ No broken builds allowed
- ❌ No direct commits
- ❌ No force pushes

### 🚀 Feature Branches

```bash
feature/<short-description>

Examples:
feature/user-authentication
feature/payment-processing
feature/dashboard-widgets
```

**Purpose:** New features and functionality

**Lifecycle:**
```bash
1. Create:  git checkout -b feature/user-authentication
2. Work:    Make commits, push regularly
3. Test:    Ensure all tests pass
4. PR:      Create pull request to main
5. Review:  Address feedback
6. Merge:   Squash and merge
7. Delete:  Branch deleted after merge
```

**Duration:** < 3 days (ideally < 1 day)

### 🐛 Bug Fix Branches

```bash
fix/<short-description>

Examples:
fix/login-redirect-loop
fix/database-connection-leak
fix/memory-leak-image-processing
```

**Purpose:** Fix bugs in production code

**Lifecycle:** Same as feature branches

**Priority:** Higher than features

### ♻️ Refactor Branches

```bash
refactor/<short-description>

Examples:
refactor/auth-to-result-pattern
refactor/remove-callback-hell
refactor-simplify-data-flow
```

**Purpose:** Code quality improvements without behavior changes

**Rules:**
- ✅ All existing tests must still pass
- ✅ No behavior changes
- ✅ Document what changed and why

### 📚 Documentation Branches

```bash
docs/<short-description>

Examples:
docs/api-authentication
docs/readme-installation
docs-contributing-guide
```

**Purpose:** Documentation only changes

**Benefits:**
- Lighter review required
- Faster merge process
- Can skip some quality gates

### 🔐 Security Branches

```bash
security/<short-description>

Examples:
security/sql-injection-fix
security/xss-prevention
security-auth-bypass-fix
```

**Purpose:** Security vulnerability fixes

**Special handling:**
- Urgent review required
- Security team approval
- May follow hotfix process if critical

### ⚡ Performance Branches

```bash
perf/<short-description>

Examples:
perf/database-query-optimization
perf-cache-implementation
perf-image-compression
```

**Purpose:** Performance optimizations

**Requirements:**
- Before/after benchmarks
- Performance tests
- No regressions

### 🔥 Hotfix Branches

```bash
hotfix/<short-description>

Examples:
hotfix-payment-crash
hotfix-api-down
hotfix-data-corruption
```

**Purpose:** Emergency production fixes

**Workflow:**
```bash
1. Create:  git checkout -b hotfix/payment-crash
2. Fix:     Minimal fix only
3. Test:    Smoke tests locally
4. Merge:   Expedited review + merge
5. Deploy:  Immediately to production
6. Post:    Create proper PR with tests later
```

**Special rules:**
- Can bypass some normal processes
- Minimal testing acceptable
- Requires post-mortem
- Must create proper follow-up PR

---

## Branch Naming Conventions

### Format

```bash
<prefix>/<kebab-case-description>[#<ticket-number>]

Examples:
✅ feature/user-authentication#123
✅ fix/login-redirect#456
✅ refactor/payment-processor
✅ docs-api-endpoints
✅ hotfix-payment-crash

❌ feature_user_auth           # Use hyphens
❌ newFeature                   # Needs prefix
❌ feature/123                  # Include description
❌ FEATURE/user-auth            # Use lowercase
```

### Prefixes

| Prefix | Usage | Review Level |
|--------|-------|--------------|
| `feature/` | New features | Standard |
| `fix/` | Bug fixes | Standard |
| `refactor/` | Code quality | Standard |
| `docs/` | Documentation | Light |
| `perf/` | Performance | Standard |
| `security/` | Security fixes | Strict |
| `test/` | Test changes | Standard |
| `chore/` | Maintenance | Light |
| `hotfix/` | Emergencies | Expedited |

### Description Guidelines

```bash
✅ feature/user-profile-page
✅ fix/database-connection-leak
✅ refactor/auth-service-result-types
✅ docs-api-authentication-flow
✅ perf-cache-layer-implementation

❌ feature/stuff               # Too vague
❌ fix/bug                     # What bug?
❌ refactor/code               # What code?
❌ docs/update                 # Update what?
```

---

## Workflow

### Standard Feature Workflow

```bash
# 1. Start from clean main
git checkout main
git pull origin main

# 2. Create feature branch
git checkout -b feature/user-authentication

# 3. Do work
git add -A
git commit -m "feat: add user authentication service"
git push origin feature/user-authentication

# 4. Regular sync with main
git fetch origin main
git rebase origin/main

# 5. Create PR on GitHub
gh pr create --template feature.md

# 6. After approval, squash merge
# (Via GitHub UI)

# 7. Delete branch
git checkout main
git pull origin main
git branch -d feature/user-authentication
```

### Bug Fix Workflow

```bash
# Same as feature, but with fix/ prefix
git checkout -b fix/login-redirect-loop
# ... work, commit, push
gh pr create --template bugfix.md
```

### Hotfix Workflow

```bash
# 1. Create hotfix branch
git checkout -b hotfix/payment-crash

# 2. Minimal fix
# Make minimal changes to fix issue

# 3. Quick test
npm run test:smoke

# 4. Commit and push
git add -A
git commit -m "hotfix: fix payment processing crash"
git push origin hotfix/payment-crash

# 5. Expedited PR
gh pr create --template hotfix.md --label urgent

# 6. Immediate merge after quick review
gh pr merge 123 --squash --admin

# 7. Deploy
# CI/CD auto-deploys

# 8. Create proper follow-up
# Create proper PR with full tests later
```

---

## Branch Protection Rules

### Main Branch Protection

```yaml
# .github/branch-protection-rules.yml
main:
  # Who can push
  push_restrictions:
    - nobody  # No direct pushes

  # Required reviews
  required_reviews:
    minimum: 1
    dismiss_stale: true
    require_code_owner: false

  # Required status checks
  required_checks:
    - lint
    - typecheck
    - test
    - build

  # Require branches to be up to date
  require_up_to_date: true

  # Restrictions
  allow_deletions: false
  allow_force_pushes: false
```

### Enforced via GitHub UI

```
Settings → Branches → Add rule
Pattern: main
✅ Require status checks to pass
✅ Require branches to be up to date
✅ Require pull request reviews
  - Required approving reviews: 1
  - Dismiss stale reviews
✅ Do not allow bypassing the above settings
✅ Restrict who can push
✅ Do not allow force pushes
```

---

## Synchronization

### Keeping Branches Up to Date

```bash
# Method 1: Rebase (preferred)
git fetch origin main
git rebase origin/main

# Method 2: Merge (if team prefers)
git fetch origin main
git merge origin/main
```

### When to Sync

```bash
✅ Before creating PR
✅ Before requesting re-review
✅ When main has significant changes
✅ When merge conflicts occur
```

### Dealing with Merge Conflicts

```bash
# 1. Update main
git checkout main
git pull origin main

# 2. Rebase your branch
git checkout feature/your-branch
git rebase main

# 3. Resolve conflicts
# Edit conflicted files
git add <resolved-files>
git rebase --continue

# 4. Force push (carefully!)
git push origin feature/your-branch --force-with-lease
```

---

## Branch Lifecycle

### Duration Guidelines

| Branch Type | Max Duration | Ideal Duration |
|-------------|--------------|----------------|
| feature/ | 3 days | 1 day |
| fix/ | 2 days | 4 hours |
| refactor/ | 5 days | 2 days |
| docs/ | 2 days | 2 hours |
| perf/ | 5 days | 2 days |
| security/ | 1 day | 2 hours |
| hotfix/ | 1 hour | 30 minutes |

### Stale Branch Cleanup

```bash
# Automated cleanup via GitHub
# Settings → Branches → Add rule → "Stale branches"
# Delete after: 30 days inactive

# Manual cleanup
git checkout main
git pull
git branch -a
git push origin --delete feature/stale-branch
```

---

## Commit Conventions

### Commit Message Format

```bash
<type>: <description>

[optional body]

[optional footer]
```

### Types

```bash
feat     - New feature
fix      - Bug fix
refactor - Code refactoring
docs     - Documentation only
perf     - Performance improvement
test     - Adding/updating tests
chore    - Maintenance tasks
style    - Code style changes
```

### Examples

```bash
✅ feat: add user authentication with JWT
✅ fix: resolve database connection leak
✅ refactor: migrate to Result<T, E> pattern
✅ docs: update API authentication documentation
✅ perf: optimize database queries

❌ Added authentication feature
❌ Fixed bug
❌ Update docs
❌ WIP
❌ fix stuff
```

---

## Anti-Patterns

### ❌ Don't Do This

```bash
# Long-lived branches
feature/feature-branch-from-3-weeks-ago

# Vague naming
feature/stuff
fix/bug

# Direct commits to main
git checkout main
git commit -m "quick fix"

# Force push to shared branches
git push origin main --force

# Merge without PR
git checkout main
git merge feature/branch

# Huge feature branches
feature/everything-for-v2
```

### ✅ Do This Instead

```bash
# Short, focused branches
feature/user-authentication

# Clear naming
feature/user-profile-page
fix/login-redirect-loop

# Always use PRs
gh pr create --template feature.md

# Small, incremental changes
feature/user-authentication
feature/user-profile
feature/user-settings
```

---

## Integration with Philosophy

### Result<T, E> Pattern in Branches

When refactoring to Result<T, E>:

```bash
# Create refactor branch
git checkout -b refactor/auth-to-result-pattern

# Follow incremental approach:
# 1. Internal functions first
# 2. Then public API
# 3. Update all call sites
# 4. Update tests

# Each step in its own commit
git commit -m "refactor: internal auth functions to Result<T, E>"
git commit -m "refactor: public auth API to Result<T, E>"
git commit -m "refactor: update auth call sites"
git commit -m "refactor: update auth tests for Result<T, E>"
```

### Option<T> Pattern

When migrating to Option<T>:

```bash
git checkout -b refactor/database-to-option-pattern

# Incremental migration
git commit -m "refactor: database queries return Option<T>"
git commit -m "refactor: update query consumers for Option<T>"
git commit -m "refactor: remove null checks, use pattern matching"
```

---

## Related Documentation

- [Pull Request Templates](./templates/README.md) - PR templates
- [PR Lifecycle](./lifecycle.md) - PR stages and policies
- [Merging Strategy](./merging-strategy.md) - How to merge
- [Review Guidelines](./review-guidelines.md) - How to review

---

*Document version: 1.0*
*Last updated: 2025-03-08*
