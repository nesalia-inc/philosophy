# Documentation Maintenance

Keeping documentation accurate and useful over time.

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
"Good enough docs now"
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

## When to Update

### Trigger Events

| Event | Action |
|-------|--------|
| New feature added | Add to README, create guide |
| API changed | Update API reference |
| Bug found | Add to troubleshooting |
| Process changed | Update .ai/prompts/ |
| Rules changed | Update .ai/rules.md |
| Bug in docs fixed | Update immediately |

### The "If It's Not Updated, It's Wrong" Rule

```
Code: function renamed from `getUser` to `fetchUser`
    │
    ▼
Documentation: still says `getUser`
    │
    ▼
User tries `getUser` → FAILS
    │
    ▼
Trust in documentation = DESTROYED
```

---

## Maintenance Workflow

### For Features

```
PR merged
    │
    ▼
Check docs affected?
    │
    ├─ YES → Update docs in same PR
    │
    └─ NO → Create follow-up issue
```

### For Bugs

```
Bug discovered in docs
    │
    ▼
Fix immediately
    │
    ▼
PR with fix
    │
    ▼
Merged
```

---

## Quality Checks

### Before Publishing

- [ ] All code examples tested
- [ ] Links work
- [ ] Matches current code
- [ ] No broken images
- [ ] Reviewed by human

### Regular Audit

Quarterly check:

- [ ] All links valid
- [ ] Code examples work
- [ ] No deprecated features
- [ ] Screenshots current
- [ ] Matches actual behavior

---

## Who Maintains

### For User Docs

| Doc Type | Owner |
|----------|-------|
| README | Anyone |
| Quick Start | Feature developer |
| Tutorial | Any team member |
| Troubleshooting | Anyone who fixes bugs |

### For Agent Docs

| Doc Type | Owner |
|----------|-------|
| rules.md | Architecture lead |
| prompts/ | Process owner |
| context.md | Project lead |

---

## Measuring Quality

### Metrics

| Metric | Target |
|--------|--------|
| Docs age | < 3 months since last update |
| Broken links | 0 |
| Untested code examples | 0 |
| User questions answered by docs | > 80% |

### Feedback Loop

```
User asks question
    │
    ▼
Is it answered in docs?
    │
    ├─ YES → Mark as "covered"
    │
    └─ NO → Add to docs
```

---

## Common Mistakes

### 1. Documentation Debt

```
Ignore docs for 6 months
    │
    ▼
Massive update needed
    │
    ▼
Never happens
    │
    ▼
Docs become useless
```

**Fix**: Update docs with every change

### 2. Perfect But Wrong

```
Write beautiful docs
    │
    ▼
Code changes
    │
    ▼
Docs not updated
    │
    ▼
Users misled
```

**Fix**: Test docs with code

### 3. Writing for Yourself

```
Write docs as you understand it
    │
    ▼
New person can't follow
    │
    ▼
Docs useless
```

**Fix**: Have new person review

---

## Tools and Automation

### Linting

```yaml
# docs-lint.yml
- name: Check links
  uses: lycheeverse/lychee-action

- name: Check code examples
  run: npm run docs:test
```

### Testing

```typescript
// Test that code examples work
import { execute } from './docs-test'

it('README example works', async () => {
  const result = await execute('const db = database({...})')
  expect(result).not.toThrow()
})
```

---

## Summary

| Principle | Action |
|----------|--------|
| Update with code | Same PR or follow-up |
| Good enough > perfect | Don't wait for ideal |
| Test examples | Code must work |
| Regular audit | Quarterly review |
| Feedback loop | Add missing answers |

---

*Document version: 1.0*
*Last updated: 2026-03-06*
