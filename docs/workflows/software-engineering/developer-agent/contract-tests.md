# Contract Tests

Interface validation tests written before implementation.

**Note**: All code is functional - no classes, no services. Use pure functions with `Result` type for error handling.

---

## Purpose

Contract tests define **what the code must do** before it's written. They validate the interface contract, not the implementation details.

---

## Workflow

```
1. Agent receives Issue + Plan
            ↓
2. Agent writes contract tests (interface validation)
            ↓
3. Review (Human + Agent)
            ↓
4. Contract tests approved → IMMUTABLE
            ↓
5. Development agent implements to pass tests
            ↓
6. CI validates
```

---

## Contract Tests vs Implementation Tests

| Aspect | Contract Tests | Implementation Tests |
|--------|---------------|---------------------|
| When | Before implementation | During/after implementation |
| What | Interface contract | Implementation details |
| Who writes | Agent (first pass) | Agent (during dev) |
| Mutable | Until approved | Always |
| Run by | CI (immutable) | CI (always) |

---

## Contract Test Structure

### What to Include

```typescript
// tests/contract/create-user.test.ts

// Pure function: (email, name) → Result<User, ValidationError | DuplicateError>
describe('createUser', () => {
  it('should accept email and name, return Ok(User)', () => {
    // Validates: (email, name) → Result<User, E>
  })

  it('should return Err(ValidationError) for invalid email', () => {
    // Validates error case
  })

  it('should return Err(DuplicateError) if email exists', () => {
    // Validates error case
  })
})

// Return type validation
describe('User', () => {
  it('should have id, email, name, createdAt', () => {
    // Validates return object structure
  })
})
```

### What NOT to Include

```typescript
// WRONG - Implementation details in contract tests
describe('createUser', () => {
  it('should save to database', () => { ... })     // HOW, not WHAT
  it('should hash password', () => { ... })
  it('should use transactions', () => { ... })
  it('should call userRepository.save()', () => { ... })
})
```

---

## Review Process

### Who Reviews

| Reviewer | Focus |
|----------|-------|
| Human | Business logic, edge cases, errors |
| Agent (different) | Technical correctness, coverage |

### Review Checklist

- [ ] Interface matches plan exactly
- [ ] Error cases covered
- [ ] Return types correct
- [ ] No implementation details
- [ ] Test names clear
- [ ] All paths covered

### Approval

When approved:
- Tests become **IMMUTABLE**
- Cannot be modified by development agent
- Only changed via new review cycle

---

## Development Agent Constraints

### Agent CAN

- Write implementation code
- Write implementation tests
- Run contract tests
- Modify implementation, NOT contract

### Agent CANNOT

- Modify contract tests
- Delete contract tests
- Weaken contract assertions

---

## CI Integration

### Pipeline

```yaml
# .github/workflows/contract-tests.yml
name: Contract Tests

on:
  pull_request:

jobs:
  contract-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Run contract tests
        run: npm run test:contract

      - name: Verify no contract modifications
        run: |
          git diff --name-only | grep -q "contract" && \
          echo "Contract tests modified - NOT ALLOWED" && exit 1 || \
          echo "Contract tests unchanged - OK"
```

---

## Example: Complete Flow

### 1. Plan Defines Interface

```markdown
## createUser(email: string, name: string): Result<User, ValidationError | DuplicateError>

Returns:
- Ok({ id: string, email: string, name: string, createdAt: Date })
- Err(ValidationError) if email invalid
- Err(DuplicateError) if email exists
```

### 2. Agent Writes Contract Tests

```typescript
import { Result, Ok, Err } from '@pereira/result'

// Pure function: no classes, no services
describe('createUser', () => {
  it('should return Ok(User) with correct shape', () => {
    const result = createUser('test@test.com', 'John')

    expect(result).toBeInstanceOf(Ok)
    const user = (result as Ok<User>).value
    expect(user.id).toBeDefined()
    expect(user.email).toBe('test@test.com')
    expect(user.name).toBe('John')
    expect(user.createdAt).toBeInstanceOf(Date)
  })

  it('should return Err(ValidationError) for invalid email', () => {
    const result = createUser('invalid', 'John')

    expect(result).toBeInstanceOf(Err)
    const error = (result as Err<ValidationError>).error
    expect(error.code).toBe('INVALID_EMAIL')
  })

  it('should return Err(DuplicateError) for existing email', () => {
    const result = createUser('existing@test.com', 'John')

    expect(result).toBeInstanceOf(Err)
    const error = (result as Err<DuplicateError>).error
    expect(error.code).toBe('DUPLICATE_EMAIL')
  })
})
```

### 3. Review (Human + Agent)

Human checks:
- Are business requirements met?
- Are error cases correct?
- Is Result type appropriate?

Agent (reviewer) checks:
- Are tests technically correct?
- Is coverage complete?
- No implementation details?

### 4. Approved → Immutable

```
Contract tests: APPROVED
Status: IMMUTABLE
Can only be changed via new review cycle
```

### 5. Development Agent Implements

```typescript
// Pure function - no class, no service
function createUser(email: string, name: string): Result<User, ValidationError | DuplicateError> {
  // Implementation
  // Must pass contract tests
}
```

### 6. CI Validates

```
✓ Contract tests pass
✓ Implementation tests pass
✓ Coverage: 100%
✓ Ready to merge
```

---

## When Contract Tests Need Changes

### Scenario: Implementation Impossible

```
Agent: "Contract test requires X but API only supports Y"
         ↓
Human Review: "You're right, adjust contract"
         ↓
Contract Modified → New Review Cycle
```

### Scenario: Missing Edge Case

```
Agent: "Contract doesn't cover null email"
         ↓
Human Review: "Good catch, add to contract"
         ↓
Contract Modified → New Review Cycle
```

---

*Document version: 1.0*
*Last updated: 2026-03-06*
