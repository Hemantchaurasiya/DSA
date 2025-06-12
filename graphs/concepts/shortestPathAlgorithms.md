# How many way to find shortest path in the graph?

There are several ways to find the **shortest path** in a graph, depending on:

- **Graph type** (directed/undirected, weighted/unweighted)
- **Weight types** (positive/negative/zero)
- **Target** (single source to all, single source to one, all pairs, etc.)

---

## 🔍 **Types of Shortest Path Problems**

1. **Single Source Shortest Path**
    - Find shortest paths from a source to all other vertices.
2. **Single Pair Shortest Path**
    - Find the shortest path from one vertex to another.
3. **All Pairs Shortest Path**
    - Find shortest paths between all pairs of vertices.

---

## ✅ **Algorithms Based on Graph Type**

### 1. **Unweighted Graph**

- **BFS (Breadth-First Search)**
    - Time: `O(V + E)`
    - Use case: Unweighted graphs (each edge cost = 1)
    - Example: Shortest path in maze/grid

---

### 2. **Weighted Graph with Positive Weights**

- **Dijkstra’s Algorithm**
    - Time: `O((V + E) log V)` with a priority queue
    - Use case: Positive weights only
    - Variants:
        - Simple Dijkstra with array → `O(V^2)`
        - With min-heap (priority queue) → `O((V + E) log V)`

---

### 3. **Weighted Graph with Negative Weights (No Negative Cycles)**

- **Bellman-Ford Algorithm**
    - Time: `O(V * E)`
    - Use case: Handles negative weights, detects negative cycles
    - Also works when Dijkstra fails (due to negative edges)

---

### 4. **All-Pairs Shortest Path**

- **Floyd-Warshall Algorithm**
    - Time: `O(V^3)`
    - Use case: All-pairs shortest path, handles negative weights (no negative cycles)

---

### 5. *A (A Star) Algorithm*

- Time: Depends on heuristic
- Use case: Grid/maze problems with heuristic function (like Manhattan Distance)
- Optimal if heuristic is admissible

---

### 6. **0-1 BFS**

- Time: `O(V + E)`
- Use case: Edge weights are 0 or 1
- Uses deque instead of priority queue

---

### 7. **SPFA (Shortest Path Faster Algorithm)**

- Time: Better average-case than Bellman-Ford
- Use case: Alternative to Bellman-Ford in practice

---

### 8. **Bidirectional Dijkstra**

- Time: `O((V + E) log V)` but faster in practice
- Use case: Single pair shortest path with optimization

---

### 9. **Johnson’s Algorithm**

- Time: `O(V^2 log V + VE)`
- Use case: All-pairs shortest path with negative weights (no negative cycles)
- Combines Bellman-Ford + Dijkstra

---

## 🧠 Summary Table

| Graph Type | Algo | Handles Negative | Time Complexity |
| --- | --- | --- | --- |
| Unweighted | BFS | ❌ | O(V + E) |
| Positive weights | Dijkstra | ❌ | O((V + E) log V) |
| With negative weights | Bellman-Ford | ✅ | O(V * E) |
| All-pairs (any weights) | Floyd-Warshall | ✅ | O(V³) |
| 0-1 Edge Weights | 0-1 BFS | ❌ | O(V + E) |
| With heuristic (grid/maze) | A* | ❌ | Depends on heuristic |
| Faster all-pairs with neg. wt | Johnson’s Algo | ✅ | O(V² log V + VE) |

### 🔢 Example Grid for Visualization

We'll use a simple 5x5 grid (coordinates as `(row, col)`):

```
r
CopyEdit
S . . . .
. # # . .
. . . # .
. # . . .
. . . . T

```

- `S` = Start (0,0)
- `T` = Target (4,4)
- `.` = Walkable path
- `#` = Wall/Blocked
- Each valid move has weight `1` unless specified
- Edges: only 4 directions (up/down/left/right)

---

## 1️⃣ BFS (Unweighted Graph – Grid)

### ✅ **Use case**: Find shortest path in unweighted graph.

### 🔁 Visualization Steps

- Starts from `S = (0,0)`
- Visits neighbors in wave-like fashion (level by level)
- Explores all shortest paths simultaneously

**Visited Order Example**:

```
scss
CopyEdit
(0,0)
↓
(0,1), (1,0)
↓
(0,2), (2,0)
↓
(0,3), (2,1), (3,0)
...
Ends at (4,4)

```

### ✅ Java Code:

