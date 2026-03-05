# Autonomous Security System

End-to-end security pipeline combining automated scanning with AI-powered fix generation.

---

## The Vision

```
AUTONOMOUS SECURITY:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   Detect → Report → Analyze → Fix → Review                │
│                                                             │
│   All automated, with human in the loop                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘

COMPONENTS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. SECURITY SCANNER (CLI)
   * Finds vulnerabilities
   * Generates reports
   * Creates issues

2. MARTY (Claude Code Action Fork)
   * Analyzes findings
   * Generates patches
   * Creates PRs

3. HUMAN REVIEW
   * Approves or requests changes
   * Final decision
```

---

## The Complete Workflow

### Step 1: Developer Pushes Code

```
DEVELOPER WORKFLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Developer writes code
2. Runs local security scan (optional)
3. Pushes to repository

   $ git push origin feature/login

4. CI/CD triggers automatically
```

---

### Step 2: Security Scanner Runs

```
CI/CD PIPELINE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

.github/workflows/security.yml:

name: Security Scan

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Security Scanner
        run: |
          npx security-scanner scan ./src \
            --level full \
            --output json \
            > security-findings.json

      - name: Create Security Issue
        if: success() && github.event_name == 'push'
        run: |
          npx security-scanner report ./security-findings.json \
            --create-issue

      - name: Upload SARIF
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: security-findings.json
```

---

### Step 3: Issue Created

```
AUTOMATED ISSUE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Title: [Security] Vulnerabilities Found - 3 Issues

Body:

## Security Scan Results

Scan ID: abc123
Timestamp: 2026-03-04T12:00:00Z
Branch: feature/login

### Findings

| Severity | Type | File | Line | Confidence |
|----------|------|------|------|------------|
| CRITICAL | SQL Injection | src/auth.ts | 42 | HIGH |
| HIGH | Hardcoded API Key | src/config.ts | 15 | HIGH |
| MEDIUM | Unsafe Eval | src/utils.ts | 8 | MEDIUM |

### Actions Required

Please review these vulnerabilities before merging.

---

@marty analyze and fix

---

_This issue was automatically created by the security scanner._
```

---

### Step 4: Marty Analyzes and Fixes

```
MARTY WORKFLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Triggered by: @marty analyze and fix

1. READS FINDINGS
   → Downloads security report
   → Parses each vulnerability

2. ANALYZES EACH VULNERABILITY
   → Reads source code
   → Understands context
   → Determines fix approach

3. GENERATES PATCHES
   → Creates code changes
   → Ensures fixes are correct

4. CREATES PR
   → Branch: security-fix-001
   → Title: [Security] Fix SQL Injection and API Key exposure
   → Description: Detailed explanation of fixes
   → Changes: Diff of all fixes

5. RESPONDS IN ISSUE
   → Links to PR
   → Explains each fix
   → Asks for review
```

---

### Step 5: Human Review

```
REVIEW PROCESS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Developer receives notification
2. Reviews PR changes
3. Two options:

   OPTION A: APPROVE
   → Merge PR
   → Vulnerabilities fixed

   OPTION B: REQUEST CHANGES
   → Comment: "This fix doesn't handle X case"
   → @marty updates fix
   → Repeat until approved
```

---

## Marty: Claude Code Action Fork

### What is Marty?

```
MARTY:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Marty is a fork of Claude Code Action, customized for security:

* Responds to @marty mentions
* Analyzes security findings
* Generates patches
* Creates PRs
* Explains fixes

Built on top of Claude Agent SDK:
* Full Claude capabilities
* GitHub API access
* File operations
* PR creation
```

### Configuration

```yaml
# .github/workflows/marty.yml

name: Marty Security

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

jobs:
  marty:
    runs-on: ubuntu-latest
    if: >
      contains(github.event.comment.body, '@marty')
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.event.issue.head_branch }}
          fetch-depth: 0

      - name: Run Marty
        uses: nesalia/marty-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
          prompt: |
            Analyze the security vulnerabilities reported in this issue
            and generate fixes for them.
          claude_args: |
            --max-turns 10
            --model claude-opus-4-6
```

### Marty Commands

