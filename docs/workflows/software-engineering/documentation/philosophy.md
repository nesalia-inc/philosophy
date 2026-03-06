# Documentation Philosophy

The mindset and principles behind great documentation.

---

## Why Documentation Matters

| Without Docs | With Docs |
|--------------|------------|
| "How does this work?" | Read the docs |
| Tribal knowledge | Shared knowledge |
| Onboarding takes weeks | Onboarding takes days |
| Same questions repeated | Self-service |

---

## Two Audiences, Two Needs

### Users

**Primary Question**: "How do I use this?"

**Needs**:
- Quick answers
- Copy-paste examples
- Troubleshooting help
- Can ask questions when confused

### AI Agents

**Primary Question**: "How do I implement this?"

**Needs**:
- Clear rules (forbidden/allowed)
- Complete context
- No ambiguity
- Can't ask clarifying questions

---

## The Documentation Contract

### For Users

> "I promise to answer your question before you ask it."

### For Agents

> "I promise complete context so you cannot fail."

---

## Quality Indicators

### Good Documentation

- Answers the question in the title
- Has working code examples
- Explains the "why", not just the "how"
- Acknowledges limitations
- Is maintained

### Bad Documentation

- Title doesn't match content
- Examples don't work
- No context provided
- Claims perfection
- Is outdated

---

## The Maintenance Mindset

### The Trap

```
"We'll write perfect docs later"
    │
    ▼
Never happens
    │
    ▼
Outdated docs
    │
    ▼
Users stop reading
```

### The Solution

```
"Write good enough docs now"
    │
    ▼
Kept up to date
    │
    ▼
Users trust and read
    │
    ▼
Improvements suggested
    │
    ▼
Better docs
```

**Rule**: Good enough + maintained > perfect + outdated

---

## Writing Principles

### 1. Start with the User's Question

```
❌ BAD:
"Configuration Options"

✅ GOOD:
"How do I configure authentication?"

❌ BAD:
"Database Schema"

✅ GOOD:
"How do I define a user table?"
```

### 2. Show, Don't Tell

```
❌ BAD:
"You should use environment variables."

✅ GOOD:
```bash
# Set the database URL
export DATABASE_URL="postgresql://..."

# Your code
const db = connect(process.env.DATABASE_URL)
```
```

### 3. Be Specific

```
❌ BAD:
"Use strong authentication"

✅ GOOD:
"Use JWT tokens with 1-hour expiry and RS256 signing"
```

### 4. Admit What You Don't Know

```
❌ BAD:
"Works perfectly"

✅ GOOD:
"Known issue: Slow on Firefox. See workaround in troubleshooting."
```

---

## The Agent Problem

### Without Clear Rules

```
Agent reads: "Use best practices"
    │
    ▼
"What are best practices?"
    │
    ▼
Makes assumptions ← DANGER
```

### With Clear Rules

```
Agent reads: ".ai/rules.md - No any types, Result for errors"
    │
    ▼
Clear constraints
    │
    ▼
Follows rules ← SAFE
```

---

## Documentation for Agents

Agents need different documentation than humans:

| For Humans | For Agents |
|------------|------------|
| Tutorials | Rules |
| Concepts | Patterns |
| Guides | Workflows |
| Examples | Constraints |
| Can ask questions | Need complete context |

---

## The Truth About Documentation

1. **No one reads it until they need it**
   - Write for the moment of need

2. **People copy-paste, they don't read**
   - Every example must work

3. **Context matters**
   - Don't assume knowledge

4. **Less is more**
   - Don't document the obvious

5. **Updates are critical**
   - Outdated docs are worse than no docs

---

## Summary

| Principle | Meaning |
|-----------|---------|
| Progressive Disclosure | Reveal info when needed |
| Action-Oriented | Answer before asked |
| Copy-Paste Ready | Examples must work |
| Maintainable > Perfect | Good enough + updated |
| Honest | Admit limitations |
| For Agents | Complete context, no ambiguity |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
