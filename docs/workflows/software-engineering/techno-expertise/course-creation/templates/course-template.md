# Course Template

What each techno course must contain.

---

## Required Sections

Every techno course must have:

| Section | Required | Description |
|---------|-----------|--------------|
| README.md | ✅ | Overview + Quick Start |
| fundamentals/ | ✅ | Core concepts |
| patterns/ | ✅ | Recommended approaches |
| pitfalls/ | ✅ | Common mistakes |
| examples/ | ✅ | Copy-paste code |
| changelog.md | ✅ | Update history |

---

## README.md Template

```markdown
# [Technology Name]

Brief description of what this technology is (1-2 sentences).

## Overview

[3-5 sentences explaining what it is and why to use it]

## Quick Start

[5-minute getting started guide]

### Installation

```bash
npm install [package-name]
```

### Basic Usage

[Minimal code example to get started]

## Version

Current: X.Y.Z

## When to Use

- Use case 1
- Use case 2
- Use case 3

## When NOT to Use

- Don't use for use case 1
- Don't use for use case 2

## Official Documentation

- Docs: https://...
- API Reference: https://...
- Best Practices: https://...

## What's Inside

- [Fundamentals](./fundamentals/) - Core concepts
- [Patterns](./patterns/) - Recommended approaches
- [Pitfalls](./pitfalls/) - Common mistakes
- [Examples](./examples/) - Copy-paste code
```

---

## Fundamentals Section

### Structure

```
fundamentals/
├── README.md           # Index of concepts
├── concept-1.md       # Topic 1
├── concept-2.md       # Topic 2
└── ...
```

### Content Template

```markdown
# [Concept Name]

## Definition

What this concept is (1-2 sentences).

## Why It Matters

Why understanding this is important (2-3 sentences).

## How It Works

Technical explanation of how it works.

### Key Properties

| Property | Type | Description |
|----------|------|-------------|
| prop1 | string | Description |

### Example

```typescript
// Code example
```
```

---

## Patterns Section

### Structure

```
patterns/
├── README.md           # Index of patterns
├── pattern-1.md       # Pattern 1
└── ...
```

### Content Template

```markdown
# [Pattern Name]

## Problem

What problem this pattern solves.

## Solution

How to solve it correctly.

## When to Use

- Use case 1
- Use case 2

## Implementation

```typescript
// Correct implementation
```

### Key Points

1. Point 1
2. Point 2
3. Point 3
```

---

## Pitfalls Section

### Structure

```
pitfalls/
├── README.md           # Index of pitfalls
├── mistake-1.md      # Mistake 1
└── ...
```

### Content Template

```markdown
# [Mistake Name]

## Problem

What goes wrong.

## Why It Happens

Why developers make this mistake.

## How to Avoid

How to prevent or fix this mistake.

### ❌ Bad

```typescript
// Wrong code
```

### ✅ Good

```typescript
// Correct code
```
```

---

## Examples Section

### Structure

```
examples/
├── README.md           # Index of examples
├── example-1.ts       # Example 1
└── ...
```

### Content Template

```markdown
# [Example Name]

## Use Case

When to use this example.

## Complete Example

```typescript
import { thing } from 'package'

// Full working code
```

## Explanation

Line-by-line explanation.
```

---

## Changelog Template

```markdown
# Changelog

All notable changes to this course.

## [Date]

### Added
- New concept: [name]
- New pattern: [name]

### Updated
- Updated [section]: [change]
- Fixed [section]: [fix]

### Removed
- Removed outdated [section]

---

## [Previous Date]

[Previous changes]
```

---

## Quality Checklist

Before completing a course:

- [ ] README explains what technology does in 30 seconds
- [ ] Quick Start works in 5 minutes
- [ ] All fundamental concepts covered
- [ ] All recommended patterns documented
- [ ] All common pitfalls listed
- [ ] All examples are copy-paste ready
- [ ] All code examples tested
- [ ] Official documentation links valid
- [ ] Changelog started
- [ ] Version noted

---

*Document version: 1.0*
*Last updated: 2026-03-06*
