# Trial Conversion Analytics

Analyze and optimize trial-to-paid conversion rates.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Trial start/end tracking
* Conversion funnel analysis
* Trial engagement scoring
* Churn prediction
* Cohort analysis
* A/B test support
* Optimization recommendations
* Automated interventions
```

---

## Implementation

```typescript
import { TrialAnalytics } from '@nesalia/business';

const trial = new TrialAnalytics();

// Get trial overview
const overview = await trial.getOverview({
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log({
  totalTrials: overview.totalTrials,
  activeTrials: overview.activeTrials,
  convertedTrials: overview.converted,
  churnedTrials: overview.churned,

  conversionRate: overview.conversionRate,
  // Percentage of trials that converted

  avgTrialLength: overview.avgTrialLength,
  // Days from start to conversion or churn
});
```

---

## Funnel Analysis

```typescript
// Trial conversion funnel
const funnel = await trial.getFunnel({
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log({
  stages: funnel.stages,
  // [
  //   { name: 'trial_started', count: 1000, rate: 100 },
  //   { name: 'activated', count: 700, rate: 70 },     // Used product
  //   { name: 'engaged', count: 500, rate: 50 },       // Key features used
  //   { name: 'converted', count: 300, rate: 30 },
  // ]

  dropOffs: funnel.dropoffs,
  // Where users leave the funnel
});
```

---

## Engagement Scoring

```typescript
// Get trial user engagement score
const score = await trial.getEngagementScore({
  trialUserId: 'trial_123',
});

console.log({
  score: score.value, // 0-100
  level: score.level, // low, medium, high

  factors: score.factors,
  // {
  //   featureUsage: 40,
  //   sessionFrequency: 30,
  //   actionsCount: 20,
  //   timeSpent: 10,
  // }

  prediction: {
    likelyToConvert: score.prediction.likelyToConvert,
    confidence: score.prediction.confidence,
  },
});
```

---

## Cohort Analysis

```typescript
// Analyze by cohort (when trial started)
const cohort = await trial.getCohortAnalysis({
  from: '2024-01-01',
  to: '2024-03-04',
  groupBy: 'week', // or 'month'
});

console.log({
  cohorts: cohort.cohorts,
  // [
  //   {
  //     period: '2024-01-W1',
  //     users: 100,
  //     conversionRate: 35,
  //     day1: 80, day3: 60, day7: 45, day14: 38,
  //   },
  //   ...
  // ]

  avgConversionByAge: cohort.avgConversionByAge,
  // { day1: 75, day3: 55, day7: 42, day14: 32, day30: 28 }
});
```

---

## Predicting Conversion

```typescript
// Predict which trials will convert
const predictions = await trial.predictConversions({
  trialIds: ['trial_123', 'trial_456', 'trial_789'],
});

console.log(predictions);
// [
//   { trialId: 'trial_123', probability: 0.85, recommendation: 'nurture' },
//   { trialId: 'trial_456', probability: 0.15, recommendation: 'intervene' },
//   { trialId: 'trial_789', probability: 0.60, recommendation: 'convert' },
// ]

// Recommendations:
// - convert: High probability, ensure smooth upgrade
// - nurture: Medium, send educational content
// - intervene: Low, send personalized outreach
```

---

## A/B Testing

```typescript
// Track trial A/B test
await trial.trackExperiment({
  experimentId: 'trial_length_test',
  variant: '14_day',
  userId: 'user_123',
});

// Get experiment results
const experiment = await trial.getExperimentResults({
  experimentId: 'trial_length_test',
});

console.log({
  variants: experiment.variants,
  // [
  //   { name: '7_day', users: 500, conversions: 100, rate: 20 },
  //   { name: '14_day', users: 500, conversions: 150, rate: 30 },
  // ]

  winner: experiment.winner, // 14_day
  confidence: experiment.confidence, // 95%
  lift: experiment.lift, // 50% improvement
});
```

---

## Automated Interventions

```typescript
// Trigger intervention based on score
await trial.triggerIntervention({
  trialUserId: 'trial_123',
  interventionType: 'email', // or 'notification', 'discount'
  template: 'trial_reminder_day_7',
});

// Get users needing intervention
const atRisk = await trial.getAtRiskTrials({
  minScore: 30, // Below this score = at risk
});

console.log({
  count: atRisk.length,
  users: atRisk.map(u => ({
    id: u.id,
    score: u.engagementScore,
    daysRemaining: u.daysRemaining,
    recommendedAction: u.recommendedAction,
  })),
});
```

---

## Optimization Report

```typescript
// Get optimization recommendations
const recommendations = await trial.getRecommendations({
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log(recommendations);
// [
//   {
//     type: 'trial_length',
//     current: 14,
//     suggested: 21,
//     expectedImpact: '+15% conversion',
//     confidence: 'high',
//   },
//   {
//     type: 'onboarding',
//     issue: 'Drop-off at step 3',
//     suggestion: 'Add progress indicator',
//     expectedImpact: '+8% activation',
//   },
// ]
```

---

## Export

``````typescript
// Export trial data
const export = await trial.export({
  format: 'csv',
  from: '2024-01-01',
  to: '2024-03-04',
  fields: ['userId', 'startDate', 'endDate', 'converted', 'engagementScore'],
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-05*
