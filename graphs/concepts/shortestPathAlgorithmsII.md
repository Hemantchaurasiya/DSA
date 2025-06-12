# Shortest Path Algorithms

# 📏 **Shortest Path in an Unweighted Graph using BFS**

---

## 🧠 Core Idea:

If all edges have **equal weight** (like 1), then **Breadth-First Search (BFS)** guarantees the shortest path in terms of the **number of edges** from the source node.

Why?

- BFS visits nodes **level by level**, so the **first time** we reach a node, it’s via the **shortest path** from the source.

---

## ✅ Use Cases:

- Unweighted roads (e.g., shortest path in a maze/grid).
- Social networks (minimum degree of separation).
- Word ladder problems.

---

## 🔄 Algorithm Steps:

1. Initialize:
    - `distance[] = ∞` for all nodes.
    - Set `distance[source] = 0`.
    - Use a `Queue` to perform BFS.
2. Start BFS from source.
3. For each neighbor:
    - If `distance[neighbor] == ∞` (not visited):
        - Update: `distance[neighbor] = distance[current] + 1`
        - Enqueue the neighbor.
4. Repeat until the queue is empty.

---

## 💻 Java Code:

```java
java
CopyEdit
void bfsShortestPath(int V, List<List<Integer>> adj, int source) {
    int[] distance = new int[V];
    Arrays.fill(distance, Integer.MAX_VALUE);
    distance[source] = 0;

    Queue<Integer> queue = new LinkedList<>();
    queue.offer(source);

    while (!queue.isEmpty()) {
        int node = queue.poll();

        for (int neighbor : adj.get(node)) {
            if (distance[neighbor] == Integer.MAX_VALUE) {
                distance[neighbor] = distance[node] + 1;
                queue.offer(neighbor);
            }
        }
    }

    // Print shortest distances
    for (int i = 0; i < V; i++) {
        System.out.println("Shortest path from " + source + " to " + i + " is: " + distance[i]);
    }
}

```

---

## 🔍 Dry Run Example:

### 🔹 Graph:

```
makefile
CopyEdit
Edges: 0—1, 0—2, 1—3, 2—4, 3—5, 4—5

```

```
csharp
CopyEdit
adj = [
  [1, 2],     // 0
  [0, 3],     // 1
  [0, 4],     // 2
  [1, 5],     // 3
  [2, 5],     // 4
  [3, 4]      // 5
]

```

### 🎯 Start BFS from `source = 0`:

```
CopyEdit
distance[] = [0,1,1,2,2,3]

```

Explanation:

- 0 → 1 (1 step)
- 0 → 2 (1 step)
- 0 → 1 → 3 (2 steps)
- 0 → 2 → 4 (2 steps)
- 0 → 2 → 4 → 5 or 0 → 1 → 3 → 5 (3 steps)

---

## ⏱️ Time & Space Complexity:

| Operation | Complexity |
| --- | --- |
| Time | O(V + E) |
| Space | O(V) |

Efficient and ideal for unweighted graphs!

---

## ✅ Summary:

| Graph Type | Best Approach | Why? |
| --- | --- | --- |
| Unweighted | BFS | Level-order → shortest |
| Weighted | Dijkstra / Bellman-Ford | We need to compare weights |

# 🧠 **Dijkstra’s Algorithm** – *Shortest Path in Weighted Graphs (Non-Negative Weights)*

---

## 🧠 Core Idea:

Dijkstra’s Algorithm finds the **shortest path from a source node** to **all other nodes** in a **weighted graph with non-negative edge weights**.

> ✅ Greedy Approach
> 
> 
> ✅ Uses **PriorityQueue (Min-Heap)** to always expand the next node with the smallest known distance.
> 

---

## 🔍 Use Cases:

- GPS Navigation Systems
- Network Routing Protocols
- Maps / Game AI Pathfinding (without negative weights)

---

## 📦 Works For:

- Directed or undirected graphs
- Edge weights must be **non-negative**

---

## 🔄 Algorithm Steps:

1. Initialize a `distance[]` array with `∞`, and `distance[source] = 0`.
2. Use a **Min Heap (PriorityQueue)** storing pairs `(distance, node)`.
3. While the heap is not empty:
    - Extract the node with the **minimum distance**.
    - For each neighbor:
        - If `distance[neighbor] > distance[curr] + weight`:
            - Update `distance[neighbor]`
            - Add `(newDistance, neighbor)` to the heap.

---

## 💻 Java Code:

