# No Compression

Uncompressed data wastes storage and bandwidth.

---

## Problem

```
COMPRESSION SAVINGS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Text/JSON: 70-80% smaller
Images: 20-30% smaller
Archives: Already compressed

Example:
* 100GB uncompressed → $23/month
* 25GB compressed → $6/month
* Savings: $17/month!
```

---

## Prevention

### 1. Compress S3 Uploads

```typescript
// Compress before upload
import { gzip } from 'zlib';

async function uploadCompressed(key: string, data: string) {
  const compressed = gzip(Buffer.from(data));

  await s3.putObject({
    Bucket: 'my-bucket',
    Key: key + '.gz',
    Body: compressed,
    ContentEncoding: 'gzip',
  }).promise();
}
```

### 2. Use S3 Server-Side Compression

```typescript
// Enable S3 compression
await s3.putBucketEncryption({
  Bucket: 'my-bucket',
  ServerSideEncryptionConfiguration: {
    Rules: [
      {
        ApplyServerSideEncryptionByDefault: {
          SSEAlgorithm: 'AES256',
        },
      },
    ],
  },
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
