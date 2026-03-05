# Security Scanner Architecture

A comprehensive CLI system for analyzing security vulnerabilities using AST analysis and AI-powered verification.

---

## The Vision

```
GOAL:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Build a security scanner that:

1. Finds vulnerabilities that traditional tools miss
2. Uses AI to understand context and complex patterns
3. Verifies findings to reduce false positives
4. Suggests patches for human review
5. Works as a CLI tool, integrated in CI/CD

Similar to Claude Code Security, but:
* Open source
* Customizable rules
* Hybrid AST + AI approach
```

---

## Why Hybrid AST + AI?

```
COMPARISON:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────┬──────────────┬──────────────┬──────────────┐
│                     │  AST Only    │  AI Only     │  HYBRID     │
├─────────────────────┼──────────────┼──────────────┼──────────────┤
│ Speed               │   Fast       │   Slow       │   Fast*     │
│ Cost                │   Free       │   High       │   Medium    │
│ Recall              │   Medium     │   High       │   High      │
│ Precision           │   Medium     │   Medium     │   High      │
│ Complex bugs        │   Low        │   High       │   High      │
│ Deterministic       │   Yes        │   No         │   Mixed     │
│ Easy to debug       │   Yes        │   No         │   Partial   │
└─────────────────────┴──────────────┴──────────────┴──────────────┘

* Hybrid is fast because AST handles simple cases, AI handles complex
```

---

## Architecture Overview

```
SYSTEM ARCHITECTURE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                        CLI INTERFACE                         │
│  $ security scan ./src --level full                         │
│  $ security scan ./src --output json                        │
│  $ security scan ./src --fix                               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                    SCANNER PIPELINE                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ STAGE 1: Quick Scan (AST Rules)                      │   │
│  │ • Parse code to AST                                   │   │
│  │ • Match against known patterns                       │   │
│  │ • Fast, deterministic                                │   │
│  │ • High recall, medium precision                      │   │
│  └──────────────────────────────────────────────────────┘   │
│                         │                                    │
│                         ▼                                    │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ STAGE 2: Deep Scan (AI Analysis)                     │   │
│  │ • Analyze complex code context                       │   │
│  │ • Trace data flow                                    │   │
│  │ • Find logic vulnerabilities                        │   │
│  │ • Slower but thorough                                │   │
│  └──────────────────────────────────────────────────────┘   │
│                         │                                    │
│                         ▼                                    │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ STAGE 3: Verification                                │   │
│  │ • Re-analyze each finding                           │   │
│  │ • Attempt to disprove                               │   │
│  │ • Confidence scoring                                │   │
│  │ • Reduces false positives                           │   │
│  └──────────────────────────────────────────────────────┘   │
│                         │                                    │
│                         ▼                                    │
│  ┌──────────────────────────────────────────────────────┐   │
│  │ STAGE 4: Patch Generation (Optional)                 │   │
│  │ • Generate fix suggestions                          │   │
│  │ • Show before/after                                 │   │
│  │ • Human approval before apply                       │   │
│  └──────────────────────────────────────────────────────┘   │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Stage 1: Quick Scan (AST Rules)

### What It Does

```
AST ANALYSIS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Parse source code to AST (Abstract Syntax Tree)
2. Traverse AST and match against rule patterns
3. Flag potential vulnerabilities
4. Return results in milliseconds

Example patterns:
* Dangerous function calls (eval, exec)
* Hardcoded strings matching API key patterns
* Missing null checks
* SQL query string concatenation
```

### Priority Rules (Phase 1)

```
HIGH IMPACT, CLEAR PATTERNS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. HARDCODE SECRETS
   • API keys: /api[_-]?key/i
   • Passwords: /password\s*=/i
   • Private keys: /-----BEGIN.*PRIVATE KEY-----/
   • Tokens: /Bearer\s+[a-zA-Z0-9-]+/

2. SQL INJECTION
   • `query("SELECT * FROM " + table)`
   • `db.query(userInput)`
   • Raw string concatenation in SQL

