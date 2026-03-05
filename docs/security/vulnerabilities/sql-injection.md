# SQL Injection

SQL Injection occurs when user input is incorporated into SQL queries without proper sanitization, allowing attackers to manipulate the query structure.

---

## Severity

**CRITICAL** - Can lead to complete database compromise, data exfiltration, or remote code execution.

---

## How It Works

```
ANATOMY OF SQL INJECTION:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Vulnerable Code:
─────────────────────
const userId = req.body.userId;
const query = `SELECT * FROM users WHERE id = ${userId}`;

User Input: 1; DROP TABLE users;--

Expanded Query:
─────────────────────
SELECT * FROM users WHERE id = 1; DROP TABLE users;--

Result: Table deleted, or attacker gets all data
```

---

## Types of SQL Injection

### 1. Classic SQL Injection

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

URL: /user?id=1

SQL: SELECT * FROM users WHERE id = 1

Payload: 1 OR 1=1

Result: SELECT * FROM users WHERE id = 1 OR 1=1
        (Returns ALL users)
```

### 2. Blind SQL Injection

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

When app doesn't show errors but behaves differently:

Payload: 1 AND 1=1  (True - normal response)
Payload: 1 AND 1=2  (False - different response)

Attacker can infer data by asking true/false questions
```

### 3. Union-Based SQL Injection

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Payload: 1 UNION SELECT username,password FROM admin

Result: SELECT * FROM users WHERE id = 1
        UNION
        SELECT username,password FROM admin

Attacker gets admin credentials
```

### 4. Second-Order SQL Injection

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Data stored but executed later:

1. Attacker registers with: admin'--
2. Stored as: admin'--
3. Later, admin'-- is used in query
4. Executes malicious SQL
```

---

## Detection

### AST Patterns to Detect

```typescript
// Pattern 1: String concatenation in query
const sqlInjectionRule = {
  id: 'sql-injection-concat',
  patterns: [
    // Template literals with variables
    {
      pattern: 'TemplateLiteral',
      check: (node: Node) => {
        return hasUnescapedExpressions(node) &&
               isUsedInQuery(node);
      },
    },
    // String concatenation
    {
      pattern: 'BinaryExpression',
      check: (node: Node) => {
        return node.operator === '+' &&
               isStringConcat(node) &&
               isUsedInQuery(node);
      },
    },
    // f-strings / interpolation
    {
      pattern: 'CallExpression',
      check: (node: Node) => {
        return ['format', 'replace'].includes(node.callee.name) &&
               hasUserInput(node.arguments);
      },
    },
  ],
};
```

### Common Vulnerable Patterns

```typescript
// VULNERABLE - AVOID THESE:

// 1. Template literals
const query = `SELECT * FROM users WHERE id = ${userId}`;

// 2. String concatenation
const query = "SELECT * FROM users WHERE id = " + userId;

// 3. f-strings (Python)
query = f"SELECT * FROM users WHERE id = {userId}"

// 4. .format()
query = "SELECT * FROM users WHERE id = {}".format(userId)

// 5. String replacement
query = "SELECT * FROM users WHERE id = " + req.params.id;
```

---

## Prevention

### 1. Parameterized Queries (Best)

```typescript
// GOOD - Parameterized
const query = 'SELECT * FROM users WHERE id = $1';
const result = await db.query(query, [userId]);

// GOOD - Named parameters
const query = 'SELECT * FROM users WHERE id = :userId';
const result = await db.query(query, { userId });

// GOOD - ORM
const user = await db.user.findUnique({
  where: { id: userId },
});
```

### 2. Input Validation

```typescript
// Validate input type and format
function validateUserId(input: unknown): number {
  const parsed = z.number().int().positive().parse(input);
  return parsed;
}

// Use allowlists
const ALLOWED_SORT_FIELDS = ['id', 'name', 'createdAt'];
function validateSortField(field: string): string {
  if (!ALLOWED_SORT_FIELDS.includes(field)) {
    throw new Error('Invalid sort field');
  }
  return field;
}
```

### 3. Escaping (Last Resort)

```typescript
// If you MUST use dynamic SQL
function escapeIdentifier(identifier: string): string {
  // Double up any double quotes
  return identifier.replace(/"/g, '""');
}

// For string values
function escapeString(value: string): string {
  // Escape single quotes
  return value.replace(/'/g, "''");
}
```

### 4. Least Privilege

```typescript
// Database user should have minimal permissions
const dbUser = {
  // Only SELECT on users table
  permissions: ['SELECT ON users'],
  // No DROP, DELETE, etc.
};
```

---

## Testing

### Manual Testing

```
TESTING CHECKLIST:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Numeric Fields
   * Enter: 1 OR 1=1
   * Enter: 1; DROP TABLE users--
   * Enter: 1 UNION SELECT * FROM admin--

2. String Fields
   * Enter: ' OR '1'='1
   * Enter: ' OR '1'='1'--
   * Enter: admin'--

3. Error-Based
   * Enter: '
   * Enter: 1/0
   * Enter: CAST(asdf AS int)

4. Time-Based (Blind)
   * Enter: 1; WAITFOR DELAY '0:0:5'--
   * Enter: 1 AND SLEEP(5)--
```

### Automated Testing

```typescript
// Test with SQL injection payloads
const payloads = [
  "' OR '1'='1",
  "'; DROP TABLE users;--",
  "1 OR 1=1",
  "1 UNION SELECT NULL--",
];

for (const payload of payloads) {
  const response = await fetch(`/user?id=${payload}`);
  expect(response.status).not.toBe(500);
  expect(response.body).not.toContain('SQL');
}
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

app.get('/user', (req, res) => {
  const userId = req.query.id;
  const query = `SELECT * FROM users WHERE id = ${userId}`;
  db.query(query).then(users => res.json(users));
});

AFTER (Fixed):
━━━━━━━━━━━━━━━━

app.get('/user', async (req, res) => {
  const userId = z.number().parse(req.query.id);
  const user = await db.user.findUnique({
    where: { id: userId },
  });
  res.json(user);
});
```

---

## Related Vulnerabilities

- [NoSQL Injection](./nosql-injection.md)
- [LDAP Injection](./ldap-injection.md)
- [Command Injection](./command-injection.md)

---

*Document version: 1.0*
*Last updated: 2026-03-04*
