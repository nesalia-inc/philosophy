# Quality Gates

Automated quality checks that must pass before pull requests can be merged.

---

## Overview

Quality gates are automated checks that enforce code quality, security, and consistency. They run on every pull request and block merging if any check fails.

---

## Philosophy of Quality Gates

```
┌─────────────────────────────────────────────────────────────────────┐
│                      QUALITY GATES PHILOSOPHY                       │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  1. SHIFT LEFT                                                       │
│     Catch issues early, before they reach production                │
│                                                                      │
│  2. FAIL FAST                                                        │
│     Immediate feedback on issues                                    │
│                                                                      │
│  3. EXPLICIT REQUIREMENTS                                            │
│     Clear rules, no ambiguity                                       │
│                                                                      │
│  4. AUTOMATED ENFORCEMENT                                            │
│     No manual checks, no exceptions                                 │
│                                                                      │
│  5. CONTINUOUS IMPROVEMENT                                           │
│     Gates evolve with project needs                                │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Standard Quality Gates

### 1. Linting

```yaml
check: lint
purpose: Code style and consistency
critical: Yes
```

#### What It Checks

```bash
# Code style
✅ Consistent indentation
✅ Proper spacing
✅ No unused variables
✅ No console.log in production
✅ Naming conventions
✅ Import ordering
```

#### Configuration

```javascript
// eslint.config.js
export default [
  {
    files: ['**/*.ts', '**/*.tsx'],
    languageOptions: {
      ecmaVersion: 'latest',
      sourceType: 'module',
      parser: typescriptEslintParser
    },
    rules: {
      // Error prevention
      'no-unused-vars': 'error',
      'no-console': ['error', { allow: ['warn', 'error'] }],
      'no-var': 'error',
      'prefer-const': 'error',

      // Code quality
      'no-duplicate-imports': 'error',
      'no-return-await': 'off',
      'require-await': 'error',

      // TypeScript specific
      '@typescript-eslint/no-explicit-any': 'error',
      '@typescript-eslint/no-unused-vars': 'error',
      '@typescript-eslint/explicit-function-return-type': 'error',

      // Philosophy alignment
      'no-throw-literal': 'error',  // Use Result.err, not throw
      'prefer-promise-reject-errors': 'error'  // Reject with Error objects
    }
  }
]
```

#### How to Run

```bash
npm run lint

# Expected output
✓ No lint errors (142 files checked)
```

#### Common Issues

```bash
❌ ERROR: Unexpected 'any' type
✅ Fix: Use specific types or Result<T, E>

❌ ERROR: Unused variable 'data'
✅ Fix: Remove or use the variable

❌ ERROR: Unexpected console statement
✅ Fix: Use logger instead or remove
```

---

### 2. Type Checking

```yaml
check: typecheck
purpose: Type safety and correctness
critical: Yes
```

#### What It Checks

```bash
# Type correctness
✅ No type errors
✅ No implicit any
✅ Proper type definitions
✅ Correct type exports
✅ No type mismatches
```

#### Configuration

```json
// tsconfig.json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

#### How to Run

```bash
npm run typecheck

# Expected output
✓ No TypeScript errors
```

#### Common Issues

```bash
❌ ERROR: Parameter 'user' implicitly has 'any' type
✅ Fix: type user: User

❌ ERROR: Object is possibly 'undefined'
✅ Fix: Use Option<T> pattern

❌ ERROR: Type 'string' is not assignable to 'UUID'
✅ Fix: Use proper type conversion
```

---

### 3. Unit Tests

```yaml
check: test
purpose: Correctness and behavior
critical: Yes
```

#### What It Checks

```bash
# Test coverage
✅ All new code has tests
✅ All modified code has tests
✅ Edge cases covered
✅ Error paths tested
✅ Coverage threshold met (>80%)
```

#### Philosophy-Aligned Tests

```typescript
// ✅ GOOD: Real implementations
import { describe, it, expect } from 'vitest'
import { authenticateUser } from './service'

describe('authenticateUser', () => {
  it('returns user on valid credentials', () => {
    const result = authenticateUser({
      email: 'test@example.com',
      password: 'correct-password'
    })

    expect(result.ok).toBe(true)
    expect(result.value).toHaveProperty('id')
    expect(result.value).toHaveProperty('email')
  })

  it('returns error on invalid credentials', () => {
    const result = authenticateUser({
      email: 'test@example.com',
      password: 'wrong-password'
    })

    expect(result.ok).toBe(false)
    expect(result.error).toHaveProperty('type', 'invalid_credentials')
  })
})

// ❌ BAD: Mocks
describe('authenticateUser', () => {
  it('calls database', () => {
    const mockDb = { query: jest.fn() }
    authenticateUser(mockDb)
    expect(mockDb.query).toHaveBeenCalled()
  })
})
```

