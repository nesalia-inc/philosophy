# Cache Hit Rate

Low cache hit rate means cache infrastructure is wasted.

---

## Problem

```
COST IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Hit Rate < 50%:
* Cache infrastructure = wasted
* Better to use database directly!

Target:
* > 90% hit rate for cache to be worth it
```

---

## Prevention

### 1. Monitor Hit Rate

```typescript
// Track hit rate
const cache = new Redis({ url: process.env.REDIS_URL });

async function getCached(key: string) {
  const start = Date.now();
  const value = await cache.get(key);
  const duration = Date.now() - start;

  metrics.record('cache.get', {
    key,
    hit: value !== null,
    duration,
  });

  return value;
}
```

### 2. Optimize Cache Keys

```typescript
// GOOD - Specific keys
const userKey = `user:${userId}`;
const userPostsKey = `user:${userId}:posts`;

// GOOD - Include version
const userKeyV2 = `user:${userId}:v2`;
```

### 3. Warm Cache

```typescript
// Warm cache on startup
async function warmCache() {
  const popularUsers = await db.user.findMany({
    take: 100,
    orderBy: { views: 'desc' },
  });

  for (const user of popularUsers) {
    await cache.set(`user:${user.id}`, user);
  }
}
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
