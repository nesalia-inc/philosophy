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

Templates are stored in `docs/workflows/software-engineering/issue-templates/`

| Template | Use For | File |
|----------|---------|------|
| Feature | New features or enhancements | `0000-feature.md` |
| Bug | Bug reports | `0000-bug.md` |
| Task | General work items | `0000-task.md` |
| Refactoring | Code cleanup or restructure | `0000-refactor.md` |
| Documentation | Docs updates | `0000-documentation.md` |
| Security | Security vulnerabilities | `0000-security.md` |

### Feature Request Template

For new features or enhancements:

```markdown
---
name: Feature Request
about: Suggest a new feature
title: '[FEATURE] '
labels: 'enhancement'
assignees: ''
---

## Description
[One clear sentence describing what you want]

## Problem
[Explain the pain point this solves]

## Solution
[Describe your proposed solution]

## Acceptance Criteria
- [ ] Criterion 1 (must be testable)
- [ ] Criterion 2
- [ ] Criterion 3

## Technical Notes
- Implementation approach: [link to similar features or docs]
- Dependencies: [any external services or packages needed]
- API changes: [if this changes an existing API]

## Design
- Mockups: [links to Figma or images]
- Related features: [links to related issues]

## Priority
- [ ] Critical - Blocking release
- [ ] High - Important feature
- [ ] Medium - Nice to have
- [ ] Low - Backlog

## Estimated Effort
- [ ] Small (< 1 day)
- [ ] Medium (1-3 days)
- [ ] Large (> 3 days)
```

### Bug Report Template

For reporting bugs or issues:

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
1. Go to '[page or feature]'
2. Click on '[action]'
3. Enter '[input]'
4. See error

## Expected Behavior
[What should happen]

## Actual Behavior
[What actually happens]

## Environment
- OS: [e.g., macOS 14.0]
- Browser: [e.g., Chrome 120]
- Version: [e.g., 1.2.3]
- Repository: [link to repo]

## Error Log
```
[Paste relevant error messages or stack traces]
```

## Root Cause Hypothesis
[If you know the cause, describe it here]

## Priority
- [ ] Critical - Production is down
- [ ] High - Blocking users
- [ ] Medium - Workaround exists
- [ ] Low - Minor issue
```

### Task Template

For general work items:

```markdown
---
name: Task
about: General work item
title: '[TASK] '
labels: 'task'
assignees: ''
---

## Description
[What needs to be done - be specific]

## Requirements
- [ ] Requirement 1
- [ ] Requirement 2
- [ ] Requirement 3

## Context
- Why this task: [background]
- Related: [links to related issues or code]

## Implementation Notes
- Files to modify: [paths if known]
- Approach: [any specific approach to follow]
```

### Refactoring Template

For code cleanup or restructuring:

```markdown
---
name: Refactoring
about: Code cleanup or restructure
title: '[REFACTOR] '
labels: 'refactoring'
assignees: ''
---

## Description
[What needs to be refactored and why]

## Scope
- Files affected: [list files or directories]
- Functions/components: [specific items to refactor]

## Current State
[Describe the current problematic code]

## Target State
[Describe what the code should look like after]

## Motivation
- [ ] Improve readability
- [ ] Improve performance
- [ ] Remove technical debt
- [ ] Follow new standards

## Breaking Changes
- [ ] No breaking changes
- [ ] May break existing functionality
- [ ] Breaking changes - migration needed

## Risk Level
- [ ] Low
- [ ] Medium
- [ ] High
```

### Documentation Template

For documentation updates:

```markdown
---
name: Documentation
about: Documentation updates
title: '[DOCS] '
labels: 'documentation'
assignees: ''
---

## Description
[What documentation needs to be updated]

## Type
- [ ] New documentation
- [ ] Update existing documentation
- [ ] Fix incorrect documentation

## Location
- File: [path to file or docs section]
```

### Security Template

For security vulnerabilities:

```markdown
---
name: Security Issue
about: Security vulnerability or concern
title: '[SECURITY] '
labels: 'security'
assignees: ''
---

## Description
[One sentence describing the security issue]

## Severity
- [ ] Critical
- [ ] High
- [ ] Medium
- [ ] Low

## Type
- [ ] Vulnerability (CVE)
- [ ] Misconfiguration
- [ ] Access Control Issue
- [ ] Data Exposure
- [ ] Dependency Vulnerability

## Vulnerability Details
### Affected Component
[What is affected]

### Attack Vector
[How can an attacker exploit this?]

### Impact
[What can happen if exploited?]
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

### Template Files

Templates are maintained in `docs/workflows/software-engineering/issue-templates/`:

```
docs/workflows/software-engineering/issue-templates/
├── 0000-feature.md       # Feature request
├── 0000-bug.md          # Bug report
├── 0000-task.md         # General task
├── 0000-refactor.md     # Refactoring
├── 0000-documentation.md # Documentation
└── 0000-security.md     # Security issue
```

### Adding to a Repository

Copy these templates to `.github/ISSUE_TEMPLATE/` in each repository:

```
.github/
└── ISSUE_TEMPLATE/
    ├── 0000-feature.md
    ├── 0000-bug.md
    ├── 0000-task.md
    ├── 0000-refactor.md
    ├── 0000-documentation.md
    └── 0000-security.md
```

GitHub will automatically offer these templates when creating new issues.

---

## Quick Reference

| When | Use Template | Label |
|------|--------------|-------|
| New feature | Feature | `enhancement` |
| Something broken | Bug | `bug` |
| General work | Task | `task` |
| Code cleanup | Refactoring | `refactoring` |
| Docs update | Documentation | `documentation` |
| Security issue | Security | `security` |
| Assign to agent | Any | Add agent context |
| Need clarification | Any | `needs-info` label |
| Working on it | Any | `in-progress` label |
| Blocked | Any | `blocked` label + reason |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
