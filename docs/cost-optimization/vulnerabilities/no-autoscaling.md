# No Auto-scaling

Always-on servers waste money during low traffic.

---

## Problem

```
COST EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Always-on: 2x t3.medium = $100/month

With auto-scaling:
* Peak (8am-8pm): 2 servers
* Off-peak (8pm-8am): 1 server

Savings: 25-50%!
```

---

## Prevention

### 1. Configure Auto-scaling

```typescript
// AWS Auto Scaling
import { AutoScalingClient, PutScalingPolicyCommand } from '@aws-sdk/client-auto-scaling';

await autoScalingClient.send(new PutScalingPolicyCommand({
  AutoScalingGroupName: 'my-asg',
  PolicyType: 'TargetTrackingScaling',
  TargetTrackingConfiguration: {
    PredefinedMetricSpecification: {
      PredefinedMetricType: 'ASGAverageCPUUtilization',
    },
    TargetValue: 70,
  },
}));
```

### 2. Set Schedule

```typescript
// Scale down at night
{
  Schedule: {
    ScheduledActions: [{
      Time: '0 20 * * *', // 8 PM UTC
      MinSize: 1,
      MaxSize: 1,
    }],
  },
}
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
