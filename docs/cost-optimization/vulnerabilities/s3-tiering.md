# S3 Tiering

Files on wrong storage class waste money.

---

## Problem

```
S3 COSTS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Standard:      $0.023/GB/month
Standard-IA:   $0.012/GB/month  (50% savings!)
Glacier:       $0.004/GB/month  (80% savings!)

Files not accessed in 30 days? Use Standard-IA!
Files not accessed in 180 days? Use Glacier!
```

---

## Prevention

### 1. Use Lifecycle Rules

```typescript
// GOOD - Lifecycle configuration
import { S3Client, putBucketLifecycleConfigurationCommand } from '@aws-sdk/client-s3';

const lifecycleRules = [
  {
    ID: 'MoveToIA',
    Status: 'Enabled',
    Filter: { Prefix: '' },
    Transitions: [
      {
        Days: 30,
        StorageClass: 'STANDARD_IA',
      },
      {
        Days: 180,
        StorageClass: 'GLACIER',
      },
    ],
  },
];

await s3Client.send(new PutBucketLifecycleConfigurationCommand({
  Bucket: 'my-bucket',
  LifecycleConfiguration: { Rules: lifecycleRules },
}));
```

### 2. Use Intelligent-Tiering

```typescript
// GOOD - Auto-tiering
const rule = {
  ID: 'IntelligentTiering',
  Status: 'Enabled',
  Filter: { Prefix: '' },
  Transitions: [
    {
      Days: 90,
      StorageClass: 'INTELLIGENT_TIERING',
    },
  ],
};
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
