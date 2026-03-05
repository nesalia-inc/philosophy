# Caching Issues

Improper caching strategies lead to stale data, excessive network requests, or unnecessary computations.

---

## Severity

**HIGH** - Affects both performance and data consistency.

---

## Common Issues

### 1. No Cache Headers

```typescript
// VULNERABLE - No caching
app.get('/api/users', (req, res) => {
  const users = db.users.findMany();
  res.json(users); // Every request hits database!
});
```

### 2. Aggressive Caching Without Invalidation

```typescript
// VULNERABLE - Cached forever
app.get('/api/user/:id', (req, res) => {
  const user = db.users.findUnique({ where: { id: req.params.id }});
  res.set('Cache-Control', 'public, max-age=31536000');
  // User updates, but cache never invalidates!
});
```

### 3. Client Cache Not Invalidated

```typescript
// VULNERABLE
const { data } = useQuery(['user', id], () => fetchUser(id));
// After update, query still returns old data!
```

---

## Prevention

### 1. Proper Cache Headers

```typescript
// GOOD - Cache with revalidation
app.get('/api/users', (req, res) => {
  const users = db.users.findMany();

  res.set('Cache-Control', 'public, max-age=60, stale-while-revalidate=300');
  res.json(users);
});

// GOOD - No cache for sensitive data
app.get('/api/profile', (req, res) => {
  const profile = getProfile(req.user.id);

  res.set('Cache-Control', 'no-store');
  res.json(profile);
});
```

### 2. Use Cache Tags

```typescript
// GOOD - Cache with tags
app.get('/api/users', async (req, res) => {
  const users = await db.users.findMany();

  // Tag this response
  res.set('Cache-Tag', 'users');

  res.json(users);
});

// Invalidation endpoint
app.post('/api/admin/invalidate', async (req, res) => {
  const { tag } = req.body;
  await invalidateCacheByTag(tag); // Vercel/Cloudflare style
  res.json({ success: true });
});
```

### 3. Client Cache Invalidation

```typescript
// GOOD - Invalidate after mutation
const queryClient = new QueryClient();

async function updateUser(id, data) {
  await api.updateUser(id, data);

  // Invalidate related queries
  queryClient.invalidateQueries(['user', id]);
  queryClient.invalidateQueries(['users']);
}
```

### 4. Stale-While-Revalidate

```typescript
// GOOD - Serve stale while revalidating
app.get('/api/posts', async (req, res) => {
  const cached = await redis.get('posts');

  if (cached) {
    res.set('Cache-Control', 'public, max-age=60, stale-while-revalidate=300');
    return res.json(JSON.parse(cached));
  }

  const posts = await db.posts.findMany();
  await redis.set('posts', JSON.stringify(posts), 'EX', 60);

  res.json(posts);
});
```

---

## Fix Example

```
BEFORE (No caching):
━━━━━━━━━━━━━━━━━━━━

app.get('/api/users', async (req, res) => {
  const users = await db.users.findMany();
  res.json(users);
});

AFTER (Proper caching):
━━━━━━━━━━━━━━━━━━━━━━

// GET /api/users - Cache for 1 minute
app.get('/api/users', async (req, res) => {
  const users = await db.users.findMany();

  res.set('Cache-Control', 'public, max-age=60, stale-while-revalidate=300');
  res.set('Cache-Tag', 'users');
  res.json(users);
});

// POST /api/users - Invalidate cache
app.post('/api/users', async (req, res) => {
  await createUser(req.body);

  await redis.del('users'); // Invalidate
  res.json({ success: true });
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
