# Data Duplication

Duplicate data wastes storage and increases costs.

---

## Problem

```
COST IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Same data stored multiple times:
* In multiple databases
* In multiple S3 buckets
* As backups

Savings:
* Dedup = 20-40% storage savings
```

---

## Prevention

### 1. Use Deduplication

```typescript
// Use content-addressable storage
import crypto from 'crypto';

function getHash(content: Buffer): string {
  return crypto.createHash('sha256').update(content).digest('hex');
}

async function uploadFile(content: Buffer) {
  const hash = getHash(content);

  // Check if exists
  const existing = await s3.headObject({
    Bucket: 'dedup-bucket',
    Key: hash,
  }).catch(() => null);

  if (existing) {
    return { hash, alreadyStored: true };
  }

  // Upload with hash as key
  await s3.putObject({
    Bucket: 'dedup-bucket',
    Key: hash,
    Body: content,
  });

  return { hash, alreadyStored: false };
}
```

### 2. Single Source of Truth

```typescript
// One database, not multiple
// Share data via API, not copies
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
