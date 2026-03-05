# Cache Memory

Over-provisioned cache memory wastes resources.

---

## Problem

```
COST IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Redis: $0.20/GB/month
* More memory = more $$
* Monitor actual usage
```

---

## Prevention

### 1. Set Max Memory

```typescript
// GOOD - Limit Redis memory
const redis = new Redis({
  maxmemory: '256mb',
  maxmemory_policy: 'allkeys-lru',
});
```

### 2. Use Appropriate Eviction Policy

```typescript
// LRU - Least Recently Used
// Best for: General purpose

// ALLKEYS-LRU - Best for: Unknown access patterns
// NOEVICTION - Best for: Data that must not be lost

const redis = new Redis({
  maxmemory_policy: 'allkeys-lru',
});
```

### 3. Monitor Memory Usage

```typescript
// Monitor Redis memory
const info = await redis.info('memory');
console.log(`Used: ${info.used_memory_human}`);
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
