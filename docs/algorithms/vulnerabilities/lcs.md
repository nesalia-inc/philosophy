# LCS (Longest Common Subsequence)

LCS finds the longest subsequence common to two sequences.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Diff algorithms (git diff)
* plagiarism detection
* Sequence alignment
* Bioinformatic applications

Complexities:
* Time: O(n × m)
* Space: O(n × m) or O(min(n, m))
```

---

## Implementation

```typescript
function lcs(a: string, b: string): number {
  const n = a.length;
  const m = b.length;
  const dp = Array.from({ length: n + 1 }, () => new Array(m + 1).fill(0));

  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= m; j++) {
      if (a[i - 1] === b[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
      } else {
        dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
      }
    }
  }

  return dp[n][m];
}
```

---

## Space-Optimized

```typescript
function lcsOptimized(a: string, b: string): number {
  const n = a.length;
  const m = b.length;

  // Use the shorter string for columns
  if (m > n) return lcsOptimized(b, a);

  const dp = new Array(m + 1).fill(0);
  let prev: number[];

  for (let i = 1; i <= n; i++) {
    prev = [...dp];
    for (let j = 1; j <= m; j++) {
      if (a[i - 1] === b[j - 1]) {
        dp[j] = prev[j - 1] + 1;
      } else {
        dp[j] = Math.max(prev[j], dp[j - 1]);
      }
    }
  }

  return dp[m];
}
```

---

## Get the Actual Subsequence

```typescript
function lcsWithString(a: string, b: string): string {
  const n = a.length;
  const m = b.length;
  const dp = Array.from({ length: n + 1 }, () => new Array(m + 1).fill(0));

  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= m; j++) {
      if (a[i - 1] === b[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
      } else {
        dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
      }
    }
  }

  // Backtrack to find the LCS
  let i = n, j = m;
  const lcs: string[] = [];

  while (i > 0 && j > 0) {
    if (a[i - 1] === b[j - 1]) {
      lcs.push(a[i - 1]);
      i--;
      j--;
    } else if (dp[i - 1][j] > dp[i][j - 1]) {
      i--;
    } else {
      j--;
    }
  }

  return lcs.reverse().join('');
}

// Usage
console.log(lcsWithString('ABCDGH', 'AEDFHR')); // ADH
console.log(lcsWithString('AGGTAB', 'GXTXAYB')); // GTAB
```

---

## Real-world: Git Diff

```typescript
function diff(oldText: string, newText: string): string[] {
  const oldLines = oldText.split('\n');
  const newLines = newText.split('\n');

  const n = oldLines.length;
  const m = newLines.length;
  const dp = Array.from({ length: n + 1 }, () => new Array(m + 1).fill(0));

  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= m; j++) {
      if (oldLines[i - 1] === newLines[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
      } else {
        dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
      }
    }
  }

  // Build diff output
  const diff: string[] = [];
  let i = n, j = m;

  while (i > 0 || j > 0) {
    if (i > 0 && j > 0 && oldLines[i - 1] === newLines[j - 1]) {
      diff.unshift(`  ${oldLines[i - 1]}`);
      i--; j--;
    } else if (j > 0 && (i === 0 || dp[i][j - 1] >= dp[i - 1][j])) {
      diff.unshift(`+ ${newLines[j - 1]}`);
      j--;
    } else {
      diff.unshift(`- ${oldLines[i - 1]}`);
      i--;
    }
  }

  return diff;
}
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
