# Code Review Guidelines

Comprehensive guide to conducting effective and efficient pull request reviews.

---

## Overview

Code review is one of the most important practices for maintaining code quality, sharing knowledge, and preventing bugs. This guide covers how to both **give** and **receive** reviews effectively.

---

## Review Philosophy

### Goals of Code Review

1. **Quality Assurance** - Catch bugs, logic errors, and issues
2. **Knowledge Sharing** - Learn from each other's approaches
3. **Consistency** - Ensure code follows team standards
4. **Maintainability** - Keep codebase understandable and modifiable
5. **Mentorship** - Help team members grow and improve

### Core Principles

```
✅ Constructive  - Helpful, not critical
✅ Respectful    - Assume good intentions
✅ Thorough      - Review carefully, not superficially
✅ Efficient    - Review promptly, don't block
✅ Collaborative - Discuss, don't dictate
```

---

## For Reviewers

### Before Reviewing

#### 1. Check CI Status

```bash
# Don't review if CI is failing
❌ Red X on CI checks
✅ Green checkmarks on all checks

# If CI is failing:
- Comment "Waiting for CI to pass"
- Come back when it's green
```

#### 2. Understand Context

```bash
# Read the PR description first
- What problem is being solved?
- Why was this approach chosen?
- What are the trade-offs?

# Look at linked issues
- Read the original issue
- Check design documents
- Review related discussions
```

#### 3. Check File Scope

```bash
# How much to review?
✅ < 200 lines     - Thorough review
✅ 200-500 lines   - Focused review
⚠️  500-1000 lines - Detailed review, may need multiple sessions
❌ > 1000 lines    - Request split into smaller PRs
```

### Review Process

#### Step 1: High-Level Review

```
┌────────────────────────────────────────────────────────────────┐
│ 1. ARCHITECTURE                                                 │
│    ├─ Does the approach make sense?                             │
│    ├─ Is it overly complex?                                     │
│    ├─ Are there better alternatives?                            │
│    └─ Does it follow existing patterns?                         │
│                                                                 │
│ 2. CORRECTNESS                                                  │
│    ├─ Does it solve the stated problem?                         │
│    ├─ Are there edge cases missed?                              │
│    ├─ Are there obvious bugs?                                   │
│    └─ Will it handle errors properly?                           │
│                                                                 │
│ 3. PHILOSOPHY ALIGNMENT                                         │
│    ├─ Result<T, E> pattern used correctly?                      │
│    ├─ Option<T> pattern used correctly?                         │
│    ├─ No any types?                                             │
│    ├─ Functions under 50 lines?                                 │
│    └─ Tests are real, not mocks?                                │
└────────────────────────────────────────────────────────────────┘
```

#### Step 2: Detailed Code Review

```bash
# Review file by file
1. Read the diff, not the whole file
2. Understand each change
3. Check for issues
4. Leave comments

# Focus on:
- Logic errors
- Edge cases
- Error handling
- Type safety
- Performance issues
- Security concerns
- Code clarity
```

#### Step 3: Quality Checks

```typescript
// Check for Result<T, E> pattern
❌ throw new Error('Failed')
✅ return Result.err({ type: 'failed' })

// Check for Option<T> pattern
❌ user.name ?? 'Unknown'
✅ user.name.match({
  some: (name) => name,
  none: () => 'Unknown'
})

// Check for any types
❌ function process(data: any): any
✅ function process(data: Data): Result<Processed, Error>

// Check function length
❌ 80-line function
✅ Split into 3 smaller functions

// Check for tests
❌ Tests with mocks
✅ Tests with real implementations
```

### Commenting Guidelines

#### Types of Comments

```bash
# 1. Blocking Issues (must fix)
❌ "This is wrong"
✅ "This logic has an issue: if array is empty,
    we'll get undefined. Consider returning Option<T>."

# 2. Suggestions (should fix)
💡 "Have you considered using Map instead of
   object for O(1) lookups?"

# 3. Questions (clarification)
❓ "What's the reason for this approach?"

# 4. Approval (good as is)
✅ "Looks good! Nice use of Result<T, E> pattern."

# 5. Nitpicks (optional style)
🎨 "Minor: could use const instead of let here
   (non-blocking)"
```

#### Comment Structure

```markdown
## Issue: [Brief Title]

**Severity:** [Must fix / Should fix / Nice to have]

**Problem:**
[Explain what the issue is]

**Why it matters:**
[Explain the impact]

**Suggestion:**
[Propose a solution]

**Example:**
```typescript
// Show code example if helpful
```
```

#### Effective Feedback

```bash
❌ BAD: "This is terrible."
✅ GOOD: "I'm concerned this approach might lead
         to issues because [reason]. Have you
         considered [alternative]?"

❌ BAD: "Fix this."
✅ GOOD: "Could we refactor this into smaller
         functions? It would be easier to test
         and understand."

❌ BAD: "Use Result<T, E>."
✅ GOOD: "This function throws on error, but
         our philosophy requires Result<T, E>.
         Could you update it to return
         Result<Data, Error> instead?"

❌ BAD: (Long comment about minor formatting)
✅ GOOD: "Nit: formatting (non-blocking)"
```

