# Missing Indexes

Missing database indexes cause slow queries, especially on large tables.

---

## Severity

**HIGH** - Can slow down queries from milliseconds to seconds.

---

## Common Issues

### 1. No Index on Foreign Key

```sql
-- VULNERABLE - No index on foreign key
SELECT * FROM orders WHERE user_id = 123;

-- user_id has no index!
-- Full table scan on every query
```

### 2. No Index on WHERE Clause

```sql
-- VULNERABLE
SELECT * FROM users WHERE email = 'john@example.com';

-- email has no index!
-- Must scan entire table
```

### 3. No Composite Index

```sql
-- VULNERABLE
SELECT * FROM orders WHERE status = 'pending' AND created_at > '2024-01-01';

-- No composite index on (status, created_at)!
-- Must scan large portion of table
```

---

## Prevention

### 1. Index Foreign Keys

```sql
-- GOOD
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

### 2. Index WHERE Clauses

```sql
-- GOOD
CREATE INDEX idx_users_email ON users(email);
```

### 3. Create Composite Indexes

```sql
-- GOOD - For queries with multiple conditions
CREATE INDEX idx_orders_status_created
ON orders(status, created_at DESC);
```

### 4. Use EXPLAIN

```sql
-- Analyze query
EXPLAIN SELECT * FROM orders WHERE user_id = 123;

-- If "Seq Scan" = no index used!
-- If "Index Scan" = index used!
```

---

## Fix Example

```
BEFORE (Slow):
━━━━━━━━━━━━━━━━

-- Query takes 2 seconds
SELECT * FROM orders
WHERE user_id = 123
AND status = 'pending'
ORDER BY created_at DESC;

-- No indexes!

AFTER (Fast):
━━━━━━━━━━━━━━━━

-- Create indexes
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_created ON orders(created_at DESC);

-- Create composite index
CREATE INDEX idx_orders_user_status_created
ON orders(user_id, status, created_at DESC);

-- Query now takes 10ms!
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
