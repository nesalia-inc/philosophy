# Lambda Cold Starts

Slow Lambda cold starts increase latency and costs.

---

## Problem

```
COLD START IMPACT:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Cold start: 1-10 seconds
* Warm: <100ms

If function called infrequently:
* Every call = cold start
* User experience suffers
* Retries = extra costs
```

---

## Prevention

### 1. Increase Memory

```typescript
// More memory = faster CPU
export const handler = awslambda.streamifyResponse(
  async (event) => {
    // More memory = faster execution
  }
);

// Configure: 1024MB or higher
```

### 2. Keep Functions Warm

```typescript
// Scheduled warming
import { CloudWatchEvents } from 'aws-sdk';

setInterval(async () => {
  await lambda.invoke({
    FunctionName: 'my-function',
    InvocationType: 'Event', // Async
  }).promise();
}, 5 * 60 * 1000); // Every 5 minutes
```

### 3. Reduce Package Size

```typescript
// GOOD - Minimal dependencies
// Only import what you need
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
