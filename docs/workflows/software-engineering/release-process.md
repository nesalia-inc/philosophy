# Release Process

How to release new versions of your software.

---

## Overview

```
MAIN BRANCH ──► VERSION ──► BUILD ──► TEST ──► DEPLOY ──► MONITOR
    │            │          │         │         │          │
    ▼            ▼          ▼         ▼         ▼          ▼
  Ready      bump        create    verify   deploy    watch
   for       version    artifact  on staging  to prod  for issues
  release
```

---

## Versioning Strategy

### Semantic Versioning

Format: `MAJOR.MINOR.PATCH`

```
1.2.3
│ │ │
│ │ └─ PATCH: Bug fixes, no breaking changes
│ └─── MINOR: New features, backward compatible
└───── MAJOR: Breaking changes
```

### When to Bump Version

| Type | Example | Bump |
|------|---------|------|
| Bug fix | Fix login bug | PATCH |
| New feature | Add password reset | MINOR |
| Breaking change | Change API response | MAJOR |

---

## Release Workflow

### Step 1: Prepare Release

Ensure main branch is ready:

```bash
# Checkout main
git checkout main

# Pull latest
git pull origin main

# Run full CI
npm run ci
# ✓ All checks pass
```

### Step 2: Create Release PR

```bash
# Create release branch
git checkout -b release/v1.2.0

# Update version
npm version minor
# v1.2.0

# Commit
git commit -m "release: v1.2.0"

# Push
git push origin release/v1.2.0

# Create PR to main
```

### Step 3: Release PR Checklist

- [ ] All features merged
- [ ] All tests pass
- [ ] Changelog updated
- [ ] Version bumped
- [ ] No breaking changes (or documented)

### Step 4: Merge and Tag

```bash
# Merge release to main
git checkout main
git merge release/v1.2.0

# Create tag
git tag -a v1.2.0 -m "Release v1.2.0"

# Push
git push origin main --tags
```

---

## Automated Releases

### Using Changesets

Setup changesets for automatic versioning:

```bash
# Install
npm install -D @changesets/cli

# Initialize
npx changeset init
```

### Changeset Flow

```markdown
# In your PR, add changeset:

---
'@org/package': minor
---

# This creates a changeset file
```

### Release Script

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    branches:
      - main

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20

      - run: npm ci

      - run: npm run build

      - run: npx changeset version
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - run: npx changeset publish
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

---

## Deployment Environments

### Development

- Deploys on push to `develop`
- Auto-deploys
- Less strict testing

### Staging

- Deploys on PR merge to `main`
- Full testing
- Mirror production

### Production

- Manual promotion from staging
- Strict testing
- Monitored

---

## Release Checklist

### Before Release

- [ ] All tests pass
- [ ] Build succeeds
- [ ] Changelog updated
- [ ] Version bumped
- [ ] Breaking changes documented

### During Release

- [ ] PR merged to main
- [ ] Tag created
- [ ] CI pipeline runs
- [ ] Deployment succeeds

### After Release

- [ ] Version published (npm, etc.)
- [ ] GitHub release created
- [ ] Changelog on GitHub
- [ ] Team notified
- [ ] Monitored for issues

---

## Changelog

### Format

```markdown
# Changelog

## v1.2.0 - 2026-03-06

### Features
- Add password reset functionality
- New user profile page

### Bug Fixes
- Fix login redirect loop
- Resolve email validation

### Breaking Changes
- `authenticate()` now returns Result<User, Error>
- API response format changed (see migration guide)

## v1.1.0 - 2026-02-20

[previous releases...]
```

### Auto-Generate

Use tools like conventional-changelog:

```bash
# Generate changelog
npx conventional-changelog -p angular -i CHANGELOG.md -s
```

---

## Hotfix Release

For critical bugs in production:

```bash
# Create hotfix branch from tag
git checkout -b hotfix/v1.2.1 v1.2.0

# Fix the bug
# ... commit changes ...

# Version bump
npm version patch

# Merge
git checkout main
git merge hotfix/v1.2.1
git tag -a v1.2.1 -m "Hotfix v1.2.1"
git push origin main --tags
```

---

## Quick Reference

| Command | Purpose |
|---------|---------|
| `npm version patch` | Bump patch version |
| `npm version minor` | Bump minor version |
| `npm version major` | Bump major version |
| `git tag -a v1.0.0 -m "Release v1.0.0"` | Create tag |
| `git push origin --tags` | Push tags |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
