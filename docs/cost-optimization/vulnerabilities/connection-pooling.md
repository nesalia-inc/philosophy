# Connection Pooling

Improper connection pooling wastes database connections and increases costs.

---

## Problem

```
COST IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Each connection uses memory
* Connection limits are expensive
* Unused connections = wasted resources
```

---

## Prevention

### 1. Right-size Pool

```typescript
// GOOD - Appropriate pool size
const pool = new Pool({
  max: 20, // Match your needs
  idleTimeoutMillis: 30000,
});
```

### 2. Use Connection Pooler

```typescript
// GOOD - Use PgBouncer or similar
const pool = new Pool({
  host: 'pgbouncer',
  port: 6432,
  max: 10,
});
```

### 3. Monitor Usage

```typescript
// Monitor pool metrics
pool.on('connect', () => console.log('New connection'));
pool.on('error', (err) => console.error('Pool error', err));
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
