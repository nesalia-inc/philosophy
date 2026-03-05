# A* Search Algorithm

A* combines Dijkstra's algorithm with heuristics for efficient pathfinding.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Game pathfinding
* GPS navigation
* Puzzle solving (8-puzzle, rubik's cube)
* Robot navigation

Requirements:
* Admissible heuristic (never overestimates)
* Non-negative edge costs
```

---

## Implementation

```typescript
interface Node {
  x: number;
  y: number;
  g: number; // Cost from start
  h: number; // Heuristic to goal
  f: number; // g + h
  parent: Node | null;
}

function heuristic(a: Node, b: Node): number {
  // Manhattan distance (for grid)
  return Math.abs(a.x - b.x) + Math.abs(a.y - b.y);
}

function aStar(
  grid: number[][],  // 0 = walkable, 1 = obstacle
  start: Node,
  goal: Node
): Node[] | null {
  const rows = grid.length;
  const cols = grid[0].length;
  const openSet: Node[] = [start];
  const closedSet = new Set<string>();

  while (openSet.length > 0) {
    // Get node with lowest f score
    let current = openSet[0];
    let currentIndex = 0;
    for (let i = 1; i < openSet.length; i++) {
      if (openSet[i].f < current.f) {
        current = openSet[i];
        currentIndex = i;
      }
    }

    // Remove from openSet
    openSet.splice(currentIndex, 1);

    // Check if goal
    if (current.x === goal.x && current.y === goal.y) {
      // Reconstruct path
      const path: Node[] = [];
      let node: Node | null = current;
      while (node) {
        path.unshift(node);
        node = node.parent;
      }
      return path;
    }

    closedSet.add(`${current.x},${current.y}`);

    // Check neighbors
    const directions = [[0, 1], [1, 0], [0, -1], [-1, 0]];
    for (const [dx, dy] of directions) {
      const nx = current.x + dx;
      const ny = current.y + dy;

      // Check bounds and obstacles
      if (nx < 0 || nx >= rows || ny < 0 || ny >= cols || grid[nx][ny] === 1) {
        continue;
      }

      if (closedSet.has(`${nx},${ny}`)) continue;

      const neighbor: Node = {
        x: nx,
        y: ny,
        g: current.g + 1,
        h: heuristic({ x: nx, y: ny, g: 0, h: 0, f: 0, parent: null }, goal),
        f: 0,
        parent: current,
      };
      neighbor.f = neighbor.g + neighbor.h;

      // Check if already in openSet with better path
      const existing = openSet.find(n => n.x === nx && n.y === ny);
      if (existing && existing.g <= neighbor.g) {
        continue;
      }

      openSet.push(neighbor);
    }
  }

  return null; // No path found
}
```

---

## Usage

```typescript
const grid = [
  [0, 0, 0, 0, 0],
  [0, 1, 1, 1, 0],
  [0, 0, 0, 1, 0],
  [0, 1, 0, 0, 0],
  [0, 0, 0, 0, 0],
];

const start = { x: 0, y: 0, g: 0, h: 0, f: 0, parent: null };
const goal = { x: 4, y: 4, g: 0, h: 0, f: 0, parent: null };

const path = aStar(grid, start, goal);

if (path) {
  console.log('Path found:', path.map(n => `(${n.x},${n.y})`));
} else {
  console.log('No path found');
}
```

---

## Heuristics

```
COMMON HEURISTICS:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Manhattan (grid, 4 directions):
h = |x1 - x2| + |y1 - y2|

Euclidean (any direction):
h = sqrt((x1 - x2)² + (y1 - y2)²)

Chebyshev (8 directions):
h = max(|x1 - x2|, |y1 - y2|)

DIAGONAL (8 directions):
h = max(|x1 - x2|, |y1 - y2|) + (sqrt(2) - 1) * min(|x1 - x2|, |y1 - y2|)
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
