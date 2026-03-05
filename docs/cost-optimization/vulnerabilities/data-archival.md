# Data Archival

Old data on expensive storage increases database costs unnecessarily.

---

## Problem

```
COST IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Enterprise DB: ~$500/month per TB
* S3: ~$23/month per TB
* Archive = 95% savings!
```

---

## Prevention

### 1. Archive Old Data

```sql
-- Move old data to archive table
CREATE TABLE orders_archive (LIKE orders);

INSERT INTO orders_archive
SELECT * FROM orders
WHERE created_at < '2023-01-01';

DELETE FROM orders
WHERE created_at < '2023-01-01';
```

### 2. Use Partitioning

```sql
-- Partition by date
CREATE TABLE orders (
  id BIGSERIAL,
  created_at TIMESTAMP
) PARTITION BY RANGE (created_at);

CREATE TABLE orders_2024 PARTITION OF orders
  FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');
```

### 3. Implement TTL

```typescript
// Archive data after 1 year
await db.archiveOldData({
  table: 'orders',
  archiveTable: 'orders_archive',
  olderThan: '1 year',
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
