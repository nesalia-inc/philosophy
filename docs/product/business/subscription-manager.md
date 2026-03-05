# Subscription Manager

Manage subscriptions, plans, trials, and recurring billing.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Multiple subscription plans
* Trial periods
* Plan upgrades/downgrades
* Pause/resume subscriptions
* Cancellation handling
* Dunning management
```

---

## Implementation

```typescript
import { SubscriptionManager } from '@nesalia/business';

const subscriptions = new SubscriptionManager({
  provider: 'stripe',
});

// Create plans
await subscriptions.createPlan({
  name: 'Pro',
  interval: 'month',
  amount: 29,
  currency: 'USD',
  features: ['unlimited_users', 'priority_support'],
});

await subscriptions.createPlan({
  name: 'Enterprise',
  interval: 'month',
  amount: 99,
  currency: 'USD',
  features: ['unlimited_everything', 'dedicated_support', 'sla'],
});

// Create subscription with trial
const subscription = await subscriptions.create({
  customerId: 'cust_123',
  planId: 'plan_pro',
  trialDays: 14,
});

console.log({
  id: subscription.id,
  status: subscription.status, // 'active', 'trialing', 'past_due', 'canceled'
  currentPeriodEnd: subscription.currentPeriodEnd,
  trialEnd: subscription.trialEnd,
});
```

---

## Subscription Operations

```typescript
// Upgrade subscription
await subscriptions.upgrade('sub_123', 'plan_enterprise');

// Downgrade (at period end)
await subscriptions.downgrade('sub_123', 'plan_basic');

// Pause subscription
await subscriptions.pause('sub_123', { months: 1 });

// Resume subscription
await subscriptions.resume('sub_123');

// Cancel at period end
await subscriptions.cancel('sub_123', {
  immediately: false,
  reason: 'too_expensive',
  feedback: 'Need more features',
});

// Cancel immediately
await subscriptions.cancel('sub_123', { immediately: true });
```

---

## Webhooks

```typescript
// Handle webhooks
await subscriptions.handleWebhook({
  event: 'customer.subscription.updated',
  data: {
    subscription: {
      id: 'sub_123',
      status: 'past_due',
      currentPeriodEnd: '2024-04-01',
    },
  },
});

// Types of events:
// - subscription.created
// - subscription.updated
// - subscription.deleted
// - trial.will_end
// - payment.succeeded
// - payment.failed
```

---

## Analytics

```typescript
const analytics = await subscriptions.getAnalytics({
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log({
  mrr: analytics.mrr, // Monthly Recurring Revenue
  arr: analytics.arr, // Annual Recurring Revenue

  churn: {
    rate: analytics.churnRate,
    count: analytics.churnedCustomers,
    reasons: analytics.churnReasons,
  },

  growth: {
    newSubscriptions: analytics.newSubscriptions,
    netGrowth: analytics.netGrowth,
    expansion: analytics.expansionRevenue, // upgrades
    contraction: analytics.contractionRevenue, // downgrades
  },

  ltv: {
    average: analytics.averageLTV,
    byPlan: analytics.ltvByPlan,
  },
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
