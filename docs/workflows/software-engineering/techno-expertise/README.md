# Techno Expertise

How to train agents to become experts on technologies.

---

## Three-Pillar System

The techno expertise system is built on **three types of documentation**:

```
techno-expertise/
├── techno-courses/      # Technology-specific (Stripe, React, PostgreSQL)
├── need-based-guides/   # Problem-centric (Caching, Database, State)
└── theoretical-courses/ # Theory (Graph Theory, Automata, Category Theory)
```

Each serves a different purpose in developer education.

---

### 1. Techno Courses

**Focus**: One specific technology
**Question**: "Everything about [TECHNOLOGY]"
**Example**: Stripe, React, PostgreSQL

See [Techno Courses](./techno-courses/) for details.

---

### 2. Need-Based Guides

**Focus**: Solving specific problems
**Question**: "How to solve [PROBLEM]"
**Example**: How to cache, How to handle state, How to design APIs

See [Need-Based Guides](./need-based-guides/) for details.

---

### 3. Theoretical Courses

**Focus**: Fundamental concepts
**Question**: "Why does it work this way?"
**Example**: Graph Theory, Automata, Category Theory, Probability

See [Theoretical Courses](./theoretical-courses/) for details.

---

## Comparison

| Aspect | Techno Course | Need-Based Guide | Theoretical Course |
|--------|---------------|------------------|-------------------|
| Scope | One technology | Multiple technologies | Concepts |
| Organization | By concept | By problem | By theory |
| Audience | Learn a tech | Solve a problem | Understand why |
| Examples | Code | Cross-tech | Math, diagrams |
| Updates | Frequent | Medium | Rare |

---

## When to Use Each

| Situation | Use |
|-----------|-----|
| Need to implement with [Technology] | Techno Course |
| Need to solve [Problem] | Need-Based Guide |
| Need to understand [Concept] | Theoretical Course |

---

## The Problem

```
You: "Add Stripe payments"
Agent: "Ok I'll add payments"
Agent searches: "stripe nodejs tutorial"
Agent finds: Old blog post from 2019
Agent implements: Using deprecated API
```

**The agent doesn't know**:
- What's the best way to do it
- Recommended patterns
- Pitfalls to avoid
- Current best practices

---

## The Solution: Techno Courses

Instead of short reference files, we build **comprehensive courses** for each technology:

```
.technos/
├── index.md              # Registry - list of all courses
├── stripe/               # Full course on Stripe
│   ├── README.md         # Overview, quick start
│   ├── fundamentals/      # Core concepts
│   ├── patterns/         # Recommended patterns
│   ├── pitfalls/        # Common mistakes
│   └── examples/        # Code examples
├── nextjs/
│   └── ...
└── postgresql/
    └── ...
```

---

## Why Courses

| Aspect | Reference File | Course |
|--------|---------------|--------|
| Depth | Basics | Full |
| Shareable | Yes | Yes (repo) |
| Updatable | Static | Evolving |
| Learning | No | Yes |
| Projects | No | Yes (exercises) |

---

## The Registry

All courses are listed in the index:

```markdown
# Techno Courses Registry

| Techno | Status | Version | Last Updated |
|--------|--------|---------|--------------|
| [Stripe](./stripe/) | Complete | v14 | 2026-03 |
| [Next.js](./nextjs/) | Complete | v14 | 2026-03 |
| [Drizzle](./drizzle/) | In Progress | - | - |
| [PostgreSQL](./postgresql/) | Planned | - | - |
```

---

## Course Structure

Each course contains:

```
techno/
├── README.md           # Overview, quick start
├── fundamentals/       # Core concepts
│   ├──概念1.md
│   └── concept2.md
├── patterns/          # Recommended patterns
│   ├── pattern1.md
│   └── pattern2.md
├── pitfalls/          # Common mistakes
│   ├── mistake1.md
│   └── mistake2.md
├── examples/          # Code examples
│   └── example1.ts
└── changelog.md       # Track updates
```

---

## For Agents

### Using a Course

When implementing feature with Technology:

1. **Check if course exists** in `.technos/`
2. **Read fundamentals** to understand concepts
3. **Follow patterns** for implementation
4. **Avoid pitfalls** documented
5. **Use examples** as templates

### Research Workflow

```
Need to implement feature with Technology
    │
    ▼
Course exists in .technos/?
    │
    ├─ YES → Read course → Implement
    │
    └─ NO → Research online
            │
            ▼
            Find official docs
            │
            ▼
            Create course (propose)
```

---

## Creating a New Course

### Steps

1. Create directory: `.technos/[technology]/`
2. Create `README.md` with overview
3. Add fundamentals (core concepts)
4. Add patterns (recommended approaches)
5. Add pitfalls (common mistakes)
6. Add examples (copy-paste ready)
7. Update registry in `index.md`

### Quality Standards

- [ ] Official documentation referenced
- [ ] Code examples tested
- [ ] Patterns validated
- [ ] Pitfalls verified
- [ ] Examples copy-paste ready

---

## Course Format

### README.md Template

```markdown
# [Technology Name]

Brief description of what this technology is.

## Quick Start

How to get started in 5 minutes.

## Version

Current version: X.Y.Z

## Official Documentation

- Docs: https://...
- API Reference: https://...
- Best Practices: https://...

## What's Inside

- [Fundamentals](./fundamentals/)
- [Patterns](./patterns/)
- [Pitfalls](./pitfalls/)
- [Examples](./examples/)
```

---

## Creating Content

### Course Creation Process

Each type of content has its own creation process:

| Type | Process | Template |
|------|---------|----------|
| Techno Course | [Course Creation](./course-creation/) | [Template](./course-creation/templates/course-template.md) |
| Need-Based Guide | TBD | TBD |
| Theoretical Course | TBD | TBD |

---

## Available Courses

See [Registry](./registry/index.md) for complete list.

---

*Document version: 1.1*
*Last updated: 2026-03-06*
