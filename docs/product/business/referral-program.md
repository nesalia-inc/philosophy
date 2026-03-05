# Referral Program

Viral referral program with rewards and tracking.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Referral link generation
* Double-sided rewards
* Social sharing
* Reward tiers
* Milestone tracking
* Viral coefficient calculation
```

---

## Implementation

```typescript
import { ReferralProgram } from '@nesalia/business';

const referral = new ReferralProgram({
  rewards: {
    referrer: {
      credit: 20, // $20 credit
      type: 'percentage', // or 'fixed'
    },
    referred: {
      credit: 20,
      type: 'percentage',
    },
  },
  milestones: [
    { referrals: 5, bonus: 50 },
    { referrals: 10, bonus: 100 },
    { referrals: 50, bonus: 500 },
  ],
  maxReferralsPerUser: 10,
});

// Get referral link for user
const link = referral.getLink('user_123');
console.log(link);
// https://example.com/ref/ABC123

// Track referral (on signup)
await referral.track({
  referrerCode: 'ABC123',
  referredUserId: 'user_456',
  source: 'email', // or 'twitter', 'facebook', 'linkedin'
});
```

---

## Reward Management

```typescript
// Give reward to referrer
await referral.giveReward('referrer_123', {
  type: 'credit',
  amount: 20,
  reason: 'referral_completed',
});

// Check milestones
const status = await referral.getMilestoneStatus('referrer_123');
console.log({
  totalReferrals: status.referrals,
  currentMilestone: status.currentMilestone,
  nextMilestone: status.nextMilestone,
  progress: status.progress, // 0-100%
  nextReward: status.nextReward,
});

// Viral coefficient
const viral = await referral.getViralCoefficient();
console.log({
  k: viral.k, // Viral coefficient (k > 1 = viral)
  cycleTime: viral.cycleTime, // Days per cycle
  growth: viral.growth,
});
```

---

## Analytics

```typescript
const analytics = await referral.getAnalytics({
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log({
  totalReferrals: analytics.total,
  successfulReferrals: analytics.completed,
  conversionRate: analytics.conversionRate,

  rewards: {
    totalGiven: analytics.rewards.totalGiven,
    byType: analytics.rewards.byType,
  },

  topReferrers: analytics.topReferrers,

  channels: {
    email: analytics.channels.email,
    twitter: analytics.channels.twitter,
    facebook: analytics.channels.facebook,
    linkedin: analytics.channels.linkedin,
  },

  viral: analytics.viral,
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
