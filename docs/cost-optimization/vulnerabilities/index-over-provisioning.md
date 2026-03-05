# Index Over-provisioning

Too many indexes increase storage costs and slow down INSERT/UPDATE operations.

---

## Problem

```
COST IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Each index:
* Uses storage space
* Must be updated on INSERT/UPDATE/DELETE
* Increases write latency

Example:
* 1 million rows table
* 5 indexes
* Each index adds ~50MB
* Write operations 5x slower!
```

---

## Prevention

### 1. Audit Indexes Regularly

```sql
-- Check index usage
SELECT
  indexrelname,
  idx_scan,
  idx_tup_read,
  idx_tup_fetch
FROM pg_stat_user_indexes
WHERE idx_scan = 0;
```

### 2. Remove Unused Indexes

```sql
-- Drop unused index
DROP INDEX IF EXISTS idx_unused_column;
```

### 3. Use Composite Indexes

```sql
-- BAD - Separate indexes
CREATE INDEX idx_a ON table(a);
CREATE INDEX idx_b ON table(b);

-- GOOD - Composite index
CREATE INDEX idx_a_b ON table(a, b);
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
