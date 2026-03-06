# Issue Management

How to create, structure, and manage issues for effective development.

---

## Why Issue Structure Matters

Well-structured issues = efficient development:

| Bad Issue | Good Issue |
|-----------|------------|
| "Fix auth bug" | "Login fails with special characters in password" |
| "Add user feature" | "Allow users to upload profile pictures (max 5MB, jpg/png)" |
| "Improve performance" | "Reduce API response time from 2s to 200ms for /users endpoint" |

**Rule**: If you can't measure it, you can't build it.

---

## Issue Templates

All repositories use standard templates from `.github/ISSUE_TEMPLATE/`

### Feature Request Template

```markdown
---
name: Feature Request
about: Suggest a new feature
title: '[FEATURE] '
labels: 'enhancement'
assignees: ''
---

## Description
[One clear sentence about what you want]

## Problem
[Explain the pain point this solves]

## Solution
[Describe your proposed solution]

## Acceptance Criteria
- [ ] Criterion 1 (must be testable)
- [ ] Criterion 2
- [ ] Criterion 3

## Context
- Mockups/designs: [links]
- Related issues: [links]
- Technical constraints: [any limitations]

## Priority
- [ ] Critical - Blocking release
- [ ] High - Important feature
- [ ] Medium - Nice to have
- [ ] Low - Backlog
```

### Bug Report Template

```markdown
---
name: Bug Report
about: Report something that's broken
title: '[BUG] '
labels: 'bug'
assignees: ''
---

## Description
[One sentence describing the bug]

## Steps to Reproduce
1. Go to '...'
2. Click on '...'
3. See error

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Environment
- OS: [e.g., macOS 14.0]
- Browser: [e.g., Chrome 120]
- Version: [e.g., 1.2.3]

## Screenshots
[If applicable, add screenshots]

## Additional Context
[Any other context about the problem]
```

### Task Template

```markdown
---
name: Task
about: General work item
title: '[TASK] '
labels: 'task'
assignees: ''
---

## Description
[What needs to be done]

## Requirements
- [ ] Requirement 1
- [ ] Requirement 2

## Notes
[Any additional information]
```

---

## Issue Labels

### Category Labels

| Label | Use For |
|-------|---------|
| `bug` | Something is broken |
| `enhancement` | New feature or improvement |
| `task` | General work item |
| `documentation` | Docs-related work |
| `refactoring` | Code cleanup |
| `security` | Security-related |

### Priority Labels

| Label | Meaning |
|-------|---------|
| `critical` | Blocked production, must fix now |
| `high` | Important, release blocker |
| `medium` | Should be fixed |
| `low` | Nice to have |

### Status Labels

| Label | Meaning |
|-------|---------|
| `blocked` | Waiting on something else |
| `in-progress` | Being worked on |
| `review` | Ready for review |
| `needs-info` | More information needed |

---

## Issue Lifecycle

```
┌─────────────┐
│   OPEN      │  Issue created
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  IN-PROGRESS│  Assigned, actively working
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   REVIEW    │  Code complete, awaiting review
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   MERGED    │  PR merged, issue resolved
└─────────────┘
```

---

## Writing Good Issues

### The Formula

```
[ACTION] + [TARGET] + [DETAILS]

Examples:
- Add user profile picture upload
- Fix login redirect loop
- Improve API response time
- Update documentation
- Refactor database queries
```

### Acceptance Criteria

Must be **testable**:

```markdown
✅ GOOD:
- [ ] User receives email within 5 seconds
- [ ] API returns 401 for invalid tokens
- [ ] Upload accepts files up to 5MB

❌ BAD:
- [ ] Fast response time
- [ ] Proper error handling
- [ ] Good user experience
```

### Context Matters

Include relevant links:

- Related issues
- Design mockups
- API documentation
- External references

---

## Assignment

### For AI Agents

When assigning an issue to an agent, include:

```markdown
## Agent Context

### Project
- Repository: [repo-name]
- Tech stack: [stack]

### This Feature
- Connects to: [related features]
- Uses: [existing services/modules]

### Resources
- Code to reference: [file paths]
- Patterns to follow: [similar implementations]
```

---

## Issue Templates Setup

Create `.github/ISSUE_TEMPLATE/` in each repository:

```
.github/
└── ISSUE_TEMPLATE/
    ├── 0000-feature.md
    ├── 0000-bug.md
    └── 0000-task.md
```

GitHub will automatically offer these templates when creating new issues.

---

## Quick Reference

| When | Do This |
|------|---------|
| New feature | Use Feature template |
| Something broken | Use Bug template |
| General work | Use Task template |
| Assign to agent | Add agent context |
| Need clarification | Add `needs-info` label |
| Working on it | Add `in-progress` label |
| Blocked | Add `blocked` label + reason |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
