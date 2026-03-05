# Revenue Dashboard

Comprehensive revenue analytics and business metrics.

---

## Metrics

```
KEY METRICS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

MRR (Monthly Recurring Revenue):
* Current month's recurring revenue
* Excludes one-time payments

ARR (Annual Recurring Revenue):
* MRR × 12
* Standardized for annual reporting

Net Revenue Retention (NRR):
* Revenue from existing customers
* Includes upgrades, downgrades, churn
* NRR > 100% = growth from existing customers

Churn Rate:
* % of customers lost in period
* Logo churn vs Revenue churn
```

---

## Implementation

```typescript
import { RevenueDashboard } from '@nesalia/business';

const dashboard = new RevenueDashboard({
  currency: 'USD',
});

// Get comprehensive report
const report = await dashboard.getReport({
  from: '2024-01-01',
  to: '2024-03-04',
  groupBy: 'month',
});

console.log({
  // Revenue
  mrr: report.mrr.current,
  mrrGrowth: report.mrr.growth,
  arr: report.arr.current,
  arrGrowth: report.arr.growth,

  // Customers
  totalCustomers: report.customers.total,
  newCustomers: report.customers.new,
  churnedCustomers: report.customers.churned,
  churnRate: report.customers.churnRate,

  // Retention
  nrr: report.retention.nrr,
  ltv: report.retention.averageLTV,
  cac: report.retention.averageCAC,
  ltvCacRatio: report.retention.ltvCacRatio,

  // Revenue breakdown
  newMRR: report.breakdown.new,
  expansionMRR: report.breakdown.expansion,
  contractionMRR: report.breakdown.contraction,
  churnMRR: report.breakdown.churn,

  // By plan
  mrrByPlan: report.byPlan,
});
```

---

## Real-time Metrics

```typescript
// Real-time subscription updates
dashboard.on('subscription.created', (data) => {
  console.log('New subscription:', data);
  // Update dashboards, send notifications
});

dashboard.on('subscription.canceled', (data) => {
  console.log('Canceled:', data);
  // Trigger retention flow
});
```

---

## Chart Data

```typescript
// Get chart data for visualization
const chartData = await dashboard.getChart({
  metric: 'mrr',
  from: '2024-01-01',
  to: '2024-03-04',
  interval: 'day', // or 'week', 'month'
  breakdown: 'plan', // or 'cohort', 'source'
});

console.log(chartData);
// [
//   { date: '2024-01-01', value: 10000 },
//   { date: '2024-01-02', value: 10200 },
//   { date: '2024-01-03', value: 10500 },
//   ...
// ]
```

---

## Export

```typescript
// Export to CSV/Excel
const csv = await dashboard.export({
  format: 'csv',
  metrics: ['mrr', 'arr', 'customers', 'churn'],
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log(csv); // CSV string
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
