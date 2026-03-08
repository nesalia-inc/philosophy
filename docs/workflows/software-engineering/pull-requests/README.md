# Pull Request Management

Complete guide to pull request workflows, templates, and best practices.

---

## Overview

This directory contains everything you need to create, manage, and review pull requests effectively.

```
pull-requests/
├── README.md                    # This file - main guide
├── templates/                   # PR templates for all scenarios
│   ├── README.md               # How to choose the right template
│   ├── feature.md              # New features
│   ├── bugfix.md               # Bug fixes
│   ├── refactor.md             # Code refactoring
│   ├── documentation.md        # Documentation updates
│   ├── security.md             # Security fixes
│   ├── performance.md          # Performance optimizations
│   ├── hotfix.md               # Emergency hotfixes
│   ├── breaking-change.md      # Breaking changes
│   └── quick-fix.md            # Minimal quick fixes
├── branching-strategy.md       # Git branching conventions
├── review-guidelines.md        # How to review PRs effectively
├── merging-strategy.md         # Merge strategies and when to use them
├── quality-gates.md            # Automated quality checks
└── lifecycle.md                # PR lifecycle and SLA policies
```

---

## Quick Start

### 1. Choose the Right Template

Go to [templates/README.md](./templates/README.md) to find the appropriate template for your change.

### 2. Create a Branch

```bash
# Use the proper naming convention
git checkout -b <type>/<short-description>

# Examples:
git checkout -b feature/user-authentication
git checkout -b fix/login-redirect-loop
git checkout -b refactor/payment-processor
```

### 3. Make Your Changes

Follow the coding standards in `.ai/rules.md`:
- Use `Result<T, E>` for error handling
- Use `Option<T>` for optional values
- No `any` types
- Functions under 50 lines
- Write real tests, not mocks

### 4. Create the PR

When creating a PR on GitHub, select the appropriate template from the dropdown and fill it out completely.

---

## Branch Naming Conventions

Use these prefixes for your branches:

| Prefix | Usage | Example |
|--------|-------|---------|
| `feature/` | New features | `feature/user-authentication` |
| `fix/` | Bug fixes | `fix/login-redirect-loop` |
| `refactor/` | Code refactoring | `refactor/payment-processor` |
| `docs/` | Documentation only | `docs/api-endpoints` |
| `perf/` | Performance improvements | `perf/database-queries` |
| `security/` | Security fixes | `security/sql-injection-fix` |
| `hotfix/` | Emergency production fixes | `hotfix-payment-crash` |
| `test/` | Test additions/updates | `test-auth-coverage` |

### Branch Name Format

```bash
<prefix>/<kebab-case-description>

Examples:
✅ feature/user-profile-page
✅ fix/database-connection-leak
✅ refactor/auth-service-result-types
✅ docs-api-authentication-flow
✅ perf-cache-implementation

❌ feature_user_profile           # Use hyphens, not underscores
❌ newFeature                      # Start with prefix
❌ feature/userAuth                # Use kebab-case
❌ feature/123                     # Include description, not just ticket number
```

---

## PR Title Format

Follow conventional commit format:

```
<type>: <description>

Types:
  feat     - New feature
  fix      - Bug fix
  refactor - Code refactoring (no behavior change)
  docs     - Documentation only
  perf     - Performance improvement
  security - Security fix
  test     - Adding/updating tests
  chore    - Maintenance tasks
  style    - Code style changes (formatting)
```

### Examples

```bash
✅ feat: add user authentication with JWT
✅ fix: resolve database connection leak
✅ refactor: migrate to Result<T, E> pattern
✅ docs: update API authentication documentation
✅ perf: optimize database query performance
✅ security: fix SQL injection in user search
✅ test: increase auth service coverage to 95%

❌ Added auth feature                    # Missing type
❌ feat: authentication                 # Too vague
❌ FEAT: ADD USER AUTH                  # Don't use all caps
❌ fix bug                              # Missing colon and description
```

---

## Anatomy of a Good PR

### 1. Clear Title
- Follows conventional commit format
- Describes what changed, not why
- Under 72 characters

### 2. Complete Description
- **What**: What does this PR do?
- **Why**: Why is it needed?
- **How**: How does it work?
- Links to related issues
- Screenshots for UI changes

### 3. Test Plan
- How to verify the changes
- List of tests added
- Test results showing they pass
- Manual testing steps

### 4. Quality Checklist
- No `any` types
- Uses `Result<T, E>` for errors
- Uses `Option<T>` for optionals
- Functions under 50 lines
- Real tests, not mocks
- All CI checks pass

### 5. Checklist Sections
- [ ] Code follows `.ai/rules.md`
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] No lint errors
- [ ] TypeScript compiles
- [ ] All tests pass

---

## PR Lifecycle

