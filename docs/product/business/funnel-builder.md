# Funnel Builder

Multi-step checkout and sales funnel management.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Visual funnel builder
* Multi-step checkout flows
* Abandonment tracking
* Upsell/cross-sell
* A/B testing per step
* Custom fields
```

---

## Implementation

```typescript
import { Funnel, createCheckout } from '@nesalia/business';

const funnel = new Funnel('checkout', {
  steps: [
    {
      id: 'contact',
      type: 'form',
      title: 'Your Information',
      fields: [
        { name: 'email', type: 'email', required: true },
        { name: 'name', type: 'text', required: true },
      ],
    },
    {
      id: 'shipping',
      type: 'form',
      title: 'Shipping Address',
      fields: [...],
    },
    {
      id: 'payment',
      type: 'payment',
      provider: 'stripe',
    },
    {
      id: 'upsell',
      type: 'offer',
      title: 'Add extras?',
      products: ['extra-warranty', 'support-plan'],
    },
    {
      id: 'confirmation',
      type: 'confirmation',
    },
  ],
  settings: {
    allowGuestCheckout: true,
    saveAbandoned: true,
    sendEmails: true,
  },
});

// Create checkout session
const session = await funnel.createSession({
  products: [{ id: 'product-1', quantity: 1 }],
  customer: { email: 'customer@example.com' },
});

console.log(session.url); // Checkout URL
```

---

## Funnel Analytics

```typescript
const analytics = await funnel.getAnalytics({
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log({
  totalStarted: analytics.totalStarted,
  totalCompleted: analytics.totalCompleted,
  completionRate: analytics.completionRate,
  conversionRate: analytics.conversionRate,

  stepAnalytics: [
    { step: 'contact', entered: 1000, completed: 800, dropOff: 20% },
    { step: 'shipping', entered: 800, completed: 700, dropOff: 12.5% },
    { step: 'payment', entered: 700, completed: 350, dropOff: 50% },
    // ...
  ],

  avgTimeToComplete: '4m 32s',
  abandonmentPoints: analytics.abandonmentPoints,
});
```

---

## Abandonment Recovery

```typescript
// Track abandoned checkouts
const abandoned = await funnel.getAbandoned({
  from: '2024-01-01',
  to: '2024-03-04',
});

// Send recovery email
await funnel.sendRecoveryEmail(abandoned[0].id, {
  template: 'abandoned-checkout',
  delay: '1h', // Send after 1 hour
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
