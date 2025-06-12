
# 🔶 1. Breadth First Search (BFS) – Adjacency List

### 🧠 Core Idea:

- Explore all **neighbors first**, level by level.
- Implemented using a **queue**.
- Use a `visited[]` array.

---

### 💻 Java Code – BFS:

```java
java
CopyEdit
void bfs(int start, List<List<Integer>> adj, boolean[] visited) {
    Queue<Integer> q = new LinkedList<>();
    q.offer(start);
    visited[start] = true;

    while (!q.isEmpty()) {
        int node = q.poll();
        System.out.print(node + " ");

        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                q.offer(neighbor);
            }
        }
    }
}

```

---

### 🧪 Dry Run:

Graph: `0 → 1, 0 → 2, 1 → 3`

Adjacency List:

```
csharp
CopyEdit
adj = [
  [1, 2],   // 0
  [3],      // 1
  [],       // 2
  []        // 3
]

```

Call: `bfs(0, adj, visited)`

```
less
CopyEdit
Queue steps: [0] → [1,2] → [2,3] → [3]
Output: 0 1 2 3

```

---

### 🖼️ Diagram:

```
CopyEdit
0 → 1 → 3
 \
  → 2

```

---

### ⏱️ Time & Space:

- **Time**: O(V + E)
- **Space**: O(V) (queue + visited)

---

## 📌 Summary Table

| Traversal | Data Structure | Type | When to Use |
| --- | --- | --- | --- |
| DFS | Stack / Recursion | Deep-first | Topological Sort, Cycles |
| BFS | Queue | Level-wise | Shortest Path (Unweighted) |

# 🔶 2. BFS using Adjacency Matrix

### 💻 Java Code – BFS:

```java
void bfs(int start, int[][] graph, boolean[] visited) {
    Queue<Integer> queue = new LinkedList<>();
    queue.offer(start);
    visited[start] = true;

    while (!queue.isEmpty()) {
        int node = queue.poll();
        System.out.print(node + " ");

        for (int v = 0; v < graph.length; v++) {
            if (graph[node][v] > 0 && !visited[v]) {
                visited[v] = true;
                queue.offer(v);
            }
        }
    }
}

```

---

### 🔍 Example Dry Run:

For matrix:

```
graph = [
  [0, 1, 1, 0],
  [0, 0, 1, 1],
  [0, 0, 0, 1],
  [0, 0, 0, 0]
]
```

**Edges**: 0→1, 0→2, 1→2, 1→3, 2→3

- DFS(0): `0 1 2 3`
- BFS(0): `0 1 2 3`

---

### 🕐 Time & Space Complexity:

- **Time**: O(V²)
- **Space**: O(V) + recursion stack or queue

---

# 🔶 3. BFS using Edge List

### 💻 Java Code:

```java
void bfsEdgeList(int start, List<Edge> edges, boolean[] visited) {
    Queue<Integer> queue = new LinkedList<>();
    queue.offer(start);
    visited[start] = true;

    while (!queue.isEmpty()) {
        int node = queue.poll();
        System.out.print(node + " ");

        for (Edge e : edges) {
            if (e.src == node && !visited[e.dest]) {
                visited[e.dest] = true;
                queue.offer(e.dest);
            }
        }
    }
}
```

---

### 🔍 Dry Run Example:

Edge List:

```java
edges = [
  (0, 1), (0, 2),
  (1, 2), (1, 3),
  (2, 3)
]
```

- DFS(0): `0 1 2 3`
- BFS(0): `0 1 2 3`

---

### 🕐 Time & Space Complexity:

- **Time**: O(V + E × V) – inefficient for dense graphs
- **Space**: O(V)

---

## 🧠 When to Use What?

| Graph Type | DFS/BFS Efficiency | Notes |
| --- | --- | --- |
| **Adjacency List** | ⭐ Best (O(V + E)) | Recommended for all graph traversal |
| **Adjacency Matrix** | O(V²) | Simple but heavy for sparse graphs |
| **Edge List** | 🚫 Least Efficient | Used in Kruskal, MST, not BFS/DFS |