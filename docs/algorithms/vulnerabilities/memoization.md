# Memoization

Memoization is a top-down dynamic programming technique that caches function results.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Recursive functions with overlapping subproblems
* Expensive pure function calls
* Fibonacci calculation
* Factorial computation
* Tree/graph traversal with repeated nodes

Key Requirements:
* Pure function (same input → same output)
* No side effects
```

---

## Implementation

```typescript
// Generic memoization function
function memoize<T extends (...args: any[]) => any>(
  fn: T
): T {
  const cache = new Map<string, ReturnType<T>>();

  return ((...args: Parameters<T>): ReturnType<T> => {
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      return cache.get(key)!;
    }

    const result = fn(...args);
    cache.set(key, result);
    return result;
  }) as T;
}

// Fibonacci with memoization
const fibonacci = memoize((n: number): number => {
  if (n <= 1) return n;
  return fibonacci(n - 1) + fibonacci(n - 2);
});

console.log(fibonacci(50)); // Fast due to memoization
```

---

## React useMemo Pattern

```typescript
// Expensive computation
function ExpensiveComponent({ data, filter }) {
  // Only recomputes when data or filter changes
  const processedData = useMemo(() => {
    return data
      .filter(item => item.category === filter)
      .sort((a, b) => b.value - a.value)
      .map(item => expensiveTransform(item));
  }, [data, filter]);

  return <List items={processedData} />;
}
```

---

## Cache with TTL

```typescript
function memoizeWithTTL<T extends (...args: any[]) => any>(
  fn: T,
  ttl: number = 60000
): T {
  const cache = new Map<string, { value: ReturnType<T>; expiry: number }>();

  return ((...args: Parameters<T>): ReturnType<T> => {
    const key = JSON.stringify(args);
    const now = Date.now();

    const cached = cache.get(key);
    if (cached && cached.expiry > now) {
      return cached.value;
    }

    const result = fn(...args);
    cache.set(key, { value: result, expiry: now + ttl });
    return result;
  }) as T;
}
```

---

## Comparison: Memoization vs Tabulation

```
MEMOIZATION (Top-Down):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Start with the problem
* Break into subproblems
* Solve recursively, cache results
* Good when: you don't need all subproblems

TABULATION (Bottom-Up):
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Start with smallest subproblems
* Build up to the solution
* Fill table iteratively
* Good when: you need all subproblems
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