```
MARTY COMMANDS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

@marty analyze
→ Analyze vulnerabilities in the issue
→ Explain each one
→ Determine fix strategy

@marty fix
→ Generate patches for all vulnerabilities
→ Create PR with fixes

@marty fix [vulnerability-id]
→ Fix only a specific vulnerability

@marty explain [vulnerability-id]
→ Explain in detail why this is dangerous
→ Show attack scenario

@marty test
→ Generate test cases that exploit the vulnerability
→ Verify fix works
```

---

## Scanner + Marty Integration

### Architecture Diagram

```
COMPLETE SYSTEM:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────────┐
│                     DEVELOPER                                    │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     GIT PUSH                                     │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                  CI/CD PIPELINE                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 1. Checkout Code                                       │   │
│   └─────────────────────────────────────────────────────────┘   │
│                          │                                       │
│                          ▼                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 2. Security Scanner                                    │   │
│   │    $ security-scanner scan ./src --level full          │   │
│   │                                                         │   │
│   │    • AST Analysis                                      │   │
│   │    • AI Analysis                                       │   │
│   │    • Verification                                      │   │
│   └─────────────────────────────────────────────────────────┘   │
│                          │                                       │
│                          ▼                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 3. Generate Report                                     │   │
│   │    • JSON output                                       │   │
│   │    • SARIF for GitHub                                  │   │
│   │    • Create Issue (if critical)                        │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                  SECURITY ISSUE CREATED                          │
│                                                                  │
│   [Security] Vulnerabilities Found                               │
│   @marty analyze and fix                                        │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                  MARTY ACTION                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 4. Read Issue & Findings                              │   │
│   └─────────────────────────────────────────────────────────┘   │
│                          │                                       │
│                          ▼                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 5. Analyze Each Vulnerability                         │   │
│   │                                                         │   │
│   │    • Read source code                                 │   │
│   │    • Understand context                               │   │
│   │    • Determine fix approach                          │   │
│   └─────────────────────────────────────────────────────────┘   │
│                          │                                       │
│                          ▼                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 6. Generate Patches                                   │   │
│   │                                                         │   │
│   │    • Write fixed code                                 │   │
│   │    • Test compilation                                 │   │
│   │    • Verify no new issues                            │   │
│   └─────────────────────────────────────────────────────────┘   │
│                          │                                       │
│                          ▼                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 7. Create PR                                          │   │
│   │                                                         │   │
│   │    • Branch: security-fix-001                        │   │
│   │    • PR with detailed description                     │   │
│   │    • Link to original issue                           │   │
│   └─────────────────────────────────────────────────────────┘   │
│                          │                                       │
│                          ▼                                       │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │ 8. Respond in Issue                                   │   │
│   │                                                         │   │
│   │    • Summary of fixes                                │   │
│   │    • Link to PR                                      │   │
│   │    • Request review                                  │   │
│   └─────────────────────────────────────────────────────────┘   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                  HUMAN REVIEW                                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   Developer reviews PR                                          │
│   ↓                                                             │
│   Approve & Merge  OR  Request Changes                          │
│   ↓                                                             │
│   If changes: @marty update                                    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Example PR Description

```
AUTOMATED PR DESCRIPTION:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Security Fixes

This PR fixes 3 vulnerabilities found by the security scanner.

### Fixed Issues

#### 1. SQL Injection (CRITICAL) - auth.ts:42

**Problem:**
```typescript
// Vulnerable code
const user = await db.query(
  'SELECT * FROM users WHERE id = ' + userId
);
```

**Fix:**
```typescript
// Fixed code
const user = await db.query(
  'SELECT * FROM users WHERE id = $1',
  [userId]
);
```

**Explanation:**
Using parameterized queries prevents SQL injection attacks where malicious input could execute arbitrary SQL commands.

---

#### 2. Hardcoded API Key (HIGH) - config.ts:15

**Problem:**
```typescript
// Vulnerable code
const API_KEY = 'sk_live_1234567890abcdef';
```

**Fix:**
```typescript
// Fixed code
const API_KEY = process.env.STRIPE_API_KEY;
```

**Explanation:**
Hardcoded API keys can be exposed in version control. Always use environment variables.

---

#### 3. Unsafe Eval (MEDIUM) - utils.ts:8

**Problem:**
```typescript
// Vulnerable code
const result = eval(userInput);
```

