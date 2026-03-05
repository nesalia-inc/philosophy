# Topological Sort

Topological sorting orders vertices in a directed acyclic graph (DAG) such that for every edge (u, v), u comes before v.

---

## Use Cases

```
WHEN TO USE:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

* Task scheduling with dependencies
* Build systems (make, webpack)
* Course prerequisite ordering
* Package installation order
* Task pipeline ordering
```

---

## Kahn's Algorithm (BFS-based)

```typescript
function topologicalSortKahn(vertices: number[], edges: [number, number][]): number[] {
  // Build graph and in-degree count
  const graph = new Map<number, number[]>();
  const inDegree = new Map<number, number>();

  for (const v of vertices) {
    graph.set(v, []);
    inDegree.set(v, 0);
  }

  for (const [from, to] of edges) {
    graph.get(from)!.push(to);
    inDegree.set(to, inDegree.get(to)! + 1);
  }

  // Start with nodes having no incoming edges
  const queue: number[] = [];
  for (const [v, degree] of inDegree) {
    if (degree === 0) queue.push(v);
  }

  const result: number[] = [];

  while (queue.length > 0) {
    const u = queue.shift()!;
    result.push(u);

    for (const v of graph.get(u)!) {
      inDegree.set(v, inDegree.get(v)! - 1);
      if (inDegree.get(v) === 0) {
        queue.push(v);
      }
    }
  }

  // Check for cycles
  if (result.length !== vertices.length) {
    throw new Error('Graph has a cycle!');
  }

  return result;
}
```

---

## DFS-based Approach

```typescript
function topologicalSortDFS(vertices: number[], edges: [number, number][]): number[] {
  const graph = new Map<number, number[]>();

  for (const v of vertices) {
    graph.set(v, []);
  }

  for (const [from, to] of edges) {
    graph.get(from)!.push(to);
  }

  const visited = new Set<number>();
  const result: number[] = [];

  function dfs(v: number) {
    if (visited.has(v)) return;
    visited.add(v);

    for (const neighbor of graph.get(v)!) {
      dfs(neighbor);
    }

    result.push(v); // Add after visiting all neighbors
  }

  for (const v of vertices) {
    if (!visited.has(v)) {
      dfs(v);
    }
  }

  return result.reverse();
}
```

---

## Usage

```typescript
const vertices = [0, 1, 2, 3, 4];
const edges: [number, number][] = [
  [0, 1],
  [0, 2],
  [1, 3],
  [2, 3],
  [3, 4],
];

console.log(topologicalSortKahn(vertices, edges));
// [0, 1, 2, 3, 4]

console.log(topologicalSortDFS(vertices, edges));
// [0, 2, 1, 3, 4]
```

---

## Real-world Example: Task Scheduling

```typescript
interface Task {
  id: string;
  dependencies: string[];
}

function scheduleTasks(tasks: Task[]): string[] {
  const taskMap = new Map(tasks.map(t => [t.id, t.dependencies]));
  const allTasks = tasks.map(t => t.id);

  // Convert to vertex/edge format
  const edges: [string, string][] = [];
  for (const task of tasks) {
    for (const dep of task.dependencies) {
      edges.push([dep, task.id]);
    }
  }

  return topologicalSortKahn(allTasks, edges);
}

const tasks = [
  { id: 'build', dependencies: ['compile', 'test'] },
  { id: 'compile', dependencies: [] },
  { id: 'test', dependencies: ['compile'] },
  { id: 'deploy', dependencies: ['build'] },
];

console.log(scheduleTasks(tasks));
// ['compile', 'test', 'build', 'deploy']
```

---

*Document version: 1.0*
*Last updated: 2026-03-04*