### Review Categories

#### 1. Correctness

```typescript
// Check for:
- Off-by-one errors
- Null/undefined handling
- Race conditions
- Edge cases
- Error propagation

❌ function process(items: Item[]) {
    for (let i = 0; i <= items.length; i++) {  // Off-by-one!
      // ...
    }
  }

✅ function process(items: Item[]): Result<void, ProcessError> {
    for (const item of items) {
      // ...
    }
    return Result.ok(undefined)
  }
```

#### 2. Type Safety

```typescript
// Check for:
- any types
- type assertions
- loose types
- missing null checks

❌ function validate(input: any): any {
    if (input.valid) return input.data
  }

✅ type ValidationError =
    | { type: 'invalid_input'; field: string }

  function validate(input: unknown)
    : Result<Data, ValidationError> {
    // ...
  }
```

#### 3. Error Handling

```typescript
// Check for:
- Silent failures
- Uncaught errors
- Generic error types
- Missing error paths

❌ try {
    process()
  } catch (e) {
    console.error(e)  // Silent failure!
  }

✅ const result = process()
  if (!result.ok) {
    return Result.err({
      type: 'process_failed',
      cause: result.error
    })
  }
```

#### 4. Performance

```typescript
// Check for:
- N+1 queries
- Unnecessary loops
- Large computations
- Memory leaks

❌ for (const user of users) {
    user.posts = await db.query(
      'SELECT * FROM posts WHERE user_id = ?',
      [user.id]
    )  // N+1 problem!
  }

✅ const posts = await db.query(
    'SELECT * FROM posts WHERE user_id IN (?)',
    [users.map(u => u.id)]
  )
```

#### 5. Security

```typescript
// Check for:
- SQL injection
- XSS vulnerabilities
- Missing input validation
- Exposed secrets

❌ const query = `SELECT * FROM users WHERE name = '${name}'`

✅ const result = await db.query(
    'SELECT * FROM users WHERE name = ?',
    [name]
  )
```

#### 6. Testing

```typescript
// Check for:
- Missing tests
- Mock overuse
- Edge case coverage
- Test clarity

❌ it('processes data', () => {
    const mock = jest.fn()  // Mock!
    process(mock)
    expect(mock).toHaveBeenCalled()
  })

✅ it('processes data and returns result', () => {
    const result = process(realData)
    expect(result.ok).toBe(true)
    expect(result.value).toEqual(expectedData)
  })
```

#### 7. Documentation

```typescript
// Check for:
- Complex logic without comments
- Missing JSDoc on public APIs
- Unclear function names
- Magic numbers

❌ function calculate(a, b) {
    return a * 1.5 + b * 2.3  // Magic numbers!
  }

✅ /**
   * Calculates total price with tax and shipping
   * @param basePrice - Base price in cents
   * @param quantity - Number of items
   * @returns Total price in cents
   */
  function calculateTotal(
    basePrice: number,
    quantity: number
  ): number {
    const TAX_RATE = 0.15  // 15% tax
    const SHIPPING_PER_ITEM = 2.30  // $2.30 per item
    return basePrice * (1 + TAX_RATE)
         + quantity * SHIPPING_PER_ITEM
  }
```

### Approval Criteria

A PR is ready to approve when:

```bash
✅ All CI checks pass
✅ No blocking issues remain
✅ Philosophy alignment verified:
   ✓ Result<T, E> pattern used
   ✓ Option<T> pattern used
   ✓ No any types
   ✓ Functions < 50 lines
   ✓ Real tests, not mocks
✅ Tests are comprehensive
✅ Documentation is updated
✅ Security review done (if applicable)
✅ Performance review done (if applicable)
```

### Review Response Time

```bash
🎯 Target: Within 24 hours

⚡ Quick wins (< 15 min): Review immediately
📦 Standard PRs: Within 24 hours
🔨 Large PRs: Within 48 hours (may need multiple sessions)
🚨 Urgent: Within 4 hours

# If you can't review in time:
1. Re-assign to someone else
2. Comment when you'll review
3. Don't let PRs sit for days
```

---

## For Authors

### Before Requesting Review

#### 1. Self-Review Checklist

```bash
# Read your own PR first
✅ Does the description make sense?
✅ Are all files that should be changed included?
✅ Are there accidental changes (config files, etc)?
✅ Did you run the full test suite?
✅ Did you test manually (if applicable)?

# Check philosophy compliance
✅ Result<T, E> for all error cases?
✅ Option<T> for all optional values?
✅ No any types?
✅ Functions under 50 lines?
✅ Real tests, not mocks?
```

#### 2. Local Verification

```bash
# Run all checks
npm run ci
# ✓ lint passed
# ✓ typecheck passed
# ✓ tests passed
# ✓ build succeeded

# Check test coverage
npm run test:coverage
# Ensure > 80% coverage
```

