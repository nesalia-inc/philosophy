# Developer Agent

The execution layer - an autonomous agent that implements features from plans.

---

## Role

The Developer Agent is the **executor** in our development workflow:

```
Issue → Plan → [Developer Agent] → PR → Review → Merge
                    ▲
                    │
            Just executes
            (simple role)
```

**Analogy**: Like a "preparator" in a warehouse - receives order + instructions, delivers completed work.

---

## Responsibilities

1. **Read the plan** - Understand what to build
2. **Use knowledge base** - Reference techno courses
3. **Implement code** - Write the actual solution
4. **Create commits** - Track progress with clear messages
5. **Open PR** - Ready for review when done

---

## Input

Each task provides:

| Input | Source | Purpose |
|-------|--------|---------|
| Issue | Issues Management | What to build |
| Plan | Planning | How to build it |
| Contract Tests | Agent writes → Review approved | Interface validation |
| Knowledge | Techno Expertise | How to do it right |

---

## Output

Expected deliverables:

- Working code implementation
- Clear commit history
- Pull Request with description
- Tests (if required by plan)

---

## System Prompt

The agent operates on simple, clear instructions:

See [System Prompt](./system-prompt.md) for detailed instructions.

---

## Contract Tests

Before implementing, agent writes contract tests that define the interface:

See [Contract Tests](./contract-tests.md) for the complete process.

---

## Performance Metrics

Track agent effectiveness:

See [Metrics](./metrics.md) for what to measure.

---

## Workflow

```
1. Receive task (Issue + Plan)
       │
       ▼
2. Read relevant knowledge (Techno Courses)
       │
       ▼
3. Write contract tests (interface validation)
       │
       ▼
4. Review (Human + Agent)
       │
       ▼
5. Contract tests approved → IMMUTABLE
       │
       ▼
6. Create feature branch
       │
       ▼
7. Implement solution
       │
       ├── Write code to pass contract tests
       ├── Add implementation tests
       └── Update docs (if needed)
       │
       ▼
8. Commit with clear messages
       │
       ▼
9. Create Pull Request
       │
       ▼
10. Wait for review
       │
       ▼
11. Address feedback (if any)
```

**Note**: Once contract tests are approved, they become IMMUTABLE. Agent cannot modify them.

---

## Principles

### Keep It Simple

The Developer Agent doesn't need complex workflows. It needs:
- Clear instructions
- Knowledge access
- Simple process

### Focus on Execution

| What matters | What doesn't |
|--------------|--------------|
| Working code | Complex approval flows |
| Clear commits | Excessive documentation |
| PR ready for review | Pre-merge gates |

### Metrics Over Process

Invest in **measuring performance**, not in adding process steps.

---

## Relation to Other Layers

```
┌─────────────────────────────────────┐
│         Issues Management          │
│   (What needs to be built)         │
└─────────────────┬───────────────────┘
                  ▼
┌─────────────────────────────────────┐
│            Planning                │
│        (How to build it)            │
└─────────────────┬───────────────────┘
                  ▼
┌─────────────────────────────────────┐
│        Techno Expertise            │
│    (Knowledge for quality)          │
└─────────────────┬───────────────────┘
                  ▼
┌─────────────────────────────────────┐
│         Developer Agent            │
│        (Execution layer)            │
└─────────────────┬───────────────────┘
                  ▼
┌─────────────────────────────────────┐
│          PR / Review                │
│      (Quality gate - human)         │
└─────────────────────────────────────┘
```

---

*Document version: 1.0*
*Last updated: 2026-03-06*
