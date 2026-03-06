# Course Creation Process

How to create a comprehensive techno course.

---

## Overview

Creating a techno course is a structured process:

```
Phase 1: Research
    │
    ▼
Phase 2: Structure
    │
    ▼
Phase 3: Writing
    │
    ▼
Phase 4: Review
    │
    ▼
Done
```

Each phase has specific deliverables.

---

## Phase 1: Research

**Goal**: Gather all necessary information about the technology.

### Steps

1. **Collect Official Documentation**
   - Main documentation
   - API Reference
   - Best Practices guides
   - Official tutorials

2. **Identify Fundamental Concepts**
   - What is this technology?
   - How does it work?
   - What are the building blocks?

3. **Catalog Recommended Patterns**
   - How to do things correctly?
   - What patterns are recommended?
   - What is the "right way"?

4. **List Common Pitfalls**
   - What are common mistakes?
   - What causes bugs?
   - What should be avoided?

5. **Prepare Examples**
   - Common use cases
   - Working code samples

### Output

```
research/
├── docs-links.md       # All official documentation links
├── concepts.md        # List of fundamental concepts
├── patterns.md        # Recommended patterns
├── pitfalls.md        # Common mistakes to avoid
└── examples.md        # Example use cases
```

---

## Phase 2: Structure

**Goal**: Organize the information into a clear structure.

### Steps

1. Define sections
2. Organize subsections
3. Define format for each part
4. Create the directory structure

### Standard Structure

```
techno/
├── README.md           # Overview + Quick Start
├── fundamentals/       # Core concepts
│   └── README.md
├── patterns/          # Recommended patterns
│   └── README.md
├── pitfalls/          # Common mistakes
│   └── README.md
├── examples/          # Copy-paste code
│   └── README.md
└── changelog.md      # Update history
```

---

## Phase 3: Writing

**Goal**: Create the actual content.

### Steps

1. Write Fundamentals
2. Write Patterns
3. Write Pitfalls
4. Write Examples

### Writing Guidelines

| Section | Focus | Tone |
|---------|-------|------|
| Fundamentals | Understanding | Educational |
| Patterns | Implementation | Practical |
| Pitfalls | Avoidance | Warning |
| Examples | Usage | Action-oriented |

---

## Phase 4: Review

**Goal**: Ensure quality and accuracy.

### Steps

1. **Verify Accuracy**
   - Check all information
   - Validate against official docs
   - Ensure no outdated info

2. **Test Examples**
   - Run all code examples
   - Verify they work
   - Fix any bugs

3. **Validate Completeness**
   - Check all sections present
   - Ensure nothing missing

### Review Checklist

- [ ] All fundamentals covered
- [ ] Patterns are accurate
- [ ] Pitfalls are relevant
- [ ] Examples work
- [ ] Links are valid
- [ ] No outdated information

---

## Process Summary

| Phase | Time | Deliverables |
|-------|------|--------------|
| Research | 2-4 hours | Links, concepts, patterns, pitfalls |
| Structure | 30 min | Directory structure |
| Writing | 4-8 hours | All course content |
| Review | 1-2 hours | Verified course |

---

## Next Steps

See:
- [Course Template](./templates/course-template.md) - What to include
- [Agent Prompt](./agents/course-creation-prompt.md) - How to delegate to agents
- [KPIs](./kpis.md) - How to measure success

---

*Document version: 1.0*
*Last updated: 2026-03-06*
