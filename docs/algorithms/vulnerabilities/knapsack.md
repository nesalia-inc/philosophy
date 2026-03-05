# Knapsack Problem

The knapsack problem is a classic optimization problem where you maximize value within a weight limit.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Resource allocation
* Budget optimization
* Portfolio selection
* Load balancing

Variations:
* 0/1 Knapsack: Each item can be taken once
* Unbounded: Each item can be taken multiple times
```

---

## 0/1 Knapsack Implementation

```typescript
function knapsack(values: number[], weights: number[], capacity: number): number {
  const n = values.length;
  const dp = Array.from({ length: n + 1 }, () => new Array(capacity + 1).fill(0));

  for (let i = 1; i <= n; i++) {
    for (let w = 0; w <= capacity; w++) {
      if (weights[i - 1] <= w) {
        dp[i][w] = Math.max(
          dp[i - 1][w],
          dp[i - 1][w - weights[i - 1]] + values[i - 1]
        );
      } else {
        dp[i][w] = dp[i - 1][w];
      }
    }
  }

  return dp[n][capacity];
}

// Test
const values = [60, 100, 120];
const weights = [10, 20, 30];
const capacity = 50;

console.log(knapsack(values, weights, capacity)); // 220
```

---

## Space-Optimized Version

```typescript
function knapsackOptimized(values: number[], weights: number[], capacity: number): number {
  const dp = new Array(capacity + 1).fill(0);

  for (let i = 0; i < values.length; i++) {
    // Iterate backwards to avoid using same item twice
    for (let w = capacity; w >= weights[i]; w--) {
      dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
    }
  }

  return dp[capacity];
}
```

---

## Unbounded Knapsack

```typescript
function unboundedKnapsack(values: number[], weights: number[], capacity: number): number {
  const dp = new Array(capacity + 1).fill(0);

  for (let w = 0; w <= capacity; w++) {
    for (let i = 0; i < values.length; i++) {
      if (weights[i] <= w) {
        dp[w] = Math.max(dp[w], dp[w - weights[i]] + values[i]);
      }
    }
  }

  return dp[capacity];
}
```

---

## Output: What Items Were Selected

```typescript
function knapsackWithItems(values: number[], weights: number[], capacity: number) {
  const n = values.length;
  const dp = Array.from({ length: n + 1 }, () => new Array(capacity + 1).fill(0));

  for (let i = 1; i <= n; i++) {
    for (let w = 0; w <= capacity; w++) {
      if (weights[i - 1] <= w) {
        dp[i][w] = Math.max(
          dp[i - 1][w],
          dp[i - 1][w - weights[i - 1]] + values[i - 1]
        );
      } else {
        dp[i][w] = dp[i - 1][w];
      }
    }
  }

  // Backtrack to find items
  const items: number[] = [];
  let w = capacity;
  for (let i = n; i > 0; i--) {
    if (dp[i][w] !== dp[i - 1][w]) {
      items.push(i - 1);
      w -= weights[i - 1];
    }
  }

  return { maxValue: dp[n][capacity], items };
}
```

---

## Complexity

```
COMPLEXITY:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Standard:  O(n × W) time, O(n × W) space
Optimized: O(n × W) time, O(W) space

Where:
* n = number of items
* W = capacity
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
