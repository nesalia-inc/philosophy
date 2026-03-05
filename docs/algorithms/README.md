# Algorithms

Advanced algorithm documentation for software development.

---

## Categories

### Data Structures

| Algorithm | Description |
|-----------|-------------|
| [Segment Trees](./vulnerabilities/segment-trees.md) | Range queries and updates |
| [Fenwick Trees](./vulnerabilities/fenwick-trees.md) | Prefix sums and frequencies |
| [Tries](./vulnerabilities/tries.md) | Prefix matching and autocomplete |
| [Bloom Filters](./vulnerabilities/bloom-filters.md) | Probabilistic membership testing |
| [Disjoint Sets](./vulnerabilities/disjoint-sets.md) | Union-Find with path compression |

### Graph Algorithms

| Algorithm | Description |
|-----------|-------------|
| [Dijkstra](./vulnerabilities/dijkstra.md) | Single-source shortest path |
| [A* Search](./vulnerabilities/astar.md) | Heuristic pathfinding |
| [Topological Sort](./vulnerabilities/topological-sort.md) | Dependency ordering |
| [Union-Find](./vulnerabilities/disjoint-sets.md) | Connected components |

### Dynamic Programming

| Algorithm | Description |
|-----------|-------------|
| [Memoization](./vulnerabilities/memoization.md) | Top-down DP |
| [Tabulation](./vulnerabilities/tabulation.md) | Bottom-up DP |
| [Knapsack](./vulnerabilities/knapsack.md) | Resource optimization |
| [LCS](./vulnerabilities/lcs.md) | Longest common subsequence |

### String Algorithms

| Algorithm | Description |
|-----------|-------------|
| [KMP](./vulnerabilities/kmp.md) | Pattern matching |
| [Rabin-Karp](./vulnerabilities/rabin-karp.md) | Hash-based pattern matching |

### Concurrency

| Algorithm | Description |
|-----------|-------------|
| [Lock-free Structures](./vulnerabilities/lock-free.md) | CAS-based data structures |
| [Producer-Consumer](./vulnerabilities/producer-consumer.md) | Thread-safe queues |

---

## Complexity Reference

```
COMPLEXITY CHEAT SHEET:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SEARCHING:
Binary Search:        O(log n)
Hash Table:           O(1) average, O(n) worst
BST:                  O(log n) average

SORTING:
Quick/Merge/Heap:     O(n log n)
Counting/Radix:       O(n + k)
Bubble/Insert:        O(n²)

GRAPH:
BFS/DFS:              O(V + E)
Dijkstra:             O((V + E) log V)
A*:                   O(E) with heuristic

DYNAMIC PROGRAMMING:
2D DP:                O(n × m)
3D DP:                O(n × m × k)
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
