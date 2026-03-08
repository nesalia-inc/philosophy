---
name: Performance Optimization Pull Request
about: Template for performance improvements
title: 'perf: '
labels: performance
assignees: ''
---

<!-- One sentence explaining what is optimized -->
## Performance Optimization



---

## Performance Goals

### Baseline (Before)
<!-- Current performance metrics -->
- API response time: [ms]
- Database query time: [ms]
- Memory usage: [MB]
- CPU usage: [%]

### Target (After)
<!-- Expected improvements -->
- API response time: < [ms]
- Database query time: < [ms]
- Memory usage: < [MB]
- CPU usage: < [%]

---

## What was optimized?
<!-- List the optimizations -->


---

## Files Changed
```bash
# List files
```

---

## Technical Approach

### Before (Slow)
```typescript
// Show slow code
```

### After (Optimized)
```typescript
// Show optimized code
```

---

## Benchmarks

### Methodology
<!-- How performance was measured -->


### Benchmark Results

#### API Response Time
```
Before: [time]
After:  [time]
Improvement: [X]% faster
```

#### Database Queries
```
Before: [queries] per request
After:  [queries] per request
Reduction: [X]% fewer queries
```

#### Memory Usage
```
Before: [memory]
After:  [memory]
Reduction: [X]% less memory
```

### Load Test Commands
```bash
# Commands used
```

---

## Testing

### Performance Tests
- [ ] Load tests pass
- [ ] No regressions in other endpoints
- [ ] Memory leaks tested

### Correctness Tests
- [ ] All existing tests still pass
- [ ] Output is identical to before
- [ ] Edge cases work correctly

---

## Monitoring

### Metrics to Track
<!-- What to watch after deploy -->
- p50/p95/p99 response times
- Database query times
- Cache hit rates
- Memory usage

### Alerts
<!-- When to alert -->


### Dashboards
<!-- Where to monitor -->
- [Link to dashboard]

---

## Deployment

### Deployment Strategy
- [ ] **Canary deployment** (10% → 50% → 100%)
- [ ] **Blue-green deployment**
- [ ] **Rolling deployment**

### Rollback Plan
```bash
# If issues detected
```

---

## Trade-offs

### What We Gained


### What We Lost


### Mitigation


---

## Documentation
- [ ] Performance guide updated
- [ ] Caching strategy documented
- [ ] Runbook created

---

*Performance Gain: [X]% faster*
*Infrastructure Impact: [cost]*
