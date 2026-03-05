# Disjoint Sets (Union-Find)

Disjoint sets efficiently track elements partitioned into disjoint sets.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Cycle detection in graphs
* Kruskal's MST algorithm
* Network connectivity
* Image segmentation
* Lagrange's Union-Find

Complexities:
* Near O(1) with path compression + union by rank
```

---

## Implementation

```typescript
class DisjointSet {
  private parent: number[];
  private rank: number[];

  constructor(n: number) {
    this.parent = Array.from({ length: n }, (_, i) => i);
    this.rank = new Array(n).fill(0);
  }

  // Find with path compression
  find(x: number): number {
    if (this.parent[x] !== x) {
      this.parent[x] = this.find(this.parent[x]); // Path compression
    }
    return this.parent[x];
  }

  // Union by rank
  union(x: number, y: number): void {
    const rootX = this.find(x);
    const rootY = this.find(y);

    if (rootX === rootY) return; // Already in same set

    // Union by rank
    if (this.rank[rootX] < this.rank[rootY]) {
      this.parent[rootX] = rootY;
    } else if (this.rank[rootX] > this.rank[rootY]) {
      this.parent[rootY] = rootX;
    } else {
      this.parent[rootY] = rootX;
      this.rank[rootX]++;
    }
  }

  // Check if connected
  connected(x: number, y: number): boolean {
    return this.find(x) === this.find(y);
  }
}
```

---

## Usage: Cycle Detection

```typescript
function hasCycle(vertices: number[], edges: [number, number][]): boolean {
  const ds = new DisjointSet(vertices.length);

  for (const [from, to] of edges) {
    if (ds.connected(from, to)) {
      return true; // Cycle detected!
    }
    ds.union(from, to);
  }

  return false;
}

// Test
const vertices = [0, 1, 2, 3];
const edges: [number, number][] = [
  [0, 1],
  [1, 2],
  [2, 0], // Creates cycle!
  [2, 3],
];

console.log(hasCycle(vertices, edges)); // true
```

---

## Usage: Kruskal's MST

```typescript
interface Edge {
  from: number;
  to: number;
  weight: number;
}

function kruskalMST(vertices: number[], edges: Edge[]): Edge[] {
  // Sort edges by weight
  const sortedEdges = [...edges].sort((a, b) => a.weight - b.weight);

  const ds = new DisjointSet(vertices.length);
  const mst: Edge[] = [];

  for (const edge of sortedEdges) {
    if (!ds.connected(edge.from, edge.to)) {
      ds.union(edge.from, edge.to);
      mst.push(edge);
    }
  }

  return mst;
}

// Test
const vertices = [0, 1, 2, 3];
const edges: Edge[] = [
  { from: 0, to: 1, weight: 10 },
  { from: 0, to: 2, weight: 6 },
  { from: 0, to: 3, weight: 5 },
  { from: 1, to: 3, weight: 15 },
  { from: 2, to: 3, weight: 4 },
];

console.log(kruskalMST(vertices, edges));
// [{from: 2, to: 3, weight: 4}, {from: 0, to: 3, weight: 5}, {from: 0, to: 1, weight: 10}]
```

---

## Complexity Analysis

```
COMPLEXITY:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

With path compression + union by rank:

* Find: O(α(n)) ≈ O(1) amortized
* Union: O(α(n)) ≈ O(1) amortized

α(n) = inverse Ackermann function
(for all practical n, α(n) ≤ 4)
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
