# Unoptimized API Calls

Excessive API calls waste bandwidth and increase latency.

---

## Problem

```
COSTS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Each API call = bandwidth + latency
* 1000 calls = $X in bandwidth
* Better to batch or cache

Example:
* Fetch 100 users one-by-one = 100 requests
* Fetch users in batch = 1 request
```

---

## Prevention

### 1. Batch Requests

```typescript
// BAD
for (const id of userIds) {
  await api.getUser(id);
}

// GOOD
const users = await api.getUsers(userIds);
```

### 2. Use GraphQL

```typescript
// GOOD - Single request
const query = `
  query {
    users {
      id
      name
      posts { title }
    }
  }
`;
```

### 3. Cache Responses

```typescript
// Cache API responses
const cached = await cache.get(`api:${hash}`);
if (cached) return cached;

const response = await fetch(apiUrl);
await cache.set(`api:${hash}`, response);
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
