# Techno Expertise

How to train agents to become experts on technologies.

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
- Alternatives

---

## The Solution

Agents need two things:

1. **Reference files**: Pre-curated knowledge about technologies
2. **Research capability**: Ability to find up-to-date information

---

## Part 1: Techno Reference Files

### Location

```
.technos/
├── stripe.md
├── nextjs.md
├── drizzle.md
├── postgresql.md
└── ...
```

### Structure

```markdown
# Stripe

## Overview
Payment processing platform.

## Recommended Library
- `stripe` (official Node.js SDK)
- Version: Check npm for latest

## Best Practices

### Payments
- Always use webhooks for async events
- Implement idempotency keys for retries
- Never expose secret API keys in frontend

### Security
- Verify webhook signatures
- Use test mode in development
- Rotate API keys regularly

### Error Handling
- Handle specific Stripe errors
- Implement retry logic with exponential backoff

## Common Mistakes

1. Not verifying webhook signatures (security risk)
2. Hardcoding API keys in source code
3. Not handling duplicate webhook events
4. Using deprecated API methods

## Recommended Patterns

### Webhook Handler
```typescript
// Recommended pattern
const handleWebhook = async (req) => {
  const sig = req.headers['stripe-signature']
  const event = stripe.webhooks.constructEvent(
    req.body,
    sig,
    webhookSecret
  )
  // Handle event
}
```

### Payment Intent
```typescript
// Create payment intent
const paymentIntent = await stripe.paymentIntents.create({
  amount: 2000,
  currency: 'usd',
  automatic_payment_methods: { enabled: true },
})
```

## Links

- Docs: https://stripe.com/docs
- API Reference: https://stripe.com/docs/api
- Best Practices: https://stripe.com/docs/best Practices
- Status: https://stripe.com/docs/guides/upgrade-your-api

## Common Errors Solution

| Error | Solution |
|-------|----------|
| Invalid API key | Check environment variable |
| Webhook signature invalid | Verify secret matches |
| Rate limited | Implement exponential backoff |

---

## Part 2: Agent Research Capability

### The Research Workflow

```
Agent needs to implement feature
    │
    ▼
Check .technos/ file exists?
    │
    ├─ YES → Read it
    │
    └─ NO → Research online
           │
           ▼
           Find best practices
           │
           ▼
           Verify sources
           │
           ▼
           Implement
```

### Research Rules

When researching online:

1. **Start with official documentation**
   - Official docs are always the source of truth
   - Look for "Best Practices" or "Guides"

2. **Verify dates**
   - Tutorial from 2019 ≠ current best practice
   - Check library version in docs vs tutorial

3. **Cross-reference**
   - Check multiple sources
   - Look at GitHub issues for problems

4. **Validate with .technos/**
   - Does this match our reference?
   - Does it contradict?

### Source Reliability

| Source | Reliability | Notes |
|--------|-------------|-------|
| Official docs | ✅ High | Always start here |
| Official blog | ✅ High | Best practices |
| GitHub README | ✅ High | Setup guides |
| Stack Overflow | ⚠️ Variable | Check dates |
| Random blog | ❌ Low | Likely outdated |
| YouTube | ❌ Low | Often outdated |

---

## Part 3: Technology Categories

### Payment Processing

```
.technos/
├── stripe.md
├── paypal.md
└── square.md
```

**Agent must know**:
- Webhook handling
- Idempotency
- Error handling
- Security best practices

### Database

```
.technos/
├── postgresql.md
├── drizzle.md
└── redis.md
```

**Agent must know**:
- Connection pooling
- Query optimization
- Migrations
- Indexes

### Frontend Frameworks

```
.technos/
├── nextjs.md
├── react.md
└── tailwind.md
```

**Agent must know**:
- SSR vs CSR
- Routing patterns
- State management
- Performance

### Backend

```
.technos/
├── fastapi.md
├── nodejs.md
└── python.md
```

**Agent must know**:
- Async patterns
- API design
- Error handling
- Security

---

## Part 4: Research Validation

### Before Implementing

Agent must validate:

1. **Is this current?**
   - Check library version
   - Check API version

2. **Is this official?**
   - From official docs?
   - From trusted source?

3. **Does this match our .technos/?**
   - Consistent with our reference?
   - Any contradictions?

4. **Is this applicable?**
   - Fits our use case?
   - Fits our stack?

### Validation Checklist

```markdown
## Source Validation

- [ ] Source is official docs or trusted
- [ ] Information is from 2025 or later
- [ ] Library version matches our version
- [ ] Pattern matches .technos/ reference
- [ ] No security issues introduced
```

---

## Part 5: Keeping References Updated

### When to Update .technos/

- New major version released
- New best practice discovered
- Common mistake found
- Security vulnerability discovered

### Update Process

1. Agent discovers new best practice during research
2. Agent proposes update to .technos/
3. Human reviews and approves
4. Update committed

### Version Tracking

```markdown
# Stripe

## Last Updated
2026-03-06

## Version
stripe-node: ^14.0.0

## Changelog
- 2026-03: Added new webhook handling pattern
- 2025-12: Updated retry logic recommendations
```

---

## Agent Prompt for Techno Research

```markdown
You need to implement a feature using [TECHNOLOGY].

## Steps

1. Check if .technos/[technology].md exists
   - Read it for best practices

2. If no reference exists or needs update:
   - Search for official documentation
   - Look for "best practices" or "guides"
   - Verify information is current

3. Validate sources:
   - Is it from official docs?
   - Is it recent (2025+)?
   - Does it match our stack?

4. Implement using validated approach

5. If you find something new/important:
   - Propose update to .technos/[technology].md
```

---

## Summary

| Component | Purpose |
|-----------|---------|
| `.technos/` files | Pre-curated best practices |
| Research workflow | How to find current info |
| Source validation | Verify credibility |
| Update process | Keep references current |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