#### Configuration

```javascript
// vitest.config.ts
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      lines: 80,
      functions: 80,
      branches: 80,
      statements: 80
    }
  }
})
```

#### How to Run

```bash
npm run test

# Expected output
✓ src/features/auth/service.test.ts (4)
✓ src/features/user/service.test.ts (6)
✓ src/lib/utils.test.ts (3)

Test Files  13 passed (13)
     Tests  42 passed (42)

Coverage: 87.4% (above threshold)
```

---

### 4. Build

```yaml
check: build
purpose: Production readiness
critical: Yes
```

#### What It Checks

```bash
# Build success
✅ Code compiles successfully
✅ No build errors
✅ No missing dependencies
✅ Bundles generate correctly
✅ Assets optimize properly
```

#### Configuration

```javascript
// package.json
{
  "scripts": {
    "build": "next build"
  }
}
```

#### How to Run

```bash
npm run build

# Expected output
✓ Linted and checked 142 files
✓ Compiled successfully
✓ Collecting page data
✓ Generating static pages (3/3)
✓ Finalizing page optimization

Build completed successfully
```

---

## Philosophy-Specific Gates

### 5. Result<T, E> Pattern Check

```yaml
check: result-pattern
purpose: Explicit error handling
critical: Yes
tool: AST analyzer
```

#### What It Checks

```bash
# Result<T, E> usage
✅ Functions returning errors use Result<T, E>
✅ No throw statements (except in error constructors)
✅ No try/catch with silent failures
✅ Error types are explicit (not any)
✅ All Result returns are properly typed
```

#### Examples

```typescript
// ❌ BAD: Throwing errors
function processData(data: string) {
  if (!data) {
    throw new Error('Invalid data')
  }
  return JSON.parse(data)
}

// ✅ GOOD: Result<T, E>
type ProcessError =
  | { type: 'invalid_data' }
  | { type: 'parse_error'; message: string }

function processData(data: string): Result<Data, ProcessError> {
  if (!data) {
    return Result.err({ type: 'invalid_data' })
  }

  try {
    const parsed = JSON.parse(data)
    return Result.ok(parsed)
  } catch (e) {
    return Result.err({
      type: 'parse_error',
      message: String(e)
    })
  }
}
```

#### AST Pattern

```typescript
// AST checker looks for:
// ❌ ThrowStatement
// ✅ ReturnStatement with Result type

// Violation example:
throw new Error('...')

// Correct pattern:
return Result.err({ type: '...', ... })
```

---

### 6. Option<T> Pattern Check

```yaml
check: option-pattern
purpose: Explicit null handling
critical: Yes
tool: AST analyzer
```

#### What It Checks

```bash
# Option<T> usage
✅ No null in type signatures
✅ No undefined in type signatures
✅ Optional values use Option<T>
✅ Pattern matching before use
✅ No non-null assertions (!)
```

#### Examples

```typescript
// ❌ BAD: null in type signature
function findUser(id: string): User | null {
  // ...
}

// ✅ GOOD: Option<T>
function findUser(id: string): Option<User> {
  // ...
}

// ❌ BAD: Non-null assertion
const user = maybeUser!  // Dangerous!

// ✅ GOOD: Pattern matching
user.match({
  some: (u) => console.log(u.name),
  none: () => console.log('User not found')
})
```

---

### 7. No `any` Types Check

```yaml
check: no-any-types
purpose: Type safety
critical: Yes
tool: TypeScript compiler + AST
```

#### What It Checks

```bash
# Type safety
✅ No 'any' in code
✅ No 'unknown' without type guard
✅ Proper type definitions
✅ No type assertions without checks
```

#### Examples

```typescript
// ❌ BAD: any
function process(data: any): any {
  return data.value
}

// ✅ GOOD: Specific types
interface Data {
  value: string
}

function process(data: Data): string {
  return data.value
}

// ❌ BAD: Type assertion
const value = data as ValueType

// ✅ GOOD: Type guard
if (isValueType(data)) {
  const value = data  // Type narrowed
}
```

---

### 8. Function Length Check

```yaml
check: function-length
purpose: Code maintainability
critical: Yes
tool: AST analyzer
```

#### What It Checks

```bash
# Function size
✅ Functions under 50 lines
✅ Cyclomatic complexity < 10
✅ Nesting depth < 4
✅ Parameters < 5
```

#### Configuration

```javascript
// eslint.config.js
{
  rules: {
    'max-lines-per-function': ['error', {
      max: 50,
      skipBlankLines: true,
      skipComments: true
    }],
    'complexity': ['error', 10],
    'max-depth': ['error', 4],
    'max-params': ['error', 4]
  }
}
```

#### Examples

