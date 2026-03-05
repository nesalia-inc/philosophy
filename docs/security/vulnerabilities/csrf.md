# CSRF (Cross-Site Request Forgery)

CSRF attacks force authenticated users to perform unwanted actions on web applications where they're currently authenticated.

---

## Severity

**MEDIUM** - Requires user to be authenticated. Can lead to unwanted state changes.

---

## How It Works

```
ATTACK FLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. User logs into vulnerable site (bank.com)

2. Attacker sends malicious page:
   <html>
   <img src="https://bank.com/transfer?to=attacker&amount=10000">
   </html>

3. User visits attacker's page (while still logged into bank)

4. Browser automatically sends cookies to bank.com

5. Bank processes request - user loses money!

Key: Browser automatically includes cookies with requests
```

---

## Types of CSRF

### 1. GET-Based CSRF

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

<img src="https://example.com/delete?id=123">

<a href="https://example.com/transfer?to=attacker&amount=10000">

<script>
fetch('https://example.com/change-email?email=attacker@evil.com');
</script>
```

### 2. POST-Based CSRF

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

<form action="https://example.com/transfer" method="POST">
  <input type="hidden" name="to" value="attacker">
  <input type="hidden" name="amount" value="10000">
</form>

<script>document.forms[0].submit();</script>
```

### 3. JSON CSRF

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

<script>
fetch('https://api.example.com/user', {
  method: 'PUT',
  credentials: 'include',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ role: 'admin' }),
});
</script>
```

---

## Detection

### AST Patterns

```typescript
const csrfPatterns = {
  issues: [
    // No CSRF token
    {
      pattern: 'router.post',
      check: (node) => !hasCSRFProtection(node),
      issue: 'POST endpoint without CSRF protection',
    },
    // No SameSite cookie
    {
      pattern: 'cookie(',
      check: (node) => !hasSameSite(node),
      issue: 'Session cookie without SameSite attribute',
    },
    // CORS allows credentials
    {
      pattern: 'cors(',
      check: (node) => node.credentials === true,
      issue: 'CORS with credentials from any origin',
    },
  ],
};
```

---

## Prevention

### 1. CSRF Tokens

```typescript
// GOOD - Use CSRF tokens
import csrf from 'csurf';

const csrfProtection = csrf({ cookie: true });

// Middleware
app.use(csrfProtection);

// Form
<form action="/transfer" method="POST">
  <input type="hidden" name="_csrf" value="<%= csrfToken %>">
  ...
</form>

// Header (for APIs)
fetch('/api/transfer', {
  method: 'POST',
  headers: {
    'X-CSRF-Token': csrfToken,
  },
});
```

### 2. SameSite Cookies

```typescript
// GOOD - SameSite cookie attribute
res.cookie('session', token, {
  httpOnly: true,
  secure: true,
  sameSite: 'strict', // or 'lax'
});
```

### 3. Custom Headers

```typescript
// GOOD - Require custom header
app.use((req, res, next) => {
  if (['POST', 'PUT', 'DELETE'].includes(req.method)) {
    const csrfToken = req.headers['x-csrf-token'];
    if (!csrfToken || csrfToken !== req.csrfToken) {
      return res.status(403).json({ error: 'Invalid CSRF token' });
    }
  }
  next();
});
```

### 4. Origin Header Check

```typescript
// GOOD - Check Origin header
app.use((req, res, next) => {
  const origin = req.headers.origin;
  const allowedOrigins = ['https://example.com'];

  if (!allowedOrigins.includes(origin)) {
    return res.status(403).json({ error: 'Origin not allowed' });
  }
  next();
});
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

app.post('/transfer', (req, res) => {
  const { to, amount } = req.body;
  db.transfer(req.user.id, to, amount);
  res.json({ success: true });
});

AFTER (Fixed):
━━━━━━━━━━━━━━━━

import csrf from 'csurf';

const csrfProtection = csrf({ cookie: true });
app.use(csrfProtection);

app.get('/form', (req, res) => {
  res.render('transfer-form', { csrfToken: req.csrfToken() });
});

app.post('/transfer', (req, res) => {
  const { to, amount } = req.body;

  // 1. CSRF check (handled by middleware)
  // 2. Authorization
  if (!req.user.canTransfer()) {
    return res.status(403).json({ error: 'Forbidden' });
  }

  db.transfer(req.user.id, to, amount);
  res.json({ success: true });
});
```

---

## Related Vulnerabilities

- [XSS](./xss.md) - Can steal CSRF tokens
- [Broken Access Control](./broken-access-control.md)

---

*Document version: 1.0*
*Last updated: 2026-03-04*
