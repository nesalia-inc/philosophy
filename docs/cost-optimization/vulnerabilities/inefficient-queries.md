# Inefficient Queries

Inefficient database queries waste compute resources and increase costs.

---

## Problem

```
IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* More compute = higher DB costs
* Longer queries = more connections used
* More I/O = higher storage costs
```

---

## Common Issues

### 1. SELECT *

```sql
-- BAD
SELECT * FROM users;

-- GOOD
SELECT id, name, email FROM users;
```

### 2. No Pagination

```sql
-- BAD
SELECT * FROM orders;

-- GOOD
SELECT * FROM orders LIMIT 100 OFFSET 0;
```

### 3. No WHERE Clause

```sql
-- BAD
SELECT * FROM logs WHERE 1=1;

-- GOOD
SELECT * FROM logs WHERE created_at > '2024-01-01';
```

---

## Prevention

### 1. Select Only Needed Columns

```typescript
// GOOD
const users = await db.select({
  id: users.id,
  name: users.name,
}).from(users);
```

### 2. Use Pagination

```typescript
// GOOD
const users = await db.query.users.findMany({
  take: 20,
  skip: page * 20,
});
```

### 3. Add Filters

```typescript
// GOOD
const recentUsers = await db.user.findMany({
  where: {
    createdAt: { gte: thirtyDaysAgo },
  },
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
