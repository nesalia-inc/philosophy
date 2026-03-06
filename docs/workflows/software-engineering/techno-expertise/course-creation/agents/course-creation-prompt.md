# Agent Prompt: Create Techno Course

Use this prompt to delegate course creation to an AI agent.

---

## The Prompt

```markdown
# Create a Techno Course for [TECHNOLOGY NAME]

You need to create a comprehensive course about [TECHNOLOGY NAME].

## Technology Overview

[Provide 1-2 sentences about what this technology is]

Official Documentation: [LINK TO OFFICIAL DOCS]

## Your Task

Create a complete techno course in the folder `.technos/[technology]/`.

## Required Structure

Create this exact structure:

```
techno/
├── README.md           # Overview + Quick Start
├── fundamentals/      # Core concepts
│   └── README.md
├── patterns/          # Recommended patterns
│   └── README.md
├── pitfalls/          # Common mistakes
│   └── README.md
├── examples/          # Copy-paste code
│   └── README.md
└── changelog.md     # Update history
```

## Content Requirements

### 1. README.md (Overview + Quick Start)

Must include:
- Technology description (1-2 sentences)
- What it solves (2-3 sentences)
- Quick Start (5 min, install + basic usage)
- Version (current version)
- When to use / When NOT to use
- Links to official documentation
- Table of contents

### 2. Fundamentals Section

Cover these core concepts:
- [List fundamental concepts for this technology]

For each concept, explain:
- What it is
- Why it matters
- How it works
- Key properties

### 3. Patterns Section

Document recommended approaches:
- [List patterns for this technology]

For each pattern, explain:
- Problem it solves
- Solution
- When to use
- Code example

### 4. Pitfalls Section

List common mistakes:
- [List common mistakes]

For each pitfall, explain:
- What goes wrong
- Why it happens
- How to avoid
- Show ❌ Bad vs ✅ Good code

### 5. Examples Section

Provide copy-paste ready examples:
- [List key examples]

Each example should:
- Be complete and working
- Include imports
- Be tested
- Have explanation

### 6. Changelog.md

Create with:
- Current date
- Version
- Placeholders for future updates

## Quality Standards

1. **Accuracy**: All information must match official documentation
2. **Completeness**: Cover all fundamental concepts
3. **Practicality**: Examples must be copy-paste ready and working
4. **Currency**: Use current version and APIs

## Research Guidelines

1. Start with official documentation
2. Check for "Best Practices" guides
3. Look at GitHub issues for common problems
4. Verify all code examples work
5. Don't include deprecated APIs

## Output

Create all files in `.technos/[technology]/`

After completion, provide a summary of what was created.
```

---

## How to Use

### Step 1: Fill the Prompt

Replace placeholders:

```
[TECHNOLOGY NAME] → "Stripe"
[Provide 1-2 sentences about what this technology is] → "Payment processing platform"
[LINK TO OFFICIAL DOCS] → "https://stripe.com/docs"
[List fundamental concepts for this technology] → "Payment Intent, Webhooks, Customers, API Keys"
[List patterns for this technology] → "Payment flow, Webhook handling, Error handling"
[List common mistakes] → "Exposing API keys, Not verifying webhooks"
[List key examples] → "Initialize, Create payment, Handle webhook"
```

### Step 2: Give to Agent

Send the filled prompt to the agent.

### Step 3: Review

When agent completes:
1. Check all sections present
2. Verify code examples work
3. Validate accuracy against official docs

---

## Example: Filled Prompt

```markdown
# Create a Techno Course for Stripe

You need to create a comprehensive course about Stripe.

## Technology Overview

Stripe is a payment processing platform that handles credit card payments, subscriptions, and invoices.

Official Documentation: https://stripe.com/docs

## Your Task

Create a complete techno course in the folder `.technos/stripe/`.

## Required Structure

Create this exact structure:
[... rest of prompt ...]
```

---

## Agent Rules

When creating a course, agents must:

1. **Start with official docs** - Never use blog posts as primary source
2. **Verify information** - Cross-check with official documentation
3. **Test all code** - Examples must work
4. **Use current version** - Don't document deprecated APIs
5. **Be practical** - Focus on real-world usage

---

## Post-Creation Review

After agent completes, verify:

- [ ] All sections present
- [ ] README has Quick Start
- [ ] Fundamentals cover core concepts
- [ ] Patterns are accurate
- [ ] Pitfalls are relevant
- [ ] Examples work
- [ ] Links are valid
- [ ] No deprecated APIs

---

*Document version: 1.0*
*Last updated: 2026-03-06*