3. COMMAND INJECTION
   • exec(userInput)
   • spawn(userInput, ...)
   • system(userInput)

4. XSS (CROSS-SITE SCRIPTING)
   • innerHTML = userInput
   • document.write(userInput)
   • eval(userInput)

5. UNSAFE EVAL
   • eval(...)
   • new Function(...)

6. MISSING AUTH CHECKS
   • API route without auth middleware
   • Protected route handler without verification

7. INSECURE RANDOM
   • Math.random() for security purposes
   • crypto.pseudoRandomBytes()

8. UNSAFE DESERIALIZATION
   • JSON.parse(untrusted)
   • pickle.load(untrusted)
```

### Implementation

```typescript
// Example AST rule for SQL injection
const sqlInjectionRule: SecurityRule = {
  id: 'sql-injection',
  severity: 'high',
  patterns: [
    // String concatenation in query
    {
      pattern: 'QueryBuilder',
      check: (node) => {
        // Check if user input flows into string concatenation
        return hasStringConcat(node.arguments);
      },
    },
    // Template literals with variables
    {
      pattern: 'TemplateLiteral',
      check: (node) => {
        return hasUnescapedExpressions(node);
      },
    },
  ],
  message: 'Potential SQL injection vulnerability',
  suggestion: 'Use parameterized queries instead',
};
```

---

## Stage 2: Deep Scan (AI Analysis)

### What It Does

```
AI ANALYSIS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Understand code context (not just patterns)
2. Trace data flow through the application
3. Find complex vulnerabilities that AST can't detect
4. Analyze business logic flaws

Examples of what AI finds that AST misses:
* Broken access control (logic spread across files)
* Race conditions
* Business logic vulnerabilities
* Authentication bypasses
* IDOR (Insecure Direct Object Reference)
```

### When to Use AI

```
USE AI FOR:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Complex data flow analysis
* Cross-file vulnerability detection
* Business logic flaws
* Authentication/authorization bugs
* Race conditions
* Logic errors

DON'T USE AI FOR:
* Simple pattern matching (use AST)
* Well-known vulnerabilities (use AST)
* Fast scanning (use AST)
```

### AI Integration

```typescript
// AI analysis prompt structure
const aiAnalysisPrompt = `
You are a security researcher analyzing code for vulnerabilities.

CODE CONTEXT:
\`\`\`
${codeContext}
\`\`\`

ANALYZE FOR:
1. Authentication bypasses
2. Authorization flaws
3. Data validation issues
4. Race conditions
5. Business logic vulnerabilities
6. Complex injection points

For each vulnerability found, provide:
- Location (file, line)
- Type of vulnerability
- Severity (critical, high, medium, low)
- Confidence (high, medium, low)
- Explanation
- Proof of concept (if possible)
`;
```

---

## Stage 3: Verification

### The Verification Loop

```
VERIFICATION PROCESS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  FINDING: "eval(userInput) at line 42"                   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Step 1: Re-analyze with context                     │   │
│  │ "Is this really dangerous in this context?"        │   │
│  │ Context: userInput comes from authenticated API     │   │
│  │ Context: There's sanitization at line 30             │   │
│  └─────────────────────────────────────────────────────┘   │
│                         │                                    │
│                         ▼                                    │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Step 2: Try to disprove                             │   │
│  │ "Find reasons this is NOT a vulnerability"          │   │
│  │ - Input is validated                                │   │
│  │ - Only called by admin users                       │   │
│  │ - Results are not displayed to users               │   │
│  └─────────────────────────────────────────────────────┘   │
│                         │                                    │
│                         ▼                                    │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Step 3: Confidence Scoring                          │   │
│  │                                                       │   │
│  │ HIGH: AI confirms + AST matches                    │   │
│  │ MEDIUM: AI confirms only                           │   │
│  │ LOW: AST pattern only                               │   │
│  └─────────────────────────────────────────────────────┘   │
│                         │                                    │
│                         ▼                                    │
│  RESULT: Confidence: MEDIUM                               │
│          Reason: "eval used but with validation"          │
│          Action: "Review manually"                        │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Verification Prompt

