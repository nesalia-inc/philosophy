# Keyword Tracker

Track keyword rankings across search engines with historical data.

---

## Features

```
CAPABILITIES:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Multi-engine tracking (Google, Bing, etc.)
* Historical ranking data
* Position changes visualization
* Volume and difficulty metrics
* Competitor comparisons
* Automated alerts
```

---

## Implementation

```typescript
import { KeywordTracker } from '@nesalia/seo';

const tracker = new KeywordTracker({
  engine: 'google',
  country: 'us',
  language: 'en',
});

// Track keywords
await tracker.track('software development company', {
  url: 'https://example.com',
  tags: ['business', 'main'],
});

// Get rankings
const rankings = await tracker.getRankings({
  keyword: 'software development company',
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log(rankings.map(r => ({
  date: r.date,
  position: r.position,
  url: r.url,
  change: r.change,
})));
```

---

## Data Model

```typescript
interface Keyword {
  id: string;
  keyword: string;
  engine: 'google' | 'bing' | 'yahoo';
  country: string;
  language: string;
  createdAt: Date;
}

interface Ranking {
  id: string;
  keywordId: string;
  date: Date;
  position: number;
  previousPosition: number | null;
  url: string;
  title: string;
  snippet: string;
  change: number; // positive = improved
}

interface KeywordMetrics {
  keywordId: string;
  avgPosition: number;
  bestPosition: number;
  worstPosition: number;
  avgChange: number;
  totalDaysRanked: number;
}
```

---

## Dashboard Integration

```typescript
// Get dashboard data
const dashboard = await tracker.getDashboard({
  from: '2024-01-01',
  to: '2024-03-04',
});

console.log({
  totalKeywords: dashboard.totalKeywords,
  avgPosition: dashboard.avgPosition,
  topGainers: dashboard.topGainers, // Most improved
  topLosers: dashboard.topLosers,   // Most declined
  distribution: dashboard.positionDistribution, // 1-10, 11-20, etc.
});
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
