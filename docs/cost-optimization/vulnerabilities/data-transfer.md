# Data Transfer

Large data transfers without compression waste bandwidth.

---

## Problem

```
COSTS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

AWS Data Transfer:
* Out to Internet: $0.09/GB
* Out to CloudFront: $0.02/GB

Example:
* 1TB uncompressed/month = $90
* 300GB compressed/month = $27
* Savings: $63/month!
```

---

## Prevention

### 1. Enable Compression

```typescript
// Use gzip/brotli
app.use(compression());

// Response headers
res.set('Content-Encoding', 'gzip');
```

### 2. Optimize Payloads

```typescript
// GOOD - Send only needed fields
const user = await db.user.findUnique({
  where: { id },
  select: { id: true, name: true }, // Not full object!
});
```

### 3. Use Pagination

```typescript
// GOOD - Paginate large lists
const items = await db.item.findMany({
  take: 100,
  skip: page * 100,
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
