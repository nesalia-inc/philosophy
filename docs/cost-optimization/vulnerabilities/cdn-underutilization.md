# CDN Underutilization

Static files served from origin instead of CDN waste bandwidth.

---

## Problem

```
COSTS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

S3: $0.09/GB (bandwidth)
CloudFront: $0.08/GB (first 10TB)

Plus CloudFront:
* 400+ edge locations
* Free SSL
* DDoS protection

Savings: Up to 60% on bandwidth!
```

---

## Prevention

### 1. Use CloudFront

```typescript
// Configure CloudFront
import { CloudFrontClient, CreateDistributionCommand } from '@aws-sdk/client-cloudfront';

await cloudFrontClient.send(new CreateDistributionCommand({
  CallerReference: 'my-distribution',
  DefaultRootObject: 'index.html',
  Origins: {
    Items: [{
      Id: 'S3-origin',
      DomainName: 'my-bucket.s3.amazonaws.com',
      S3OriginConfig: { OriginAccessIdentity: '' },
    }],
  },
}));
```

### 2. Set Cache Headers

```typescript
// Long cache for static assets
app.use('/static', (req, res, next) => {
  res.set('Cache-Control', 'public, max-age=31536000, immutable');
  next();
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
