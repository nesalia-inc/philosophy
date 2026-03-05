# Small Files

Too many small S3 files increase API costs.

---

## Problem

```
COST IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

S3 API Costs:
* PUT/COPY/POST: $0.005/1000 requests
* GET/SELECT: $0.0004/1000 requests

100,000 small files:
* GET requests = $40/month just in API calls!

Solution: Combine small files into archives
```

---

## Prevention

### 1. Combine Small Files

```typescript
// Combine logs into daily archives
import { Zip } from 'zip-lib';

async function archiveLogs(date: string) {
  const files = await s3.listObjects({
    Bucket: 'logs',
    Prefix: `daily/${date}/`,
  }).promise();

  const contents = await Promise.all(
    files.Contents.map(f => s3.getObject({
      Bucket: 'logs',
      Key: f.Key,
    }).promise())
  );

  const zip = new Zip();
  contents.forEach((c, i) => {
    zip.addFile(files.Contents[i].Key, c.Body);
  });

  const archive = await zip.toBuffer();

  await s3.putObject({
    Bucket: 'logs-archive',
    Key: `archive/${date}.zip`,
    Body: archive,
  }).promise();
}
```

### 2. Use S3 Batch Operations

```typescript
// Delete old small files in batch
const operation = {
  Operation: 'Delete',
  Key: fileKeys,
};
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
