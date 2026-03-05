# Student Discounts

Automated student verification and discount management.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Student email verification (.edu, .ac.uk, etc.)
* Integration with SheerID
* Automatic discount application
* Expiry tracking
* Renewal reminders
* Compliance reporting
```

---

## Implementation

```typescript
import { StudentDiscounts } from '@nesalia/business';

const studentDiscount = new StudentDiscounts({
  discount: 50, // 50% off
  discountType: 'percentage', // or 'fixed'
  verifyProvider: 'sheerid', // or 'custom'
  maxVerifications: 1000,
});

// Verify student
const verification = await studentDiscount.verify({
  email: 'student@university.edu',
  firstName: 'John',
  lastName: 'Doe',
  dateOfBirth: '2000-01-01',
});

console.log({
  verified: verification.verified,
  verificationId: verification.id,
  expiresAt: verification.expiresAt,
  discount: verification.discount,
});

// Apply discount to purchase
const discount = await studentDiscount.applyDiscount({
  verificationId: verification.id,
  originalPrice: 100,
});

console.log(discount);
// { discountedPrice: 50, discount: 50, verified: true }
```

---

## Verification Providers

```typescript
// SheerID integration
const sheerid = new SheerIDProvider({
  apiKey: process.env.SHEERID_API_KEY,
  templateId: 'student_discount',
});

// Custom email verification
const custom = new CustomEmailProvider({
  allowedDomains: ['.edu', '.ac.uk', '.edu.au', '.edu.cn'],
  verifyEmail: true, // Send verification email
});
```

---

## Management

```typescript
// Get all active verifications
const verifications = await studentDiscount.getActive({
  from: '2024-01-01',
  to: '2024-03-04',
});

// Send renewal reminders
await studentDiscount.sendRenewalReminders({
  daysBeforeExpiry: 30,
});

// Revoke expired verifications
await studentDiscount.revokeExpired();

// Analytics
const analytics = await studentDiscount.getAnalytics();

console.log({
  totalVerifications: analytics.total,
  successfulVerifications: analytics.verified,
  verificationRate: analytics.verificationRate,
  discountRedeemed: analytics.discountRedeemed,
  revenueImpact: analytics.revenueImpact, // Revenue lost to discounts
});
```

---

## Compliance

```typescript
// Export for compliance
const report = await studentDiscount.exportReport({
  format: 'csv',
  from: '2024-01-01',
  to: '2024-03-04',
});

// GDPR/CCPA compliant
const userData = await studentDiscount.getUserData('verification_123');
await studentDiscount.deleteUserData('verification_123');
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
