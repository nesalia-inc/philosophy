# Cache Stampede

Multiple simultaneous cache misses cause thundering herd problem.

---

## Problem

```
PROBLEM:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. Cache expires
2. 1000 requests all miss cache
3. All hit database simultaneously
4. Database overwhelmed!
```

---

## Prevention

### 1. Use Distributed Lock

```typescript
// GOOD - Single refresh
const lockKey = `lock:${cacheKey}`;

const lock = await redis.set(lockKey, '1', 'NX', 'EX', 10);

if (lock) {
  // Refresh cache
  const data = await db.fetchData();
  await redis.setex(cacheKey, 3600, JSON.stringify(data));
  await redis.del(lockKey);
} else {
  // Wait for other process
  await new Promise(r => setTimeout(r, 100));
  return JSON.parse(await redis.get(cacheKey));
}
```

### 2. Use Cache-Aside with Stale-While-Revalidate

```typescript
// GOOD - Serve stale while refreshing
const data = await redis.get(cacheKey);

if (!data) {
  const fresh = await db.fetchData();
  await redis.setex(cacheKey, 3600, JSON.stringify(fresh));
  return fresh;
}

const cached = JSON.parse(data);

// Background refresh if stale
if (Date.now() > cached.expiresAt - 60000) {
  backgroundRefresh(cacheKey);
}

return cached.data;
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
