# Techno Courses

Technology-specific documentation focusing on one tool/framework/library.

---

## Purpose

Learn everything about a **specific technology**:

- What it is
- How it works
- Best practices
- Common mistakes

---

## Example

```markdown
Stripe Course
├── README.md              # Overview, Quick Start
├── fundamentals/
│   ├── payment-intent.md  # Core concept
│   ├── webhooks.md        # Core concept
│   └── customers.md       # Core concept
├── patterns/
│   ├── payment-flow.md    # Recommended approach
│   └── error-handling.md # Recommended approach
├── pitfalls/
│   ├── exposing-keys.md   # Common mistake
│   └── missing-webhook-verification.md
└── examples/
    ├── initialize.ts      # Working code
    ├── create-payment.ts  # Working code
    └── handle-webhook.ts  # Working code
```

---

## When to Use

| Use Case | Example |
|----------|---------|
| Implementing payments | → Stripe Course |
| Building web apps | → Next.js Course |
| Database operations | → PostgreSQL Course |

---

## Structure

Each techno course contains:

| Section | Purpose |
|---------|---------|
| README.md | Overview + Quick Start (5 min) |
| fundamentals/ | Core concepts explained |
| patterns/ | Recommended implementations |
| pitfalls/ | Mistakes to avoid |
| examples/ | Copy-paste ready code |
| changelog.md | Update history |

---

## Creating a Techno Course

1. Follow [Course Creation Process](../course-creation/)
2. Use [Course Template](../course-creation/templates/course-template.md)
3. Give prompt to agent using [Agent Prompt](../course-creation/agents/course-creation-prompt.md)
4. Review using [KPIs](../course-creation/kpis.md)

---

## Registry

All techno courses are listed in [Registry](./registry/index.md)

---

*Document version: 1.0*
*Last updated: 2026-03-06*
