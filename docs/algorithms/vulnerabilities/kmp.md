# KMP (Knuth-Morris-Pratt)

KMP is an efficient string matching algorithm that finds pattern occurrences in O(n + m) time.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* String searching
* Pattern matching
* Text editors
* DNA sequence matching

Complexities:
* Time: O(n + m)
* Space: O(m)
```

---

## Implementation

```typescript
function buildLPS(pattern: string): number[] {
  const lps = new Array(pattern.length).fill(0);
  let length = 0;
  let i = 1;

  while (i < pattern.length) {
    if (pattern[i] === pattern[length]) {
      length++;
      lps[i] = length;
      i++;
    } else {
      if (length !== 0) {
        length = lps[length - 1];
      } else {
        lps[i] = 0;
        i++;
      }
    }
  }

  return lps;
}

function kmpSearch(text: string, pattern: string): number[] {
  const lps = buildLPS(pattern);
  const occurrences: number[] = [];

  let i = 0; // text index
  let j = 0; // pattern index

  while (i < text.length) {
    if (pattern[j] === text[i]) {
      i++;
      j++;
    }

    if (j === pattern.length) {
      occurrences.push(i - j);
      j = lps[j - 1];
    } else if (i < text.length && pattern[j] !== text[i]) {
      if (j !== 0) {
        j = lps[j - 1];
      } else {
        i++;
      }
    }
  }

  return occurrences;
}
```

---

## Usage

```typescript
const text = 'ABABDABACDABABCABAB';
const pattern = 'ABABCABAB';

const occurrences = kmpSearch(text, pattern);
console.log(occurrences); // [10]

// Show matches
for (const idx of occurrences) {
  console.log(`Found at index ${idx}: ${text.slice(idx, idx + pattern.length)}`);
}
```

---

## How It Works

```
EXAMPLE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Pattern: ABABCABAB
LPS:    [0,0,1,2,0,1,2,3,4]

Text:    ABABDABACDABABCABAB
              ^
Mismatch at index 4

Instead of starting over, use LPS:
LPS[3] = 2, so shift pattern by 4-2 = 2

Text:    ABABDABACDABABCABAB
                  ^^
Match found at index 10!
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
