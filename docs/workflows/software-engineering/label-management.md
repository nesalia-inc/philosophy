# Label Management

How to organize issues with labels, priority, and effort.

---

## The Problem: Priority vs Urgency

Most teams confuse these two concepts:

| | Important | Not Important |
|---|-----------|---------------|
| **Urgent** | DO FIRST | DELEGATE |
| **Not Urgent** | SCHEDULE | ELIMINATE |

Using only one "priority" field mixes both dimensions and causes confusion.

---

## Our Solution: Two Dimensions

### 1. Priority (Importance)

**Question**: "What impact does this have?"

```
priority:critical   → Production down, data loss
priority:high      → Major user impact
priority:medium    → Normal impact, nice to have
priority:low       → Minor impact
```

### 2. Effort (Size)

**Question**: "How long will this take?"

```
effort:xs    → < 1 hour
effort:s     → 1 day
effort:m     → 1-3 days
effort:l     → 1 week
effort:xl    → > 1 week
```

---

## Label Categories

### Type (What is it?)

| Label | Use For |
|-------|---------|
| `bug` | Something is broken |
| `feature` | New functionality |
| `enhancement` | Improvement to existing |
| `task` | General work |
| `refactor` | Code cleanup |
| `docs` | Documentation |
| `security` | Security issue |
| `tech-debt` | Technical debt |

### Status (Where is it?)

| Label | Use For |
|-------|---------|
| `triage` | Needs evaluation |
| `ready` | Ready to work on |
| `in-progress` | Being worked on |
| `blocked` | Waiting on something |
| `review` | Awaiting review |
| `done` | Completed |

### Priority (How important?)

| Label | Meaning | When to Use |
|-------|---------|-------------|
| `priority:critical` | Production down | System completely broken, data loss, security breach |
| `priority:high` | Major impact | Major feature broken, many users affected |
| `priority:medium` | Normal priority | Standard features, reasonable workarounds exist |
| `priority:low` | Minor impact | Nice to have, cosmetic issues |

### Effort (How big?)

| Label | Time | When to Use |
|-------|------|-------------|
| `effort:xs` | < 1 hour | Quick fixes, small changes |
| `effort:s` | 1 day | Small task, can be done in one session |
| `effort:m` | 1-3 days | Medium task, a few days of work |
| `effort:l` | 1 week | Large task, a week of work |
| `effort:xl` | > 1 week | Very large, needs breakdown |

---

## Priority Matrix

### For Triaging

```
                    LOW EFFORT          HIGH EFFORT
HIGH PRIORITY       DO FIRST            SCHEDULE
                    (Quick wins)       (Important projects)

LOW PRIORITY        DELEGATE            ELIMINATE
                    (Quick tasks)       (Backlog)
```

### Priority Guidelines

| Priority | Criteria | Response Time |
|----------|----------|---------------|
| `priority:critical` | Production down | Immediate (< 1 hour) |
| `priority:high` | Major user impact | Today |
| `priority:medium` | Normal | This week |
| `priority:low` | Minor | This sprint |

---

## Status Workflow

```
┌─────────┐     ┌─────────┐     ┌─────────────┐     ┌─────────┐
│ TRIAGE  │ ──► │  READY  │ ──► │IN-PROGRESS │ ──► │  REVIEW │
└─────────┘     └─────────┘     └─────────────┘     └────┬────┘
                                                          │
                                                          ▼
                                                   ┌──────────┐
                                                   │   DONE   │
                                                   └──────────┘
```

### Status Definitions

| Status | Description |
|--------|-------------|
| `triage` | New issue, needs evaluation |
| `ready` | Evaluated, prioritized, ready to work |
| `in-progress` | Being actively worked on |
| `blocked` | Waiting on something (add comment why) |
| `review` | Code complete, awaiting review |
| `done` | Merged, issue closed |

---

## Using Labels for Agents

Agents need clear labels to know what to work on:

### Agent Task Selection

```
Agent looking for work:
    │
    ▼
Filter: status:ready
    │
    ▼
Sort: priority (critical → high → medium → low)
    │
    ▼
Pick: highest priority
```

### Label Checklist for Agents

When creating an issue, always include:

- [ ] Type label (`bug`, `feature`, `task`, etc.)
- [ ] Priority label (`priority:*`)
- [ ] Effort label (`effort:*`)

---

## Label Naming Convention

Use prefixes to group related labels:

```
Type:
  bug
  feature
  enhancement

Status:
  triage
  ready
  in-progress
  blocked
  review
  done

Priority:
  priority:critical
  priority:high
  priority:medium
  priority:low

Effort:
  effort:xs
  effort:s
  effort:m
  effort:l
  effort:xl
```

---

## Color Scheme

### Recommended Colors

| Category | Color | Example |
|----------|-------|---------|
| Priority: Critical | Red | `#ff0000` |
| Priority: High | Orange | `#ff8800` |
| Priority: Medium | Yellow | `#ffcc00` |
| Priority: Low | Gray | `#888888` |
| Status: Blocked | Purple | `#8800ff` |
| Status: In Progress | Blue | `#0088ff` |
| Status: Done | Green | `#00cc00` |
| Type: Bug | Red | `#ff0000` |
| Type: Feature | Green | `#00cc00` |

---

## GitHub Labels Setup

Create these labels in each repository:

```
# Type
bug
feature
enhancement
task
refactor
docs
security
tech-debt

# Status
triage
ready
in-progress
blocked
review
done

# Priority
priority:critical
priority:high
priority:medium
priority:low

# Effort
effort:xs
effort:s
effort:m
effort:l
effort:xl
```

---

## Issue Template Integration

Issue templates should require labels:

```markdown
---
name: Bug Report
labels: 'bug'
---

## Priority
- [ ] priority:critical
- [ ] priority:high
- [ ] priority:medium
- [ ] priority:low

## Effort
- [ ] effort:xs
- [ ] effort:s
- [ ] effort:m
- [ ] effort:l
- [ ] effort:xl
```

---

## Quick Reference

| When | Labels |
|------|--------|
| New bug | `bug`, `triage` |
| Security issue | `security`, `priority:critical` |
| New feature | `feature`, `priority:*`, `effort:*` |
| Working on it | `in-progress` |
| Blocked | `blocked` + comment |
| Ready for review | `review` |
| Completed | `done` |

---

## Examples

### Critical Bug

```
labels: bug, priority:critical, effort:xs
title: "[BUG] Login returns 500 for all users"
```

### Medium Feature

```
labels: feature, priority:medium, effort:m
title: "[FEATURE] Add user profile pictures"
```

### Low Enhancement

```
labels: enhancement, priority:low, effort:s
title: "[ENHANCEMENT] Improve error messages"
```

### Large Refactor

```
labels: refactor, priority:medium, effort:xl
title: "[REFACTOR] Migrate to new auth system"
```

---

*Document version: 1.0*
*Last updated: 2026-03-06*
