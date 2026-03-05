# Hardcoded Secrets

Hardcoded secrets are API keys, passwords, tokens, or private keys embedded directly in source code, making them easily discoverable by attackers.

---

## Severity

**CRITICAL** - Direct exposure of credentials can lead to complete system compromise, data breaches, and unauthorized access.

---

## How It Works

```
ATTACK FLOW:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Attacker finds secrets in:
   * Source code (git history, public repos)
   * Configuration files
   * Environment files (if committed)
   * Docker images

2. Attacker uses secrets to:
   * Access APIs
   * Access databases
   * Escalate privileges
   * Exfiltrate data
```

---

## Types of Hardcoded Secrets

### 1. API Keys

```typescript
// VULNERABLE
const STRIPE_API_KEY = 'sk_live_abc123def456';
const AWS_ACCESS_KEY = 'AKIAIOSFODNN7EXAMPLE';
const OPENAI_API_KEY = 'sk-abc123xyz789';
```

### 2. Passwords

```typescript
// VULNERABLE
const DB_PASSWORD = 'admin123';
const ADMIN_PASSWORD = 'super_secret_pass';
const SMTP_PASSWORD = 'smtp_password_2024';
```

### 3. Private Keys

```typescript
// VULNERABLE
const PRIVATE_KEY = `-----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAy...
-----END RSA PRIVATE KEY-----`;

const JWT_SECRET = 'my-super-secret-jwt-key';
```

### 4. Tokens

```typescript
// VULNERABLE
const GITHUB_TOKEN = 'ghp_abc123def456ghi789jkl012';
const SLACK_TOKEN = 'xoxb-1234567890123-1234567890123-abc123def456';
const FACEBOOK_ACCESS_TOKEN = 'EAACEdEose0cBA...';
```

### 5. Database Credentials

```typescript
// VULNERABLE
const DB_HOST = 'prod-db.example.com';
const DB_USER = 'admin';
const DB_PASS = 'P@ssw0rd!2024';
```

---

## Detection

### AST Patterns to Detect

```typescript
const secretPatterns = {
  patterns: [
    // API Key patterns
    {
      regex: /api[_-]?key['":\s=]+['"][a-zA-Z0-9_\-]{20,}['"]/gi,
      type: 'api_key',
    },
    // AWS keys
    {
      regex: /(AKIA|ABIA|ACCA|ASIA)[A-Z0-9]{16}/g,
      type: 'aws_key',
    },
    // Private keys
    {
      regex: /-----BEGIN (RSA|EC|DSA|OPENSSH) PRIVATE KEY-----/,
      type: 'private_key',
    },
    // JWT secrets
    {
      regex: /jwt[_-]?secret['":\s=]+['"][^'"]+['"]/gi,
      type: 'jwt_secret',
    },
    // Generic secrets
    {
      regex: /(password|passwd|pwd|secret|token)['":\s=]+['"][^'""]{8,}['"]/gi,
      type: 'secret',
    },
    // Bearer tokens
    {
      regex: /bearer\s+[a-zA-Z0-9_\-\.]{20,}/gi,
      type: 'bearer_token',
    },
  ],
};
```

### Git History Scanning

```typescript
// Scan git history for secrets
const gitHistoryScan = {
  command: 'git log -p -S "STRIPE_API_KEY" --all',

  // Or use git-secrets or gitleaks
  tools: [
    'gitleaks',
    'git-secrets',
    'trufflehog',
    'secret-scanner',
  ],
};
```

---

## Prevention

### 1. Environment Variables

```typescript
// GOOD - Use environment variables
const stripeKey = process.env.STRIPE_API_KEY;
const dbPassword = process.env.DB_PASSWORD;
const jwtSecret = process.env.JWT_SECRET;

// GOOD - With defaults for development
const apiUrl = process.env.API_URL || 'http://localhost:3000';
```

### 2. Secret Management Services

```typescript
// GOOD - Use secret managers
import { SecretManager } from '@aws-sdk/client-secrets-manager';

// AWS Secrets Manager
const client = new SecretManager({});
const secret = await client.getSecretValue({
  SecretId: 'my-app/prod/db',
});

// HashiCorp Vault
import vault from 'node-vault';
const client = vault({ endpoint: 'https://vault.example.com' });
const { data } = await client.read('secret/data/my-app');
```

### 3. Configuration Files

```typescript
// .env file (ADD TO .gitignore!)
STRIPE_API_KEY=sk_live_abc123
DB_PASSWORD=secretpassword

// .env.example (SAFE - no values)
STRIPE_API_KEY=
DB_PASSWORD=
```

### 4. .gitignore

```
# .gitignore
.env
.env.local
.env.*.local
*.pem
*.key
credentials.json
secrets.yaml
config/production.json
```

### 5. Pre-commit Hooks

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.0
    hooks:
      - id: gitleaks
```

---

## Fix Example

```
BEFORE (Vulnerable):
━━━━━━━━━━━━━━━━━━━━

// config.ts
export const config = {
  stripeKey: 'sk_live_abc123def456',
  awsSecret: 'wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY',
  jwtSecret: 'my-super-secret-key-12345',
};

AFTER (Fixed):
━━━━━━━━━━━━━━━━

// config.ts
export const config = {
  stripeKey: process.env.STRIPE_API_KEY,
  awsSecret: process.env.AWS_SECRET_ACCESS_KEY,
  jwtSecret: process.env.JWT_SECRET,
};

// .env (NOT committed)
STRIPE_API_KEY=sk_live_abc123def456
AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/...
JWT_SECRET=my-super-secret-key-12345
```

---

## Related Vulnerabilities

- [Sensitive Data Exposure](./sensitive-data-exposure.md)
- [Insecure Storage](./insecure-storage.md)

---

*Document version: 1.0*
*Last updated: 2026-03-04*
