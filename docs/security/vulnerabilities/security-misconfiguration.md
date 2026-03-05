# Security Misconfiguration

Security Misconfiguration occurs when security settings are defined, implemented, or maintained improperly.

---

## Severity

**HIGH** - Can expose the entire application.

---

## Common Issues

### 1. Default Credentials

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

- admin/admin
- root/root
- user/user

Often unchanged in:
- Admin panels
- Databases
- Cloud services
```

### 2. Debug Mode in Production

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

// Express
app.use(morgan('dev')); // Shows detailed errors

// Django
DEBUG = True

// Flask
app.debug = True

Exposes: Stack traces, configuration, environment variables
```

### 3. Missing Security Headers

```
MISSING HEADERS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

- Strict-Transport-Security (HSTS)
- Content-Security-Policy (CSP)
- X-Content-Type-Options
- X-Frame-Options
- X-XSS-Protection
- Referrer-Policy
```

### 4. Verbose Error Messages

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Error: SQLSTATE[HY000] [2002] Connection refused
  at TCP.connect (net.js:xxx:yyy)

Database: mysql://user:password@localhost:3306/app

Full stack trace exposed!
```

### 5. Unnecessary Features

```
ENABLED BY DEFAULT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

- Directory listing
- Source code exposure
- Test endpoints
- Sample files
- Debug endpoints
- API documentation
```

---

## Prevention

### 1. Security Headers

```typescript
// GOOD - Set security headers
app.use((req, res, next) => {
  res.setHeader('Strict-Transport-Security', 'max-age=31536000; includeSubDomains');
  res.setHeader('Content-Security-Policy', "default-src 'self'");
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Referrer-Policy', 'strict-origin-when-cross-origin');
  next();
});
```

### 2. Disable Debug in Production

```typescript
// GOOD - Disable debug in production
const isProduction = process.env.NODE_ENV === 'production';

app.use(morgan(isProduction ? 'combined' : 'dev'));

// Don't expose errors in production
if (!isProduction) {
  app.use(errorHandler());
}
```

### 3. Generic Error Messages

```typescript
// GOOD - Generic error handler
app.use((err, req, res, next) => {
  console.error(err); // Log internally

  res.status(500).json({
    error: 'An unexpected error occurred',
  });
});
```

### 4. Disable Directory Listing

```typescript
// Express
const options = {
  index: false, // Don't serve index.html
};
app.use(express.static('public', options));

// Nginx
autoindex off;
```

### 5. Regular Security Audits

```typescript
// Use security scanners
// nmap, nikto, OWASP ZAP
// Check for misconfigurations
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

const app = express();

app.use(morgan('dev')); // Verbose logs
app.use(express.static('public')); // Directory listing

app.get('/debug', (req, res) => {
  res.json({
    env: process.env,
    config: app.get('env'),
  });
});

AFTER (Fixed):
━━━━━━━━━━━━━━━━

const app = express();

const isProduction = process.env.NODE_ENV === 'production';

// Only verbose logging in dev
app.use(morgan(isProduction ? 'combined' : 'dev'));

// Static files without directory listing
app.use(express.static('public', { index: false }));

// Security headers
app.use(helmet());

// Generic errors in production
app.use((err, req, res, next) => {
  res.status(500).json({ error: 'An error occurred' });
});

// Remove debug endpoints in production
if (!isProduction) {
  app.get('/debug', (req, res) => { ... });
}
```

---

## Related Vulnerabilities

- [Sensitive Data Exposure](./sensitive-data-exposure.md)
- [Debug Mode](./debug-mode.md)

---

*Document version: 1.0*
*Last updated: 2026-03-04*
