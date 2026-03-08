## Performance Optimization

<!-- One sentence explaining what is optimized -->

Example: Optimize database queries to reduce API latency by 80%

---

## Performance Goals

### Baseline (Before)
<!-- Current performance metrics -->
- API response time: 1,200ms
- Database query time: 950ms
- Memory usage: 512MB
- CPU usage: 65%

### Target (After)
<!-- Expected improvements -->
- API response time: < 200ms
- Database query time: < 100ms
- Memory usage: < 256MB
- CPU usage: < 40%

---

## Optimization Changes

### What was optimized?
<!-- List the optimizations -->
- Added database indexes on frequently queried columns
- Implemented query result caching
- Optimized N+1 queries with eager loading
- Added response compression

### Files Changed
```bash
src/api/users/index.ts       # Added caching
src/db/queries/users.ts      # Optimized queries
src/db/migrations/002_add_indexes.sql
src/lib/cache.ts            # New caching layer
```

---

## Technical Approach

### Before (Slow)
```typescript
// N+1 query problem
async function getUsersWithPosts(): Promise<User[]> {
  const users = await db.query('SELECT * FROM users')

  // N+1: One query per user!
  for (const user of users) {
    user.posts = await db.query(
      'SELECT * FROM posts WHERE user_id = ?',
      [user.id]
    )
  }

  return users
}
// Time: 1,200ms for 100 users
```

### After (Optimized)
```typescript
// Single query with JOIN
async function getUsersWithPosts(): Promise<Result<User[], DbError>> {
  const result = await db.query<Result<User[], DbError>>(`
    SELECT
      users.*,
      JSON_ARRAYAGG(
        JSON_OBJECT(
          'id', posts.id,
          'title', posts.title
        )
      ) as posts
    FROM users
    LEFT JOIN posts ON posts.user_id = users.id
    GROUP BY users.id
  `)

  return result
}
// Time: 95ms for 100 users (12.5x faster)
```

### Database Indexes Added
```sql
-- Speed up user lookups
CREATE INDEX idx_users_email ON users(email);

-- Speed up post queries
CREATE INDEX idx_posts_user_id ON posts(user_id);

-- Covering index for common queries
CREATE INDEX idx_users_status_created
  ON users(status, created_at);
```

---

## Benchmarks

### Methodology
<!-- How performance was measured -->
- Used Apache Bench for load testing
- Measured with 1000 concurrent requests
- Ran tests on identical hardware
- Tested with production-like dataset

### Benchmark Results

#### API Response Time
```
Before: 1,247ms (p95), 1,890ms (p99)
After:   187ms (p95),  245ms (p99)
Improvement: 85% faster
```

#### Database Queries
```
Before: 157 queries per request (N+1 problem)
After:    3 queries per request
Reduction: 98% fewer queries
```

#### Memory Usage
```
Before: 512MB baseline, 1.2GB peak
After:  256MB baseline, 380MB peak
Reduction: 50% less memory
```

#### Throughput
```
Before: 45 requests/second
After:  890 requests/second
Improvement: 19.7x more throughput
```

### Load Test Commands
```bash
# Benchmark endpoint
ab -n 10000 -c 100 https://api.example.com/users

# With authentication
ab -n 10000 -c 100 \
  -H "Authorization: Bearer $TOKEN" \
  https://api.example.com/users
```

---

## Testing

### Performance Tests
- [ ] Load tests pass
- [ ] No regressions in other endpoints
- [ ] Memory leaks tested
- [ ] Cache invalidation verified

### Correctness Tests
- [ ] All existing tests still pass
- [ ] Output is identical to before
- [ ] Edge cases work correctly
- [ ] Cache coherence verified

### Test Results
```bash
npm run test:performance
npm run test:load
npm run test:memory
```

---

## Monitoring

### Metrics to Track
<!-- What to watch after deploy -->
- p50/p95/p99 response times
- Database query times
- Cache hit rates
- Memory usage
- CPU usage

### Alerts
<!-- When to alert -->
- Response time > 500ms
- Cache hit rate < 80%
- Error rate > 1%

### Dashboards
<!-- Where to monitor -->
- Grafana dashboard: [Link]
- Datadog dashboard: [Link]

---

## Deployment

### Deployment Strategy
- [ ] **Canary deployment** (10% → 50% → 100%)
- [ ] **Blue-green deployment**
- [ ] **Rolling deployment**

### Rollback Plan
```bash
# If issues detected
kubectl rollback deployment/api
# Or
git revert <commit-hash>
```

### Verification
```bash
# Smoke test
curl https://api.example.com/health
curl https://api.example.com/users?limit=10

# Load test
npm run test:load:production
```

---

## Trade-offs

### What We Gained
- 85% faster response times
- 98% fewer database queries
- 50% less memory usage
- 20x higher throughput

### What We Lost
- Increased code complexity
- Additional cache invalidation logic
- More database indexes (slower writes)
- Increased memory for cache

### Mitigation
- Added extensive documentation
- Simplified cache API
- Monitor write performance
- Set cache size limits

---

## Documentation

- [ ] Performance guide updated
- [ ] Caching strategy documented
- [ ] Runbook for cache issues
- [ ] Architecture decisions record (ADR)

---

*Performance Gain: 85% faster, 20x throughput*
*Infrastructure Impact: +128MB for cache*
*Production Estimate: Save $450/month on servers*
