# Insecure Storage

Insecure Storage occurs when sensitive data is stored without proper encryption or protection.

---

## Severity

**HIGH** - If storage is compromised, all data is exposed.

---

## Common Issues

### 1. Weak Encryption

```typescript
// BAD - Weak algorithms
crypto.createCipher('des', key);     // Broken
crypto.createCipher('rc4', key);    // Broken
crypto.createCipher('aes-128-cbc', key); // Weak
```

### 2. Hardcoded Keys

```typescript
// BAD
const key = 'my-secret-key-123'; // In source code!

// GOOD
const key = process.env.ENCRYPTION_KEY;
```

### 3. No Encryption at Rest

```typescript
// BAD - Plain text storage
await db.users.update({
  where: { id: user.id },
  data: { password: user.password }, // Plain text!
});
```

---

## Prevention

### 1. Strong Encryption

```typescript
// GOOD - Strong algorithms
const ALGORITHM = 'aes-256-gcm';

const encrypt = (plaintext: string): string => {
  const key = Buffer.from(process.env.ENCRYPTION_KEY!, 'hex');
  const iv = crypto.randomBytes(12);
  const cipher = crypto.createCipheriv(ALGORITHM, key, iv);

  const encrypted = cipher.update(plaintext, 'utf8', 'hex') + cipher.final('hex');
  const authTag = cipher.getAuthTag();

  return iv.toString('hex') + ':' + authTag.toString('hex') + ':' + encrypted;
};
```

### 2. Hash Passwords

```typescript
// GOOD - Hash passwords
import bcrypt from 'bcrypt';

const hashPassword = async (password: string): Promise<string> => {
  return bcrypt.hash(password, 12);
};

const verifyPassword = async (password: string, hash: string): Promise<boolean> => {
  return bcrypt.compare(password, hash);
};
```

### 3. Database Encryption

```typescript
// Use database encryption features
// PostgreSQL: CREATE TABLE ... ENCRYPTED
// MongoDB: field-level encryption
// AWS RDS: encryption at rest
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
