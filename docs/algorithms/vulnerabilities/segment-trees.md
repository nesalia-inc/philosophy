# Segment Trees

A segment tree is a binary tree used for answering range queries and point updates efficiently.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Range sum queries
* Range minimum/maximum queries
* Range product queries
* Point updates with range queries

Complexities:
* Build: O(n)
* Query: O(log n)
* Update: O(log n)
```

---

## Implementation

```typescript
class SegmentTree {
  private tree: number[];
  private n: number;

  constructor(arr: number[]) {
    this.n = arr.length;
    this.tree = new Array(4 * this.n);
    this.build(arr, 0, 0, this.n - 1);
  }

  private build(arr: number[], node: number, start: number, end: number) {
    if (start === end) {
      this.tree[node] = arr[start];
    } else {
      const mid = Math.floor((start + end) / 2);
      this.build(arr, 2 * node + 1, start, mid);
      this.build(arr, 2 * node + 2, mid + 1, end);
      this.tree[node] = this.tree[2 * node + 1] + this.tree[2 * node + 2];
    }
  }

  // Range sum query [l, r]
  query(l: number, r: number): number {
    return this.queryRecursive(0, 0, this.n - 1, l, r);
  }

  private queryRecursive(node: number, start: number, end: number, l: number, r: number): number {
    if (r < start || end < l) return 0;
    if (l <= start && end <= r) return this.tree[node];

    const mid = Math.floor((start + end) / 2);
    return this.queryRecursive(2 * node + 1, start, mid, l, r) +
           this.queryRecursive(2 * node + 2, mid + 1, end, l, r);
  }

  // Point update
  update(index: number, value: number) {
    this.updateRecursive(0, 0, this.n - 1, index, value);
  }

  private updateRecursive(node: number, start: number, end: number, index: number, value: number) {
    if (start === end) {
      this.tree[node] = value;
    } else {
      const mid = Math.floor((start + end) / 2);
      if (index <= mid) {
        this.updateRecursive(2 * node + 1, start, mid, index, value);
      } else {
        this.updateRecursive(2 * node + 2, mid + 1, end, index, value);
      }
      this.tree[node] = this.tree[2 * node + 1] + this.tree[2 * node + 2];
    }
  }
}
```

---

## Usage

```typescript
const arr = [1, 3, 5, 7, 9, 11];
const segTree = new SegmentTree(arr);

console.log(segTree.query(1, 3)); // 3 + 5 + 7 = 15
console.log(segTree.query(0, 5)); // 36

segTree.update(2, 10); // Change 5 to 10
console.log(segTree.query(1, 3)); // 3 + 10 + 7 = 20
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
