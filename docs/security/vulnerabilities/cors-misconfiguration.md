# CORS Misconfiguration

CORS (Cross-Origin Resource Sharing) misconfiguration allows attackers to access resources from unauthorized origins.

---

## Severity

**MEDIUM** - Can allow malicious sites to make requests on behalf of users.

---

## Common Issues

### 1. Wildcard Origin

```typescript
// BAD - Allows any origin
app.use(cors({
  origin: '*'
}));
```

### 2. Credentials + Wildcard

```typescript
// BAD - Cannot use credentials with wildcard
app.use(cors({
  origin: '*',
  credentials: true
}));
```

### 3. Reflected Origin

```typescript
// BAD - Reflects any origin
app.use(cors({
  origin: (origin, callback) => callback(null, origin)
}));
```

---

## Prevention

### 1. Explicit Origins

```typescript
// GOOD - Whitelist specific origins
const allowedOrigins = [
  'https://example.com',
  'https://app.example.com',
];

app.use(cors({
  origin: allowedOrigins
}));
```

### 2. Dynamic Origin Check

```typescript
// GOOD - Check against whitelist
const allowedOrigins = new Set([
  'https://example.com',
  'https://app.example.com',
]);

app.use(cors({
  origin: (origin, callback) => {
    if (!origin || allowedOrigins.has(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  }
}));
```

### 3. Proper Credentials

```typescript
// GOOD - Explicit credentials
app.use(cors({
  origin: 'https://example.com',
  credentials: true,
}));
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
