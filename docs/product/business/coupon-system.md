# Coupon System

Promo codes, discount codes, and coupon management.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Promo code creation and management
* Percentage and fixed discounts
* Usage limits (total, per user)
* Auto-apply rules
* Expiration dates
* First-purchase only codes
* Stackable discounts
* Campaign tracking
```

---

## Implementation

```typescript
import { CouponSystem } from '@nesalia/business';

const coupons = new CouponSystem();

// Create a promo code
const promo = await coupons.create({
  code: 'SUMMER2024',
  discountType: 'percentage',
  discount: 20, // 20% off
  maxUses: 1000,
  maxUsesPerUser: 1,
  minPurchase: 50, // Minimum order amount
  expiresAt: '2024-08-31',
  firstPurchaseOnly: false,
});

console.log(promo);
// { id: 'promo_123', code: 'SUMMER2024', active: true }
```

---

## Validation & Application

```typescript
// Validate a coupon
const validation = await coupons.validate({
  code: 'SUMMER2024',
  userId: 'user_123',
  cartTotal: 100,
});

console.log({
  valid: validation.valid,
  discount: validation.discount,
  message: validation.message, // Error message if invalid
});

// Apply to order
const applied = await coupons.apply({
  code: 'SUMMER2024',
  orderId: 'order_456',
  userId: 'user_123',
  items: [
    { productId: 'prod_1', price: 50, quantity: 2 },
  ],
});

console.log(applied);
// {
//   originalTotal: 100,
//   discount: 20,
//   finalTotal: 80,
//   appliedAt: '2024-06-15T10:30:00Z'
// }
```

---

## Campaign Management

```typescript
// Create campaign with multiple codes
const campaign = await coupons.createCampaign({
  name: 'Summer Sale 2024',
  description: 'End of summer promotion',
  codes: [
    { code: 'SUMMER20', discount: 20, discountType: 'percentage' },
    { code: 'SUMMER30', discount: 30, discountType: 'percentage', minPurchase: 200 },
    { code: 'FLAT50', discount: 50, discountType: 'fixed' },
  ],
  startsAt: '2024-06-01',
  endsAt: '2024-08-31',
});

// Auto-apply best discount
const bestDiscount = await coupons.getBestDiscount({
  userId: 'user_123',
  cartTotal: 150,
  applicableCodes: ['SUMMER20', 'SUMMER30', 'FLAT50'],
});

console.log(bestDiscount);
// { code: 'SUMMER30', discount: 45, savings: 15 }
```

---

## Analytics

```typescript
const analytics = await coupons.getAnalytics({
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log({
  totalCodes: analytics.totalCodes,
  activeCodes: analytics.activeCodes,

  usage: {
    totalRedemptions: analytics.redemptions,
    uniqueUsers: analytics.uniqueUsers,
    conversionRate: analytics.conversionRate,
  },

  revenue: {
    grossRevenue: analytics.grossRevenue,
    discountGiven: analytics.discountGiven,
    netRevenue: analytics.netRevenue,
  },

  topCodes: analytics.topCodes,
  // [{ code: 'SUMMER20', redemptions: 500, revenue: 25000 }]

  byCampaign: analytics.byCampaign,
});
```

---

## Export

```typescript
// Export usage report
const report = await coupons.exportReport({
  format: 'csv',
  code: 'SUMMER2024',
  from: '2024-06-01',
  to: '2024-08-31',
});

// Report includes: code, user, order, discount, timestamp
```

---

*Document version: 1.0*
*Last updated: 2026-03-05*