```java
java
CopyEdit
class Pair {
    int node;
    int weight;

    Pair(int node, int weight) {
        this.node = node;
        this.weight = weight;
    }
}

void dijkstra(int V, List<List<Pair>> adj, int source) {
    int[] distance = new int[V];
    Arrays.fill(distance, Integer.MAX_VALUE);
    distance[source] = 0;

    PriorityQueue<Pair> pq = new PriorityQueue<>((a, b) -> a.weight - b.weight);
    pq.offer(new Pair(source, 0));

    while (!pq.isEmpty()) {
        Pair curr = pq.poll();
        int u = curr.node;

        for (Pair neighbor : adj.get(u)) {
            int v = neighbor.node;
            int w = neighbor.weight;

            if (distance[v] > distance[u] + w) {
                distance[v] = distance[u] + w;
                pq.offer(new Pair(v, distance[v]));
            }
        }
    }

    // Output shortest distances
    for (int i = 0; i < V; i++) {
        System.out.println("Shortest path from " + source + " to " + i + " is: " + distance[i]);
    }
}

```

---

## 🔍 Dry Run Example

### 🔹 Graph:

```
makefile
CopyEdit
Edges:
0 → 1 (4)
0 → 2 (1)
2 → 1 (2)
1 → 3 (1)
2 → 3 (5)

```

### 🎯 Starting from source = 0

Initial:

```
CopyEdit
distance[] = [0, ∞, ∞, ∞]

```

Steps:

- 0 → 2 (distance = 1)
- 2 → 1 (distance = 3)
- 1 → 3 (distance = 4)
- 2 → 3 = 1 + 5 = 6 (not better)

✅ Final `distance[] = [0, 3, 1, 4]`

---

## ⏱️ Time and Space Complexity

| Operation | Complexity |
| --- | --- |
| Time (Adj List + PQ) | **O((V + E) log V)** |
| Space | O(V + E) |

---

## ✅ Summary

| Feature | Dijkstra’s Algorithm |
| --- | --- |
| Handles Negative Weights | ❌ |
| Greedy | ✅ |
| Efficient with Heap | ✅ |
| Graph Type | Weighted (non-negative) |

# 🔁 **Bellman-Ford Algorithm** – *Shortest Path with Negative Weights*

---

## 🧠 Core Idea:

Bellman-Ford finds the **shortest paths** from a single source to all other vertices **even when edges have negative weights**.

Unlike Dijkstra’s (which is greedy), Bellman-Ford is a **dynamic programming** approach:

> It relaxes all edges repeatedly — up to V-1 times.
> 

> ✅ Handles negative weights
> 
> 
> ❌ Fails on **negative weight cycles** (detects them)
> 

---

## 🔍 Use Cases:

- Currency arbitrage (negative weights = profitable trades)
- Routing protocols (e.g., RIP in networking)
- General graphs with negative edges (but not negative cycles)

---

## 🔄 Algorithm Steps:

