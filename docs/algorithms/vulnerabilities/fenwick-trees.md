# Fenwick Trees (Binary Indexed Trees)

A Fenwick tree provides efficient prefix sums and point updates.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Prefix sum queries
* Frequency counts
* When you need O(log n) updates and queries
* Simpler than segment tree for prefix sums

Complexities:
* Build: O(n log n) or O(n)
* Query: O(log n)
* Update: O(log n)
```

---

## Implementation

```typescript
class FenwickTree {
  private tree: number[];
  private n: number;

  constructor(size: number) {
    this.n = size;
    this.tree = new Array(size + 1).fill(0);
  }

  // Point update: add value at index
  update(index: number, delta: number) {
    for (index++; index <= this.n; index += index & -index) {
      this.tree[index] += delta;
    }
  }

  // Prefix sum [0, index]
  query(index: number): number {
    let sum = 0;
    for (index++; index > 0; index -= index & -index) {
      sum += this.tree[index];
    }
    return sum;
  }

  // Range sum [l, r]
  rangeQuery(l: number, r: number): number {
    return this.query(r) - (l > 0 ? this.query(l - 1) : 0);
  }

  // Build from array
  build(arr: number[]) {
    for (let i = 0; i < arr.length; i++) {
      this.update(i, arr[i]);
    }
  }
}
```

---

## Usage

```typescript
const fenwick = new FenwickTree(6);
fenwick.build([1, 3, 5, 7, 9, 11]);

console.log(fenwick.query(2));        // 1 + 3 + 5 = 9
console.log(fenwick.rangeQuery(1, 4)); // 3 + 5 + 7 + 9 = 24

fenwick.update(2, 5); // Add 5 to index 2
console.log(fenwick.query(2));        // 1 + 3 + 10 = 14
```

---

## Comparison with Segment Tree

```
FENWICK vs SEGMENT TREE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Fenwick Tree:
* Only supports prefix sums
* Less memory: O(n)
* Simpler code
* Perfect for: frequency counts, cumulative sums

Segment Tree:
* Supports range queries (min, max, sum)
* More memory: O(4n)
* More flexible
* Perfect for: complex range queries
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