#### 3. PR Description Quality

```markdown
## What does this PR do?
✅ Clear, concise explanation
❌ "Updates code" (too vague)

## Why is it needed?
✅ Links to issue, explains context
❌ No context provided

## How does it work?
✅ High-level approach
❌ No explanation

## Test Plan
✅ How to verify, test results
❌ "Tested locally" (not specific)
```

### Responding to Feedback

#### Handling Different Comment Types

```bash
# Blocking Issues (Must Fix)
✅ Make the requested changes
✅ Comment "Done" or explain why not
✅ Re-request review

# Suggestions (Should Fix)
💬 Discuss if you disagree
💬 Explain your reasoning
💬 Find compromise if needed

# Questions
❓ Answer clearly
❓ Provide context
❓ Link to relevant docs/code

# Nitpicks
🎨 Fix if you want
🎨 OK to ignore if not important
```

#### Disagreeing with Feedback

```markdown
## Respectful Disagreement Template

"I see your point about [issue]. However, I chose
this approach because [reason].

The alternative [suggestion] has [drawback].

What do you think about [compromise]?"

# Examples:

"I see your point about splitting this function.
However, keeping it together ensures atomicity.
The alternative would require transactions which
add complexity.

What do you think about keeping it together but
adding inline comments to clarify?"
```

#### Updating After Review

```bash
# 1. Make changes
git add -A
git commit -m "fix: address review feedback"

# 2. Push updates
git push

# 3. Re-request review
gh pr edit 123 --add-reviewer @reviewer

# 4. Comment in PR
"Updated to address feedback. Ready for re-review."
```

### Iterating on Reviews

```bash
# Round 1: Initial review
- Leave feedback
- Author makes changes

# Round 2: Re-review
- Check if feedback addressed
- If not, ask again clearly
- If yes, approve or move to next point

# Don't let reviews go infinite:
- Group related feedback
- Be specific about what needs changing
- Approve if non-blocking issues remain
```

---

## Special Review Scenarios

### Large PRs (> 500 lines)

```bash
# For reviewers:
1. Request split into smaller PRs
2. Or review in multiple sessions
3. Focus on critical parts first
4. Leave summary comment

# For authors:
1. Split before requesting review
2. Or create RFC/Design doc first
3. Then implement in smaller chunks
```

### Breaking Changes

```bash
# Extra review needed:
✅ Migration guide complete?
✅ All consumers updated?
✅ Deprecation timeline set?
✅ Backwards compatibility considered?
✅ Rollback plan documented?

# Reviewers:
- Tech lead approval required
- Check migration path
- Verify communication plan
```

### Security Fixes

```bash
# Extra review needed:
✅ Security team review
✅ Attack scenario documented
✅ Fix prevents exploitation
✅ No new vulnerabilities introduced
✅ Post-mortem planned

# Reviewers:
- Security expert required
- Verify root cause addressed
- Check for similar issues
```

### Performance Optimizations

```bash
# Extra review needed:
✅ Before/after benchmarks
✅ No regressions
✅ Monitoring in place
✅ Rollback plan ready

# Reviewers:
- Verify benchmark methodology
- Check for trade-offs
- Ensure monitoring adequate
```

### Refactoring

```bash
# Key focus:
✅ No behavior changes
✅ Tests still pass
✅ Code quality improved
✅ Migration path (if needed)

# Reviewers:
- Run tests to verify
- Check no logic changes
- Verify improvement is real
```

---

## Anti-Patterns

### ❌ For Reviewers

```bash
# Don't do this:
❌ "LGTM" without actually reviewing
❌ Nitpicking formatting over logic
❌ Being rude or dismissive
❌ Blocking on personal preference
❌ Taking days to review
❌ Reviewing while distracted
❌ Only looking at code, not context
❌ Approving failing CI
```

### ❌ For Authors

```bash
# Don't do this:
❌ Requesting review without testing
❌ Ignoring all feedback
❌ Arguing every point
❌ Marking as "ready" when WIP
❌ Huge PRs without discussion
❌ Not responding to comments
❌ Requesting review from wrong people
```

---

## Review Metrics

### What We Track

```bash
# Team metrics:
- Time to first review
- Time to approve
- Rejection rate
- Review participation
- PR size vs review time

# Individual metrics:
- Reviews given/received
- Review response time
- Approval rate
- Comments per PR
```

### Goals

```bash
🎯 Time to first review: < 24 hours
🎯 Time to approve: < 48 hours
🎯 Rejection rate: < 30% (shows good quality)
🎯 Participation: Everyone reviews regularly
```

---

## Related Documentation

- [Pull Request Templates](./templates/README.md) - PR templates
- [Branching Strategy](./branching-strategy.md) - Branch workflows
- [Merging Strategy](./merging-strategy.md) - How to merge
- [Quality Gates](./quality-gates.md) - Quality requirements

---

*Document version: 1.0*
*Last updated: 2025-03-08*
