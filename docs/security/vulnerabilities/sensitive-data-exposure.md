# Sensitive Data Exposure

Sensitive Data Exposure occurs when applications don't adequately protect sensitive information like credentials, session tokens, personal data, or business-critical information.

---

## Severity

**CRITICAL** - Can lead to identity theft, financial fraud, and regulatory violations (GDPR, PCI-DSS).

---

## Types of Exposure

### 1. Data in Transit

```
PROBLEM:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Data sent unencrypted over network:

- HTTP instead of HTTPS
- WebSocket without TLS
- API calls over HTTP
- Email without encryption
```

### 2. Data at Rest

```
PROBLEM:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Data stored without encryption:

- Database without encryption
- Backups unencrypted
- Logs containing sensitive data
- Files on disk without encryption
```

### 3. Data in URLs

```
PROBLEM:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Sensitive data in URLs:

/api/user?id=12345                    (ID exposed)
/search?q=salary+employee+confidential (Search terms logged)
/download?file=invoice.pdf&token=abc (Token in logs)
```

### 4. Verbose Errors

```
PROBLEM:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Error messages reveal sensitive info:

Database Error: Table 'users' doesn't exist
Stack Trace: at Database.query(line 42)
Configuration: Connection string: Server=...;User=...;Password=...
```

---

## Detection

### AST Patterns

```typescript
const sensitiveDataPatterns = {
  issues: [
    // Data in URL
    {
      pattern: 'router.get',
      check: (node) => hasSensitiveParams(node, ['password', 'token', 'secret']),
      issue: 'Sensitive data in URL parameters',
    },
    // Logging sensitive data
    {
      pattern: 'logger.',
      check: (node) => hasSensitiveData(node, ['password', 'creditCard', 'ssn']),
      issue: 'Logging sensitive data',
    },
    // Weak encryption
    {
      pattern: 'crypto.createCipher',
      check: (node) => node.algorithm === 'des' || node.algorithm === 'rc4',
      issue: 'Weak encryption algorithm',
    },
  ],
};
```

---

## Prevention

### 1. Use HTTPS

```typescript
// GOOD - Force HTTPS
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('key.pem'),
  cert: fs.readFileSync('cert.pem'),
};

https.createServer(options, app);

// GOOD - HSTS Header
app.use((req, res, next) => {
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  next();
});
```

### 2. Encrypt Sensitive Data

```typescript
// GOOD - Encrypt sensitive fields
import crypto from 'crypto';

const ALGORITHM = 'aes-256-gcm';

function encrypt(text: string, key: Buffer): string {
  const iv = crypto.randomBytes(12);
  const cipher = crypto.createCipheriv(ALGORITHM, key, iv);

  let encrypted = cipher.update(text, 'utf8', 'hex');
  encrypted += cipher.final('hex');

  const authTag = cipher.getAuthTag();

  return iv.toString('hex') + ':' + authTag.toString('hex') + ':' + encrypted;
}
```

### 3. Mask Data in Logs

```typescript
// GOOD - Mask sensitive data
const maskSensitive = (data: any): any => {
  if (typeof data !== 'object') return data;

  const sensitiveFields = ['password', 'token', 'secret', 'creditCard', 'ssn'];
  const masked = { ...data };

  for (const field of sensitiveFields) {
    if (masked[field]) {
      masked[field] = '****';
    }
  }

  return masked;
};

logger.info('User login', maskSensitive({ user: 'john', password: 'secret123' }));
// Output: { user: 'john', password: '****' }
```

### 4. Don't Use URLs for Sensitive Data

```typescript
// BAD
const token = req.query.token;
res.redirect(`/verify?token=${token}`);

// GOOD
res.cookie('verify_token', token, { httpOnly: true, secure: true });
res.redirect('/verify');
```

### 5. Generic Error Messages

```typescript
// GOOD - Generic errors
app.use((err, req, res, next) => {
  console.error(err); // Log full error internally

  res.status(500).json({
    error: 'An unexpected error occurred',
    // Don't expose: err.message, err.stack
  });
});
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

app.get('/user', (req, res) => {
  const user = db.getUser(req.query.id);

  // Sensitive data exposed!
  res.json({
    id: user.id,
    password: user.password,      // NEVER
    creditCard: user.creditCard, // NEVER
    ssn: user.ssn,               // NEVER
    email: user.email,
  });
});

logger.info('User data', user); // Logs everything!

AFTER (Fixed):
━━━━━━━━━━━━━━━━

app.get('/user', authorize(), async (req, res) => {
  const user = await db.user.findUnique({
    where: { id: req.user.id },
    select: {
      id: true,
      name: true,
      email: true,
      // Never select: password, creditCard, ssn
    },
  });

  // Mask any remaining sensitive fields
  const safeUser = maskSensitive(user);

  res.json(safeUser);
});

function maskSensitive(data) {
  return {
    ...data,
    creditCard: data.creditCard ? '****' + data.creditCard.slice(-4) : undefined,
  };
}
```

---

## Related Vulnerabilities

- [Hardcoded Secrets](./hardcoded-secrets.md)
- [Insecure Storage](./insecure-storage.md)
- [Security Misconfiguration](./security-misconfiguration.md)

---

*Document version: 1.0*
*Last updated: 2026-03-04*
