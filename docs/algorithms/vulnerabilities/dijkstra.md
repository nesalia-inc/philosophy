# Dijkstra's Algorithm

Finds the shortest path from a source vertex to all other vertices in a weighted graph.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* GPS/routing systems
* Network routing
* Flight scheduling
* Game pathfinding (without obstacles)

Requirements:
* Non-negative edge weights
* Undirected or directed graphs
```

---

## Implementation

```typescript
interface Edge {
  to: number;
  weight: number;
}

class Dijkstra {
  private graph: Edge[][];

  constructor(vertices: number) {
    this.graph = Array.from({ length: vertices }, () => []);
  }

  addEdge(from: number, to: number, weight: number) {
    this.graph[from].push({ to, weight });
    this.graph[to].push({ from, weight }); // Undirected
  }

  shortestPath(source: number): number[] {
    const distances = new Array(this.graph.length).fill(Infinity);
    const visited = new Array(this.graph.length).fill(false);
    distances[source] = 0;

    const minHeap = new MinHeap<number>();
    minHeap.insert({ value: source, priority: 0 });

    while (minHeap.size() > 0) {
      const { value: u } = minHeap.extractMin()!;

      if (visited[u]) continue;
      visited[u] = true;

      for (const edge of this.graph[u]) {
        const v = edge.to;
        const newDist = distances[u] + edge.weight;

        if (newDist < distances[v]) {
          distances[v] = newDist;
          minHeap.insert({ value: v, priority: newDist });
        }
      }
    }

    return distances;
  }
}

class MinHeap<T> {
  private heap: { value: T; priority: number }[] = [];

  insert(item: { value: T; priority: number }) {
    this.heap.push(item);
    this.bubbleUp(this.heap.length - 1);
  }

  extractMin() {
    if (this.heap.length === 0) return null;
    const min = this.heap[0];
    const last = this.heap.pop()!;
    if (this.heap.length > 0) {
      this.heap[0] = last;
      this.bubbleDown(0);
    }
    return min;
  }

  size() {
    return this.heap.length;
  }

  private bubbleUp(i: number) {
    while (i > 0) {
      const parent = Math.floor((i - 1) / 2);
      if (this.heap[parent].priority <= this.heap[i].priority) break;
      [this.heap[parent], this.heap[i]] = [this.heap[i], this.heap[parent]];
      i = parent;
    }
  }

  private bubbleDown(i: number) {
    while (true) {
      const left = 2 * i + 1;
      const right = 2 * i + 2;
      let smallest = i;

      if (left < this.heap.length && this.heap[left].priority < this.heap[smallest].priority) {
        smallest = left;
      }
      if (right < this.heap.length && this.heap[right].priority < this.heap[smallest].priority) {
        smallest = right;
      }
      if (smallest === i) break;
      [this.heap[i], this.heap[smallest]] = [this.heap[smallest], this.heap[i]];
      i = smallest;
    }
  }
}
```

---

## Usage

```typescript
const dijkstra = new Dijkstra(6);

dijkstra.addEdge(0, 1, 4);
dijkstra.addEdge(0, 2, 2);
dijkstra.addEdge(1, 2, 1);
dijkstra.addEdge(1, 3, 5);
dijkstra.addEdge(2, 3, 8);
dijkstra.addEdge(2, 4, 10);
dijkstra.addEdge(3, 4, 2);
dijkstra.addEdge(3, 5, 6);
dijkstra.addEdge(4, 5, 3);

const distances = dijkstra.shortestPath(0);
console.log(distances);
// [0, 3, 2, 8, 10, 13]
```

---

## Complexity

```
COMPLEXITY:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Time: O((V + E) log V) with binary heap
      O(V²) with array

Space: O(V)
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