```java

class Solution {
    static class Cell {
        int row, col, dist;
        Cell(int r, int c, int d) { row = r; col = c; dist = d; }
    }

    public int bfsShortestPath(char[][] grid) {
        int m = grid.length, n = grid[0].length;
        boolean[][] visited = new boolean[m][n];
        int[][] dirs = {{0,1},{1,0},{0,-1},{-1,0}};
        Queue<Cell> q = new LinkedList<>();
        q.offer(new Cell(0, 0, 0));
        visited[0][0] = true;

        while (!q.isEmpty()) {
            Cell cur = q.poll();
            if (grid[cur.row][cur.col] == 'T') return cur.dist;

            for (int[] dir : dirs) {
                int nr = cur.row + dir[0], nc = cur.col + dir[1];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                    grid[nr][nc] != '#' && !visited[nr][nc]) {
                    visited[nr][nc] = true;
                    q.offer(new Cell(nr, nc, cur.dist + 1));
                }
            }
        }
        return -1; // Not reachable
    }
}

```

---

## 2️⃣ Dijkstra’s Algorithm (Weighted Positive Graph)

### ✅ Use case: Find shortest path with **positive weights**

### 🔁 Visualization Steps

- Always picks the **least-cost node** from a min-heap
- Visits neighbors and updates shortest distance
- Uses a `priority queue`

**Visited Order (Based on cost)**:

```
scss
CopyEdit
(0,0) → (0,1) [cost 1] → (1,0) [cost 1]
→ (2,0) [cost 2] → ...
→ (4,4) [cost 8]

```

### ✅ Java Code:

```java
class Solution {
    static class Cell {
        int row, col, dist;
        Cell(int r, int c, int d) { row = r; col = c; dist = d; }
    }

    public int dijkstra(char[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dist = new int[m][n];
        for (int[] d : dist) Arrays.fill(d, Integer.MAX_VALUE);
        PriorityQueue<Cell> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a.dist));
        pq.offer(new Cell(0, 0, 0));
        dist[0][0] = 0;

        int[][] dirs = {{0,1},{1,0},{0,-1},{-1,0}};
        while (!pq.isEmpty()) {
            Cell cur = pq.poll();
            if (grid[cur.row][cur.col] == 'T') return cur.dist;

            for (int[] dir : dirs) {
                int nr = cur.row + dir[0], nc = cur.col + dir[1];
                if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                    grid[nr][nc] != '#') {
                    int newDist = cur.dist + 1; // all weights = 1
                    if (newDist < dist[nr][nc]) {
                        dist[nr][nc] = newDist;
                        pq.offer(new Cell(nr, nc, newDist));
                    }
                }
            }
        }
        return -1;
    }
}

```

---

## 3️⃣ Bellman-Ford (Can handle negative weights)

### ✅ Use case: Graph with negative edges (not cycles)

### 🔁 Visualization:

- Iterates `V-1` times over all edges.
- For each edge: relax the distance.

On each iteration:

```
dist[v] = Math.min(dist[v], dist[u] + weight);

```

### ✅ Java Code (Adjacency List Based):

```java
public int bellmanFord(int V, int[][] edges, int src) {
    int[] dist = new int[V];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[src] = 0;

    for (int i = 0; i < V - 1; i++) {
        for (int[] edge : edges) {
            int u = edge[0], v = edge[1], w = edge[2];
            if (dist[u] != Integer.MAX_VALUE && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
            }
        }
    }
    return dist[V - 1]; // distance to last node
}

```

---

## 4️⃣ Floyd-Warshall (All-pairs shortest path)

### ✅ Use case: Dense graph, all-pairs paths

### 🔁 Visualization:

Uses dynamic programming:

For every vertex `k`, updates:

```

dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j])

```

### ✅ Java Code:

```java
public void floydWarshall(int[][] graph) {
    int V = graph.length;
    int[][] dist = new int[V][V];

    for (int i = 0; i < V; i++)
        for (int j = 0; j < V; j++)
            dist[i][j] = graph[i][j];

    for (int k = 0; k < V; k++)
        for (int i = 0; i < V; i++)
            for (int j = 0; j < V; j++)
                if (dist[i][k] != Integer.MAX_VALUE && dist[k][j] != Integer.MAX_VALUE)
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
}

```

---

## 5️⃣ 0-1 BFS (only weights 0 or 1)

### ✅ Use case: Fast shortest path when edges are 0 or 1

### 🔁 Visualization:

- Uses **deque** (not queue)
- Add to **front** if weight is `0`, **back** if weight is `1`

### ✅ Java Code:

```java
public int zeroOneBFS(List<List<int[]>> graph, int V, int start) {
    Deque<int[]> dq = new ArrayDeque<>();
    int[] dist = new int[V];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dq.offerFirst(new int[]{start, 0});
    dist[start] = 0;

    while (!dq.isEmpty()) {
        int[] cur = dq.pollFirst();
        int u = cur[0], d = cur[1];

        for (int[] edge : graph.get(u)) {
            int v = edge[0], w = edge[1];
            if (dist[v] > d + w) {
                dist[v] = d + w;
                if (w == 0)
                    dq.offerFirst(new int[]{v, dist[v]});
                else
                    dq.offerLast(new int[]{v, dist[v]});
            }
        }
    }
    return dist[V - 1];
}

```