1. Initialize `distance[]` = ∞ for all nodes, `distance[source] = 0`.
2. Repeat **V−1 times** (V = #vertices):
    - For each edge `(u, v, weight)`:
        - If `distance[u] + weight < distance[v]` → **update** distance.
3. Do **1 more pass** over all edges to check for **negative cycles**:
    - If `distance[u] + weight < distance[v]`, a **negative cycle** exists.

---

## 💻 Java Code:

```java
java
CopyEdit
class Edge {
    int u, v, weight;
    Edge(int u, int v, int weight) {
        this.u = u;
        this.v = v;
        this.weight = weight;
    }
}

void bellmanFord(int V, List<Edge> edges, int source) {
    int[] distance = new int[V];
    Arrays.fill(distance, Integer.MAX_VALUE);
    distance[source] = 0;

    // Step 1: Relax all edges V-1 times
    for (int i = 0; i < V - 1; i++) {
        for (Edge edge : edges) {
            if (distance[edge.u] != Integer.MAX_VALUE &&
                distance[edge.u] + edge.weight < distance[edge.v]) {
                distance[edge.v] = distance[edge.u] + edge.weight;
            }
        }
    }

    // Step 2: Check for negative-weight cycles
    for (Edge edge : edges) {
        if (distance[edge.u] != Integer.MAX_VALUE &&
            distance[edge.u] + edge.weight < distance[edge.v]) {
            System.out.println("❌ Negative weight cycle detected");
            return;
        }
    }

    // Print shortest distances
    for (int i = 0; i < V; i++) {
        System.out.println("Shortest path from " + source + " to " + i + " is: " + distance[i]);
    }
}

```

---

## 🔍 Dry Run Example

### 🔹 Graph:

```
makefile
CopyEdit
Edges:
0 → 1 (4)
0 → 2 (5)
1 → 2 (-3)
2 → 3 (2)

```

### 🎯 Start from source = 0

| Pass | Updated Distances |
| --- | --- |
| Init | [0, ∞, ∞, ∞] |
| 1 | [0, 4, 1, ∞] |
| 2 | [0, 4, 1, 3] |
| 3 | [0, 4, 1, 3] ← no changes |

✅ Final distances: `[0, 4, 1, 3]`

❌ If another edge 3 → 1 (−6) is added, it would create a negative cycle → detected in final check.

---

## ⏱️ Time and Space Complexity

| Operation | Complexity |
| --- | --- |
| Time | O(V × E) |
| Space | O(V) |

> Slower than Dijkstra, but more powerful in capability.
> 

---

## ✅ Summary:

| Feature | Bellman-Ford |
| --- | --- |
| Negative weights | ✅ Yes |
| Negative weight cycles | ❌ No (but detectable) |
| Graph type | Directed / Undirected |
| Strategy | Dynamic Programming |
| Shortest Path Guarantee | Yes, if no negative cycle |

# 🔄 **Floyd-Warshall Algorithm** – *All-Pairs Shortest Path (APSP)*

---

## 🧠 Core Idea:

Floyd-Warshall computes the **shortest distance between every pair of vertices** in a weighted graph — even with **negative weights** (as long as there’s no negative weight cycle).

> ✅ Uses Dynamic Programming
> 
> 
> ✅ Works for **directed/undirected**, **dense graphs**, **negative weights**
> 

---

## 🔍 Use Cases:

- Network routing tables (all-to-all routes)
- Transitive closure in graphs
- Game maps (preprocessing movement cost between all points)

---

## ⚙️ Concept:

Let:

- `dist[i][j]` = shortest distance from `i` to `j`
- Initialize:
    - `dist[i][j] = weight(i→j)` if edge exists
    - `dist[i][j] = ∞` if no edge
    - `dist[i][i] = 0` (distance to itself)

The main idea:

> Try every vertex k as an intermediate point:
> 

```
dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])
```

Repeat this for **all triples (i, j, k)**.

---

## 💻 Java Code:

```java
void floydWarshall(int[][] graph) {
    int V = graph.length;
    int[][] dist = new int[V][V];

    // Step 1: Initialize distances
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            dist[i][j] = graph[i][j];
        }
    }

    // Step 2: Floyd-Warshall core loop
    for (int k = 0; k < V; k++) {
        for (int i = 0; i < V; i++) {
            for (int j = 0; j < V; j++) {
                if (dist[i][k] != Integer.MAX_VALUE && dist[k][j] != Integer.MAX_VALUE) {
                    dist[i][j] = Math.min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }

    // Step 3: Check for negative weight cycle
    for (int i = 0; i < V; i++) {
        if (dist[i][i] < 0) {
            System.out.println("❌ Negative weight cycle detected");
            return;
        }
    }

    // Print final distances
    for (int i = 0; i < V; i++) {
        for (int j = 0; j < V; j++) {
            System.out.print((dist[i][j] == Integer.MAX_VALUE ? "∞" : dist[i][j]) + "\t");
        }
        System.out.println();
    }
}
```

---

## 📥 Input Example:

### Graph (adjacency matrix):

```
css
CopyEdit
   0   1   2   3
0 [0,  5, ∞, 10]
1 [∞,  0,  3, ∞]
2 [∞, ∞,  0,  1]
3 [∞, ∞, ∞,  0]

```

### Final dist[][]:

```
CopyEdit
0   5   8   9
∞   0   3   4
∞  ∞   0   1
∞  ∞  ∞   0

```

---

## ⏱️ Time and Space Complexity:

| Operation | Complexity |
| --- | --- |
| Time | **O(V³)** |
| Space | **O(V²)** |

> ⚠️ Not suitable for large sparse graphs (use Dijkstra’s or Johnson’s for those)
> 

---

## ✅ Summary:

| Feature | Floyd-Warshall |
| --- | --- |
| Graph Type | Weighted, directed/undirected |
| Handles Negative Weights | ✅ Yes |
| Negative Cycle Detection | ✅ Yes |
| Negative Cycle Support | ❌ No |
| Use Case | All-pairs shortest paths |
| Time Complexity | O(V³) |

---

### 🔄 Comparison Chart

| Algorithm | Handles Negative Weights? | Handles Negative Cycles? | Use Case |
| --- | --- | --- | --- |
| Dijkstra | ❌ | ❌ | Single-source shortest |
| Bellman-Ford | ✅ | ❌ (but detects) | Single-source shortest |
| Floyd-Warshall | ✅ | ❌ (but detects) | All-pairs shortest paths |