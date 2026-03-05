# NoSQL Injection

NoSQL Injection attacks exploit insecure query construction in NoSQL databases like MongoDB.

---

## Severity

**CRITICAL** - Can lead to data extraction or bypass authentication.

---

## How It Works

```
ATTACK FLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Vulnerable MongoDB Query:
─────────────────────────

const query = {
  username: req.body.username,
  password: req.body.password
};

db.users.find(query);

Attack:
─────────────────────────

{
  "username": "admin",
  "$gt": ""
}

Query becomes:
{
  username: "admin",
  password: { "$gt": "" }
}

$gt = greater than - matches any password!
```

---

## Common Payloads

```javascript
// Authentication bypass
{ "$gt": "" }
{ "$ne": null }
{ "$regex": ".*" }

// Data extraction
{ "$where": "this.password.length > 0" }
{ "$function": "function() { return this.password.startsWith('a'); }" }

// Information disclosure
{ "$exists": false }
{ "$type": 4 }
```

---

## Prevention

### 1. Input Validation

```typescript
// GOOD - Validate input types
const LoginSchema = z.object({
  username: z.string().min(1).max(100),
  password: z.string().min(8),
});

app.post('/login', async (req, res) => {
  const data = LoginSchema.parse(req.body);

  // data is now a plain string, not an object
  const user = await db.users.findOne({
    username: data.username,
    password: data.password,
  });
});
```

### 2. Use Parameterized Queries

```typescript
// GOOD - Safe query
const user = await db.users.findOne({
  username: req.body.username, // String only
  password: req.body.password,  // String only
});

// BAD - Object from user input
const query = req.body; // Never do this!
```

### 3. Sanitize Input

```typescript
// GOOD - Sanitize MongoDB operators
function sanitizeInput(input: unknown): string {
  if (typeof input !== 'string') {
    throw new Error('Input must be a string');
  }

  // Remove $ prefix
  return input.replace(/^\$/, '');
}
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
