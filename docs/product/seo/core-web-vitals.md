# Core Web Vitals Monitor

Monitor and track Core Web Vitals metrics for user experience.

---

## Core Web Vitals

```
METRICS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

LCP (Largest Contentful Paint):
* Measures loading performance
* < 2.5 seconds = Good
* > 4.0 seconds = Poor

FID (First Input Delay):
* Measures interactivity
* < 100 ms = Good
* > 300 ms = Poor

CLS (Cumulative Layout Shift):
* Measures visual stability
* < 0.1 = Good
* > 0.25 = Poor
```

---

## Implementation

```typescript
import { CoreWebVitals, createReport } from '@nesalia/seo';

// Client-side collection
const collector = new CoreWebVitals({
  endpoint: '/api/vitals',
});

// Report CLS (on layout shift)
new PerformanceObserver((list) => {
  for (const entry of list.getEntries()) {
    collector.report('cls', entry.value);
  }
}).observe({ type: 'layout-shift', buffered: true });

// Report LCP
new PerformanceObserver((list) => {
  const entries = list.getEntries();
  const lastEntry = entries[entries.length - 1];
  collector.report('lcp', lastEntry.value);
}).observe({ type: 'largest-contentful-paint', buffered: true });

// Report FID (on first input)
new PerformanceObserver((list) => {
  const entry = list.getEntries()[0];
  collector.report('fid', entry.processingStart - entry.startTime);
}).observe({ type: 'first-input', buffered: true });
```

---

## Server-side Aggregation

```typescript
import { aggregateVitals, getVitalsReport } from '@nesalia/seo';

const vitals = new CoreWebVitalsDB();

// Aggregate incoming vitals
await vitals.aggregate();

// Get report
const report = await vitals.getReport({
  from: '2024-01-01',
  to: '2024-03-04',
  url: '/checkout',
});

console.log({
  lcp: {
    good: report.lcp.good,
    needsImprovement: report.lcp.needsImprovement,
    poor: report.lcp.poor,
    p75: report.lcp.p75,
    p90: report.lcp.p90,
  },
  fid: { /* ... */ },
  cls: { /* ... */ },
});
```

---

## Alerting

```typescript
// Set up alerts
await vitals.setAlert({
  metric: 'lcp',
  threshold: 2500,
  condition: 'greaterThan',
  channel: 'email',
  recipients: ['team@example.com'],
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