```typescript
const verificationPrompt = `
You are verifying a potential security vulnerability.

POTENTIAL VULNERABILITY:
${vulnerabilityDetails}

CODE CONTEXT:
${fullContext}

TASK:
1. Analyze if this is a REAL vulnerability
2. Look for mitigations that might make this safe
3. Consider the attack surface
4. Provide your confidence level

Respond in JSON:
{
  "isRealVulnerability": boolean,
  "confidence": "high" | "medium" | "low",
  "reasoning": "...",
  "mitigations": ["..."],
  "recommendedAction": "block" | "review" | "ignore"
}
`;
```

---

## Stage 4: Patch Generation

### Optional but Powerful

```
PATCH GENERATION:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Not all scanners generate fixes. Claude Code Security does.

Benefits:
* Saves developer time
* Shows exactly what's wrong
* Educational

Cautions:
* Can introduce new bugs
* Must have human approval
* Only for high-confidence findings
```

### Patch Example

```
FINDING:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

File: src/auth.ts:42
Vulnerability: SQL Injection
Code:
  const user = await db.query(
    'SELECT * FROM users WHERE id = ' + userId
  );

SUGGESTED PATCH:
  const user = await db.query(
    'SELECT * FROM users WHERE id = $1',
    [userId]
  );

APPROVED BY: Human  →  APPLIED
```

---

## CLI Interface

### Usage

```
COMMAND LINE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

$ security scan <path> [options]

Options:
  --level [quick|deep|full]    Scan level (default: quick)
  --output [table|json|sarif]  Output format (default: table)
  --fix                        Generate fix suggestions
  --severity [critical|high|medium|low]  Filter by severity
  --exclude <pattern>          Exclude files
  --include <pattern>          Include files only

Examples:
  $ security scan ./src                    # Quick scan
  $ security scan ./src --level deep       # Deep scan
  $ security scan ./src --output json      # JSON output
  $ security scan ./src --fix              # With patches
```

### Output Formats

```
TABLE OUTPUT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

$ security scan ./src --severity high

┌─────────────┬──────────────────┬─────────────────┬──────────┐
│ Severity    │ File             │ Issue           │ Confidence│
├─────────────┼──────────────────┼─────────────────┼──────────┤
│ CRITICAL    │ src/auth.ts:42   │ SQL Injection   │ HIGH     │
│ HIGH        │ src/api.ts:15    │ Hardcoded API   │ HIGH     │
│ HIGH        │ src/eval.ts:8    │ Unsafe eval     │ MEDIUM   │
│ MEDIUM      │ src/utils.ts:22  │ Weak random     │ HIGH     │
└─────────────┴──────────────────┴─────────────────┴──────────┘

4 vulnerabilities found
```

```
JSON OUTPUT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{
  "scanId": "abc123",
  "timestamp": "2026-03-04T12:00:00Z",
  "findings": [
    {
      "id": "vuln-001",
      "severity": "critical",
      "type": "sql-injection",
      "file": "src/auth.ts",
      "line": 42,
      "confidence": "high",
      "message": "Potential SQL injection",
      "suggestion": "Use parameterized queries"
    }
  ],
  "summary": {
    "critical": 1,
    "high": 2,
    "medium": 1,
    "low": 0
  }
}
```

---

## Package Structure

