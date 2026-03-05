# Over-provisioned Servers

Servers with unused capacity waste money.

---

## Problem

```
COST EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Server: 8 vCPU, 32GB RAM = $200/month

Actual usage:
* CPU: 10% average
* RAM: 20% average

Solution:
* 2 vCPU, 8GB RAM = $50/month
* Savings: $150/month (75%!)
```

---

## Prevention

### 1. Right-size Instances

```typescript
// Use monitoring to right-size
// CloudWatch, Datadog, etc.

// Scale down when underutilized:
// CPU < 30% for 7 days
// RAM < 50% for 7 days
```

### 2. Use Spot Instances

```typescript
// Spot instances = 60-90% savings
const instance = await ec2.requestSpotInstances({
  InstanceType: 't3.medium',
  SpotPrice: '0.02',
  // For non-critical workloads
});
```

### 3. Monitor and Alert

```typescript
// Alert when over-provisioned
if (avgCPU < 30 && avgRAM < 50) {
  sendAlert('Consider downsizing');
}
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
