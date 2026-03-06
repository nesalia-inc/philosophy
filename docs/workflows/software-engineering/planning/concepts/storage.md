# Storage

Where to store development plans.

---

## Recommended: GitHub Issue Comment

Store the plan as a comment in the issue:

```
Issue #42
    │
    └── Comment: "## Plan"
                  └ The complete plan
```

**Why**:
- Always accessible
- Searchable
- Version history
- Linked to the work

---

## Alternative: GitHub Project

Use a project board:

```
| To Plan | Planned | In Progress | Done |
|---------|---------|-------------|------|
| Issue A | Issue B | Issue C     |      |
```

---

## Why GitHub

| Option | Pros | Cons |
|--------|------|------|
| **GitHub Issue** | Searchable, versioned, linked | Only in issue |
| **GitHub Project** | Board view | Less detail |
| **Local file** | Versioned | Lost, not searchable |
| **Notion/Other** | Rich editor | Extra tool, not linked |

---

## Agent Reading Order

When starting work, agents must:

1. Read the Issue description
2. Read the Plan (in comments)
3. Read `.ai/rules.md`
4. Read `.ai/context.md`

---

## Agent Rules

- NEVER start coding before reading the plan
- If plan is unclear, ask for clarification
- Follow the plan exactly
- If plan needs changes, propose in comment

---

*Document version: 1.0*
*Last updated: 2026-03-06*
