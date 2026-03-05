# Bloom Filters

A probabilistic data structure for membership testing with possible false positives.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Cache filtering (check before DB hit)
* Spell checking
* Duplicate detection at scale
* Blockchain (UTXO verification)

Properties:
* Space efficient: O(n) bits
* No false negatives
* Possible false positives
* Add-only (no delete)
```

---

## Implementation

```typescript
class BloomFilter {
  private bits: boolean[];
  private size: number;
  private hashCount: number;

  constructor(size: number = 1000, hashCount: number = 3) {
    this.size = size;
    this.hashCount = hashCount;
    this.bits = new Array(size).fill(false);
  }

  private hash(value: string, seed: number): number {
    let hash = 0;
    for (let i = 0; i < value.length; i++) {
      hash = (hash * 31 + value.charCodeAt(i) + seed) % this.size;
    }
    return Math.abs(hash);
  }

  add(item: string): void {
    for (let i = 0; i < this.hashCount; i++) {
      const index = this.hash(item, i);
      this.bits[index] = true;
    }
  }

  mightContain(item: string): boolean {
    for (let i = 0; i < this.hashCount; i++) {
      const index = this.hash(item, i);
      if (!this.bits[index]) {
        return false;
      }
    }
    return true;
  }
}
```

---

## Usage

```typescript
const bloom = new BloomFilter(10000, 5);

// Add users to filter
bloom.add('user:123');
bloom.add('user:456');
bloom.add('user:789');

// Check membership
console.log(bloom.mightContain('user:123')); // true (probably)
console.log(bloom.mightContain('user:999')); // false (definitely)

// Before DB query
function getUser(id: string) {
  if (!bloom.mightContain(`user:${id}`)) {
    return null; // Definitely not in DB
  }
  // Might be in DB, do actual query
  return db.users.find(id);
}
```

---

## False Positive Rate

```
FORMULA:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

p ≈ (1 - e^(-kn/m))^k

Where:
* m = number of bits
* n = number of items
* k = number of hash functions

Optimal k = (m/n) × ln(2)

RECOMMENDED:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

For 1% false positive rate:
* m/n ≈ 10 bits per element
* k ≈ 7 hash functions
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
