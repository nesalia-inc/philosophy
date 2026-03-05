# JWT Issues

JSON Web Token (JWT) implementation vulnerabilities can lead to authentication bypass, privilege escalation, or token forgery.

---

## Severity

**HIGH** - Can lead to complete authentication bypass.

---

## Common JWT Vulnerabilities

### 1. Algorithm None (alg:none)

```
ATTACK:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Attackers modify the header to set algorithm to "none":

Header:
{
  "alg": "none",
  "typ": "JWT"
}

Payload:
{
  "sub": "admin",
  "role": "admin"
}

Server accepts token WITHOUT signature verification!
```

### 2. Weak Secret Key

```
ATTACK:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

If secret is weak, attackers can brute force it:

Common weak secrets:
- "secret"
- "password"
- "123456"
- "jwt_secret"

Tools: jwt-cracker, hashcat
```

### 3. Algorithm Confusion (RS256 → HS256)

```
ATTACK:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Server uses RS256 (asymmetric):
- Public key verifies token
- Private key signs token

Attacker:
1. Gets public key
2. Creates token using public key as secret with HS256
3. Server uses public key as secret for HS256
4. Token is valid!

Expected: RS256 (asymmetric)
Attacked: HS256 (symmetric) with public key as secret
```

### 4. Not Validating Claims

```
ATTACK:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Server doesn't validate:
- exp (expiration)
- nbf (not before)
- iss (issuer)
- aud (audience)

Attacker:
1. Creates token with future expiration
2. Uses token before server checks
3. Or uses expired token
```

### 5. Key Injection

```
ATTACK:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

If server uses the token's "kid" (key ID) parameter
to select the signing key:

{
  "alg": "HS256",
  "kid": "../../../etc/passwd"
}

Server might read arbitrary files as keys!
```

---

## Detection

### AST Patterns to Detect

```typescript
const jwtPatterns = {
  vulnerablePatterns: [
    // No algorithm verification
    {
      pattern: 'jwt.verify(token, "")',
      issue: 'Empty secret',
    },
    // No expiration check
    {
      pattern: 'jwt.sign(payload, secret)',
      check: (node) => !hasExpirationClaim(node),
      issue: 'No expiration in token',
    },
    // Algorithm not set (defaults to HS256)
    {
      pattern: 'jwt.sign(payload, secret)',
      check: (node) => node.options?.algorithm === undefined,
      issue: 'Default algorithm used',
    },
    // Public key as secret
    {
      pattern: 'jwt.verify',
      check: (node) => isPublicKey(node.secret),
      issue: 'Public key used as secret',
    },
  ],
};
```

---

## Prevention

### 1. Always Verify Algorithm

```typescript
// GOOD - Explicit algorithm
const decoded = jwt.verify(token, secret, {
  algorithms: ['HS256'], // Whitelist allowed algorithms
});

// GOOD - Accept only RS256 or ES256
jwt.verify(token, publicKey, {
  algorithms: ['RS256', 'ES256'],
});
```

### 2. Use Strong Secrets

```typescript
// GOOD - Strong random secret
const secret = crypto.randomBytes(64).toString('hex');
// 128 characters of randomness

// Or use key management
const secret = await keyManagement.getKey('jwt-secret');
```

### 3. Validate All Claims

```typescript
// GOOD - Full validation
jwt.verify(token, secret, {
  algorithms: ['HS256'],
  issuer: 'my-app',           // Validate issuer
  audience: 'my-api',         // Validate audience
  complete: true,             // Return full token
});

// GOOD - Check expiration manually if needed
const decoded = jwt.decode(token);
if (decoded.exp && Date.now() >= decoded.exp * 1000) {
  throw new Error('Token expired');
}
```

### 4. Don't Trust Token Claims

```typescript
// BAD - Trusting token claims for authorization
if (decoded.role === 'admin') {
  // Attack can set role to admin!
}

// GOOD - Use database for authorization
const user = await db.user.findUnique({
  where: { id: decoded.sub },
});
if (user.role !== 'admin') {
  throw new ForbiddenError();
}
```

### 5. Use Appropriate Algorithms

```typescript
// RECOMMENDED - RS256 or ES256
// Asymmetric algorithms

// RS256: RSA Signature with SHA-256
const token = jwt.sign(payload, privateKey, { algorithm: 'RS256' });

// ES256: ECDSA with P-256 and SHA-256
const token = jwt.sign(payload, privateKey, { algorithm: 'ES256' });
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

const jwt = require('jsonwebtoken');

function createToken(user) {
  return jwt.sign({
    id: user.id,
    role: user.role,
  }, 'secret'); // Weak secret, no algorithm
}

function verifyToken(token) {
  return jwt.verify(token, 'secret'); // No validation
}

AFTER (Fixed):
━━━━━━━━━━━━━━━━

const jwt = require('jsonwebtoken');
const crypto = require('crypto');

const JWT_SECRET = process.env.JWT_SECRET;
if (!JWT_SECRET) {
  throw new Error('JWT_SECRET not configured');
}

function createToken(user) {
  return jwt.sign(
    {
      sub: user.id,           // Use 'sub' for subject
      role: user.role,
      iat: Math.floor(Date.now() / 1000),
      exp: Math.floor(Date.now() / 1000) + 3600, // 1 hour
    },
    JWT_SECRET,
    { algorithm: 'HS256' }    // Explicit algorithm
  );
}

function verifyToken(token) {
  return jwt.verify(token, JWT_SECRET, {
    algorithms: ['HS256'],    // Whitelist algorithm
    issuer: 'my-app',         // Validate issuer
    audience: 'my-api',       // Validate audience
  });
}
```

---

## Related Vulnerabilities

- [Weak Authentication](./weak-authentication.md)
- [Broken Access Control](./broken-access-control.md)

---

*Document version: 1.0*
*Last updated: 2026-03-04*
