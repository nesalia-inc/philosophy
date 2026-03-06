# Why Planning Matters

---

## The Problem: Lost Plans

Without a stored plan:

```
Discussion with Agent
    │
    │ Plan discussed
    │
    ▼
Discussion ends
    │
    │ Plan lost
    │
    ▼
Next time = start over
```

Every feature requires 2-4x rework because the agent doesn't know exactly what's expected.

---

## The Problem: Forgetting Details

Even with the best intentions, during development you forget:

- "Wait, forgot database indexes"
- "Wait, forgot security checks"
- "Wait, forgot to update tests"
- "Wait, forgot documentation"

---

## The Solution

Store the plan in the issue itself:

```
GitHub Issue #42: Add user auth
    │
    ├── Description: what needs to be done
    ├── Plan (comment): how to do it
    ├── Code (commits): implementation
    └── PR: verification
```

---

## Without Plan

```
Issue: "Add user auth"
   │
   ▼
Agent: "I'll add auth"
   │
   ▼
Agent makes assumptions about:
   - What "auth" means
   - How to implement it
   - What files to create
   │
   ▼
Agent produces code
   │
   ▼
You review: "That's not what I wanted"
   │
   ▼
Rework x 2-4 times
```

---

## With Plan

```
Issue: "Add user auth"
   │
   ▼
Plan approved by you (5-15 min)
   │
   ▼
Agent knows exactly what to do
   │
   ▼
Agent produces code
   │
   ▼
You review: "Perfect"
   │
   ▼
Done
```

---

## Why Plans Matter

1. **Clarity**: Agent knows exactly what to do
2. **Accountability**: You approved the plan
3. **Traceability**: Plan is stored forever
4. **Completeness**: Checklist prevents forgotten items

---

*Document version: 1.0*
*Last updated: 2026-03-06*
