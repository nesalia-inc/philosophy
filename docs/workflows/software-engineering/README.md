# Software Engineering Handbook

Complete workflow system for Nesalia Inc - managing 25+ repositories with AI agents.

---

## Table of Contents

1. [Project Initialization](./project-initialization.md)
2. [Feature Development Workflow](./feature-development.md)
3. [Issue Management](./issue-management.md)
   - [Issue Templates](./issue-templates/)
4. [Pull Request Process](./pull-request-process.md)
5. [Code Review Guidelines](./code-review.md)
6. [Release Process](./release-process.md)
7. [Documentation Standards](./documentation.md)
8. [AI Agent Integration](./ai-agents.md)
9. [Repository Templates](./templates/)

---

## Core Principles

### Human-Driven, AI-Executed

```
HUMAN                           AI AGENT
  │                               │
  ├──► "Add user auth" ─────────►│
  │                               ├──► Read project context
  │                               ├──► Plan implementation
  │                               ├──► Generate code
  │                               ├──► Generate tests
  │                               ├──► Create PR
  │                               │
  ◄───────────────────────────────┤
  │                               │
  │   Review, validate, merge    │
  │                               │
```

**The rule**: Humans decide WHAT, AI does HOW.

### Consistency Across Repositories

All 25 repositories share:

- Identical structure (`.github/`, `.ai/`, `src/`)
- Same coding rules (`.ai/rules.md`)
- Same workflows (`.github/workflows/`)
- Same templates

**Benefit**: Any agent can work on any project with the same context.

### Trust Through Verification

If CI passes → the code is valid

```
┌─────────────────────────────────────┐
│           CI PIPELINE               │
│                                     │
│  1. Lint (ESLint)    ──► FAIL ───► │
│  2. Type Check (TS)  ──► FAIL ───► │
│  3. Tests            ──► FAIL ───► │
│  4. Build            ──► FAIL ───► │
│                                     │
│  ALL PASS ──► PR CAN BE MERGED     │
└─────────────────────────────────────┘
```

---

## Quick Start

### Starting a New Project

```bash
# Clone the template
git clone org/project-template my-new-project

# Update remote
cd my-new-project
git remote set-url origin org/my-new-repo

# Install dependencies
npm install

# Ready to develop
npm run dev
```

### Creating an Issue

Use the issue template (auto-populated on GitHub):

```markdown
## Description
What needs to be done?

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Context
Links, screenshots, additional info
```

### Submitting a PR

After completing work:

```bash
git add .
git commit -m "feat: add user authentication"
git push origin feature-branch
```

Then fill out the PR template.

---

## Version

- **Version**: 1.0.0
- **Last Updated**: 2026-03-06
- **Owner**: Architecture Team