**Fix:**
```typescript
// Fixed code
const result = safeParse(userInput);
```

**Explanation:**
Using eval() with user input can lead to code injection attacks.

---

### Testing

All fixes have been tested:
- [x] Code compiles without errors
- [x] Tests pass
- [x] No new vulnerabilities introduced

### Review Checklist

- [ ] Verify each fix is correct
- [ ] Check for edge cases
- [ ] Ensure no new vulnerabilities
- [ ] Approve if satisfied

---

_Generated by Marty - AI Security Assistant_
```

---

## Benefits

```
BENEFITS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────┐  ┌─────────────────────┐
│   AUTOMATED        │  │   FAST             │
├─────────────────────┤  ├─────────────────────┤
│ • Scanner runs     │  │ • Issues created   │
│   automatically    │  │   immediately     │
│ • Marty fixes     │  │ • Patches gen in  │
│   without human   │  │   minutes         │
│   intervention    │  │ • No backlog of   │
│                   │  │   security issues │
└─────────────────────┘  └─────────────────────┘

┌─────────────────────┐  ┌─────────────────────┐
│   CONSISTENT       │  │   EDUCATIONAL      │
├─────────────────────┤  ├─────────────────────┤
│ • Same process    │  │ • Developers       │
│   every time      │  │   learn from       │
│ • No human error  │  │   fixes            │
│ • Standardized   │  │ • Security         │
│   fixes          │  │   awareness        │
│                   │  │   improves        │
└─────────────────────┘  └─────────────────────┘

┌─────────────────────┐  ┌─────────────────────┐
│   HUMAN IN LOOP    │  │   AUDIT TRAIL       │
├─────────────────────┤  ├─────────────────────┤
│ • All changes     │  │ • Every finding    │
│   reviewed by     │  │   is documented   │
│   humans          │  │ • Every fix is    │
│ • No automatic    │  │   traceable       │
│   code execution  │  │ • Compliance      │
│   without consent │  │   requirements    │
│                   │  │   met             │
└─────────────────────┘  └─────────────────────┘
```

---

## Configuration Options

### Scanner Only (No Marty)

```yaml
# Just scan and report, no auto-fix
name: Security Scan

on: [push, pull_request]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npx security-scanner scan ./src
      - uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: results.sarif
```

### Scanner + Manual Fix

```yaml
# Scan, report, but fix manually
name: Security Scan

on:
  push:
    branches: [main]
  issues:
    types: [opened]

jobs:
  scan:
    runs-on: ubuntu-latest
    steps:
      - uses: nesalia/security-scanner@v1
      - name: Create issue
        if: github.event_name == 'push'
        run: npx security-scanner report --create-issue
```

### Full Autonomous (Scanner + Marty)

```yaml
# Full pipeline
name: Autonomous Security

on:
  push:
    branches: [main]
  issue_comment:
    types: [created]

jobs:
  scan:
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    steps:
      - uses: nesalia/security-scanner@v1
      - name: Create issue
        run: npx security-scanner report --create-issue

  marty:
    runs-on: ubuntu-latest
    if: >
      github.event_name == 'issue_comment' &&
      contains(github.event.comment.body, '@marty')
    steps:
      - uses: nesalia/marty-action@v1
        with:
          prompt: analyze and fix
```

---

## Safety Considerations

```
SAFETY GUARDS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. HUMAN APPROVAL REQUIRED
   * Marty creates PR, doesn't merge
   * Human must approve all changes

2. SCOPE LIMITATION
   * Only fixes reported vulnerabilities
   * Doesn't modify unrelated code
   * Minimal diff

3. TESTING
   * Marty runs tests before creating PR
   * PR blocked if tests fail

4. RATE LIMITING
   * Limited number of fixes per PR
   * Prevents overwhelming reviewers

5. AUDIT LOG
   * All actions logged
   * Can trace back every change
```

---

## Related Documentation

- [Security Scanner Architecture](./security-scanner-architecture.md) - Scanner details
- [Verification Gates](./verification-gates.md) - Gate system
- [Meta-Framework Philosophy](./meta-framework-philosophy.md) - Overall vision

---

*Document version: 1.0*
*Last updated: 2026-03-04*