```
┌─────────────────────────────────────────────────────────────────────┐
│                        PULL REQUEST LIFECYCLE                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. DRAFT                                                            │
│     ├─ Branch created and work in progress                          │
│     ├─ Mark as "Draft" PR on GitHub                                 │
│     └─ No review requested yet                                      │
│                                                                      │
│  2. READY FOR REVIEW                                                │
│     ├─ All commits pushed                                           │
│     ├─ Draft → Ready for review                                     │
│     ├─ Reviewers requested                                          │
│     └─ CI checks running                                            │
│                                                                      │
│  3. REVIEW IN PROGRESS                                              │
│     ├─ Reviewers examining code                                     │
│     ├─ Comments and questions asked                                 │
│     └─ Author addressing feedback                                   │
│                                                                      │
│  4. CHANGES REQUESTED                                               │
│     ├─ Reviewer found issues                                        │
│     ├─ Author makes changes                                         │
│     └─ Re-request review                                            │
│                                                                      │
│  5. APPROVED                                                         │
│     ├─ All reviewers approved                                       │
│     ├─ All CI checks green                                          │
│     └─ Ready to merge                                               │
│                                                                      │
│  6. MERGED                                                           │
│     ├─ Squash and merge                                             │
│     ├─ Branch deleted                                               │
│     ├─ Issue closed automatically                                   │
│     └─ Deployed to staging/production                              │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Quality Gates

All PRs must pass these checks before merging:

### Automated Checks
```bash
✓ npm run lint          # No lint errors
✓ npm run typecheck     # No TypeScript errors
✓ npm run test          # All tests pass
✓ npm run build         # Build succeeds
✓ Coverage > 80%        # Test coverage threshold
```

### Manual Review
- [ ] Follows `.ai/rules.md`
- [ ] No `any` types
- [ ] Uses `Result<T, E>` pattern
- [ ] Uses `Option<T>` pattern
- [ ] Functions under 50 lines
- [ ] Tests are real (not mocks)
- [ ] Security review (if applicable)
- [ ] Performance review (if applicable)

---

## Related Documentation

- [Branching Strategy](./branching-strategy.md) - Git workflow and branch management
- [Review Guidelines](./review-guidelines.md) - How to conduct effective code reviews
- [Merging Strategy](./merging-strategy.md) - Merge strategies and when to use them
- [Quality Gates](./quality-gates.md) - Automated quality enforcement
- [PR Lifecycle](./lifecycle.md) - PR stages and SLA policies
- [Feature Development Workflow](../feature-development.md) - Complete feature workflow
- [Issue Management](../issues-management/issue-management.md) - Issue tracking and templates

---

## Best Practices Summary

### For Authors
✅ Keep PRs focused and small (< 400 lines)
✅ Write clear, descriptive commit messages
✅ Include tests for all changes
✅ Fill out the PR template completely
✅ Respond to review feedback promptly
✅ Update PR description if scope changes

❌ Don't mix unrelated changes
❌ Don't skip the PR template
❌ Don't ignore review feedback
❌ Don't merge your own PR without review
❌ Don't leave PRs in draft for weeks

### For Reviewers
✅ Review within 24 hours
✅ Be clear and specific in feedback
✅ Explain why, not just what
✅ Approve if feedback is addressed
✅ Test locally if uncertain
✅ Ask questions instead of making demands

❌ Don't delay reviews unnecessarily
❌ Don't be vague or dismissive
❌ Don't leave blocking comments without explanation
❌ Don't approve without thorough review
❌ Don't ignore the PR context

---

## Common Issues

### CI Failing

```bash
# Check what's failing
npm run ci

# Fix issues locally before pushing
npm run lint -- --fix
npm run typecheck
npm run test
```

### Merge Conflicts

```bash
# Update your branch
git checkout main
git pull
git checkout feature/your-branch
git rebase main

# Resolve conflicts
git add -A
git rebase --continue

# Push updates
git push origin feature/your-branch --force-with-lease
```

### PR Too Large

If your PR is over 500 lines:
1. Split into multiple smaller PRs
2. Refactor first in a separate PR
3. Focus on one logical change per PR

---

## SLA Policies

| Stage | Target |
|-------|--------|
| First review | Within 24 hours |
| Feedback response | Within 12 hours |
| Re-review | Within 24 hours |
| Merge after approval | Within 4 hours |

---

## Metrics We Track

- **Time to first review**: How quickly PRs get reviewed
- **Time to merge**: How long PRs take from creation to merge
- **PR size**: Average lines changed per PR
- **Rejection rate**: Percentage of PRs with "changes requested"
- **Review participation**: Who is reviewing what

---

## Tools & Automation

### GitHub Integrations
- **GitHub Actions**: CI/CD automation
- **Dependabot**: Dependency updates
- **CodeQL**: Security scanning
- **Labeler**: Automatic PR labeling

### CLI Tools
```bash
# Create PR from CLI
gh pr create --template feature.md

# View PR status
gh pr status

# Merge approved PR
gh pr merge 123 --squash
```

---

*Document version: 1.0*
*Last updated: 2025-03-08*