```typescript
// ❌ BAD: 80-line function
function processUserData(user: User, data: Data, config: Config, options: Options, extras: Extras) {
  // 50+ lines of logic
  // deeply nested
  // hard to understand
}

// ✅ GOOD: Split into smaller functions
function processUserData(user: User, data: Data, config: Config): Result<Processed, Error> {
  const validated = validateUserData(user)
  if (!validated.ok) return validated

  const transformed = transformData(data, config)
  if (!transformed.ok) return transformed

  return saveData(validated.value, transformed.value)
}
```

---

## Additional Quality Gates

### 9. Security Scanning

```yaml
check: security
purpose: Vulnerability detection
critical: Yes
tool: SAST scanner
```

#### What It Checks

```bash
# Security issues
✅ No SQL injection vectors
✅ No XSS vulnerabilities
✅ No hardcoded secrets
✅ No insecure dependencies
✅ Input validation present
```

#### Configuration

```bash
# Using SAST tool
npm install -g sast-cli
sast scan ./src

# Or npm audit
npm audit --audit-level=moderate
```

---

### 10. Performance Checks

```yaml
check: performance
purpose: Performance regression prevention
critical: No (warn only)
tool: Benchmark suite
```

#### What It Checks

```bash
# Performance
✅ No significant performance degradation
✅ Memory usage within limits
✅ Response times acceptable
```

---

## CI/CD Pipeline

### GitHub Actions Workflow

```yaml
# .github/workflows/quality-gates.yml
name: Quality Gates

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  quality-checks:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Type check
        run: npm run typecheck

      - name: Unit tests
        run: npm run test

      - name: Test coverage
        run: npm run test:coverage

      - name: Build
        run: npm run build

      - name: Result<T, E> pattern check
        run: npm run check:result-pattern

      - name: Option<T> pattern check
        run: npm run check:option-pattern

      - name: No any types check
        run: npm run check:no-any

      - name: Function length check
        run: npm run check:function-length

      - name: Security scan
        run: npm audit --audit-level=moderate

      - name: Performance check
        run: npm run test:performance
        continue-on-error: true
```

---

## Gate Enforcement

### Branch Protection Rules

```yaml
# .github/branch-protection.yml
main:
  # Require all checks to pass
  required_status_checks:
    strict: true
    checks:
      - lint
      - typecheck
      - test
      - build
      - result-pattern
      - option-pattern
      - no-any-types
      - function-length
      - security

  # Require PR review
  required_pull_request_reviews:
    required_approving_review_count: 1

  # No bypassing
  enforce_admins: true
```

---

## Bypassing Gates

### When Bypasses Are Allowed

```bash
❌ NEVER: Because "it's just a small change"
❌ NEVER: Because "I'll fix it later"
❌ NEVER: Because "CI is flaky"

✅ RARELY: Hotfix for critical production issue
  - Must get post-approval
  - Must create follow-up issue
  - Must fix within 24 hours
```

### Emergency Bypass Process

```bash
1. Document why bypass is needed
2. Get approval from tech lead
3. Merge with "bypass" label
4. Create follow-up issue immediately
5. Fix within 24 hours
```

---

## Custom Gates

### Adding Project-Specific Gates

```yaml
# Example: API breaking changes
check: api-breaking-changes
purpose: Detect API breaking changes
critical: Yes
tool: API diff tool

# Example: Database migration safety
check: migration-safety
purpose: Verify migration safety
critical: Yes
tool: Migration validator

# Example: i18n completeness
check: i18n-complete
purpose: All translations present
critical: No
tool: i18n checker
```

---

## Monitoring and Metrics

### Track These Metrics

```bash
# Gate pass/fail rates
- Lint pass rate: > 95%
- Typecheck pass rate: > 90%
- Test pass rate: > 85%
- Build pass rate: > 90%

# Philosophy compliance
- Result<T, E> usage: > 95%
- Option<T> usage: > 95%
- No any types: > 98%
- Function length compliance: > 90%

# Security
- Vulnerability-free PRs: > 99%
```

---

## Troubleshooting

### Common Gate Failures

```bash
# Lint failures
npm run lint -- --fix

# Typecheck failures
# Check for missing types, incorrect imports

# Test failures
npm run test -- --reporter=verbose

# Build failures
# Check for missing dependencies, circular imports
```

### Flaky Tests

```bash
# If tests are flaky:
1. Fix the tests (don't disable)
2. Add retries only if truly async race condition
3. Increase timeout if needed
4. Fix test isolation issues
```

---

## Related Documentation

- [Pull Request Templates](./templates/README.md) - PR templates
- [Review Guidelines](./review-guidelines.md) - Review process
- [Branching Strategy](./branching-strategy.md) - Branch workflows
- [Code Quality Rules](../../../code-quality/) - Detailed quality rules

---

*Document version: 1.0*
*Last updated: 2025-03-08*
