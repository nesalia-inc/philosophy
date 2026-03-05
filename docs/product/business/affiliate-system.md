# Affiliate System

Complete affiliate tracking, commissions, and payouts management.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Affiliate signup and management
* Custom referral links
* Commission tracking
* Multi-tier commissions
* Automatic payouts
* Performance analytics
```

---

## Implementation

```typescript
import { AffiliateSystem } from '@nesalia/business';

const affiliate = new AffiliateSystem({
  currency: 'USD',
  payoutSchedule: 'monthly',
  minPayout: 50,
});

// Create affiliate program
const program = await affiliate.createProgram({
  name: 'Premium Partner Program',
  tiers: [
    { name: 'Bronze', commission: 10, minSales: 0 },
    { name: 'Silver', commission: 15, minSales: 1000 },
    { name: 'Gold', commission: 20, minSales: 5000 },
  ],
  cookieDuration: 30, // days
});

// Sign up affiliate
const affiliateUser = await affiliate.signup({
  email: 'affiliate@example.com',
  name: 'John Doe',
  website: 'https://affiliate-site.com',
});

console.log(affiliateUser.referralCode); // ABC123
console.log(affiliateUser.referralLink); // https://example.com?ref=ABC123
```

---

## Tracking & Commissions

```typescript
// Track referral (called on purchase)
await affiliate.trackReferral({
  referralCode: 'ABC123',
  orderId: 'order-123',
  customerId: 'cust-456',
  amount: 100, // $100 order
  products: ['product-1'],
});

// Get affiliate earnings
const earnings = await affiliate.getEarnings({
  affiliateId: affiliateUser.id,
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log({
  totalEarnings: earnings.total,
  pending: earnings.pending,
  paid: earnings.paid,
  commissionRate: earnings.commissionRate,
  tier: earnings.tier,
});
```

---

## Payouts

```typescript
// Request payout
const payout = await affiliate.requestPayout({
  affiliateId: affiliateUser.id,
  method: 'bank_transfer', // or 'paypal'
});

// Process payouts (cron job)
await affiliate.processPayouts({
  batchSize: 100,
});

// Get payout history
const payouts = await affiliate.getPayoutHistory({
  affiliateId: affiliateUser.id,
});

console.log(payouts);
// [
//   { amount: 500, date: '2024-02-01', status: 'paid' },
//   { amount: 350, date: '2024-03-01', status: 'paid' },
// ]
```

---

## Dashboard

```typescript
// Affiliate dashboard data
const dashboard = await affiliate.getDashboard({
  affiliateId: affiliateUser.id,
});

console.log({
  clicks: dashboard.clicks,
  conversions: dashboard.conversions,
  conversionRate: dashboard.conversionRate,
  earnings: dashboard.earnings,
  topProducts: dashboard.topProducts,
  trends: dashboard.trends,
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