```
PACKAGE STRUCTURE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

security-scanner/
├── cli/
│   ├── index.ts          # Entry point
│   ├── commands/
│   │   ├── scan.ts       # scan command
│   │   └── init.ts       # init command
│   └── output/
│       ├── table.ts      # Table formatter
│       ├── json.ts       # JSON formatter
│       └── sarif.ts      # SARIF formatter
│
├── core/
│   ├── scanner.ts        # Main scanner engine
│   ├── parser/
│   │   ├── typescript.ts # TS parser
│   │   ├── javascript.ts # JS parser
│   │   └── python.ts    # Python parser
│   ├── rules/
│   │   ├── rule.ts      # Rule interface
│   │   ├── matcher.ts   # AST matcher
│   │   └── registry.ts  # Rule registry
│   ├── ai/
│   │   ├── analyzer.ts  # AI analyzer
│   │   ├── verifier.ts  # Verification loop
│   │   └── client.ts    # AI API client
│   └── fixer/
│       ├── generator.ts  # Patch generator
│       └── applier.ts    # Patch applier
│
├── rules/
│   ├── typescript/       # TS/JS specific rules
│   │   ├── secrets.ts    # Hardcoded secrets
│   │   ├── injection.ts  # Injection vulnerabilities
│   │   └── eval.ts       # Unsafe eval
│   ├── nodejs/           # Node.js specific
│   │   ├── auth.ts       # Authentication issues
│   │   └── crypto.ts     # Crypto issues
│   └── common/           # Language agnostic
│       ├── dependencies.ts
│       └── config.ts
│
└── tests/
    ├── fixtures/         # Test vulnerable code
    └── rules/            # Rule tests
```

---

## Testing & Validation

### Test Suites

```
TESTING APPROACH:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. OWASP Vulnerable Applications
   * WebGoat (Java)
   * DVWA (PHP)
   * Juice Shop (Node.js)

2. Deliberately Vulnerable Codebases
   * Find real vulnerabilities to test against

3. Known CVE Examples
   * Test against known CVEs
   * Ensure detection
```

### Metrics

```
METRICS TO TRACK:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. RECALL
   * % of vulnerabilities found
   * Target: > 90%

2. PRECISION
   * % of true positives vs false positives
   * Target: > 80%

3. SPEED
   * Time per file
   * Target: < 100ms for AST, < 5s for AI

4. COVERAGE
   * Languages supported
   * Frameworks supported
```

### Benchmarking

```
BENCHMARK AGAINST:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* CodeQL
* SonarQube
* Snyk
* Semgrep
* Bandit (Python)
```

---

## Roadmap

```
IMPLEMENTATION TIMELINE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

MONTH 1: MVP
─────────────────────────────────────────
* Basic CLI structure
* AST parser for TypeScript
* 20 high-priority rules
* JSON output
* 100+ test cases

MONTH 2: AI INTEGRATION
─────────────────────────────────────────
* Claude API integration
* Deep scan capability
* Verification loop
* Confidence scoring

MONTH 3: PATCHES & POLISH
─────────────────────────────────────────
* Patch generation
* Fix suggestions
* CI/CD integration
* GitHub Actions
* IDE plugins (VS Code)

MONTH 4+: EXTENSION
─────────────────────────────────────────
* More languages (Python, Go, Rust)
* Custom rules engine
* Team features
* Dashboard
* Enterprise features
```

---

## Integration Examples

### GitHub Actions

```yaml
name: Security Scan

on: [push, pull_request]

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Security Scanner
        run: |
          npx security-scanner scan ./src \
            --level full \
            --output sarif \
            --severity high \
            > results.sarif

      - name: Upload results
        uses: github/codeql-action/upload-sarif@v3
        with:
          sarif_file: results.sarif

      - name: Fail on critical
        if: failure()
        run: echo "Critical vulnerabilities found!"
```

### Pre-commit Hook

```yaml
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: security-scan
        name: Security Scan
        entry: npx security-scanner scan --level quick
        language: system
        types: [typescript, javascript]
```

---

## Related Documentation

- [Verification Gates](./verification-gates.md) - Gate system
- [Framework Primitives](./framework-primitives.md) - Primitive patterns
- [Meta-Framework Philosophy](./meta-framework-philosophy.md) - Overall vision

---

*Document version: 1.0*
*Last updated: 2026-03-04*
