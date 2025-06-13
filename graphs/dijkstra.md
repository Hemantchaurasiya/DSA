# Dijkstra's Algorithm

---

## Problem Overview
Given a **weighted graph** with **non-negative edge weights**, find the **shortest distance from a source vertex to all other vertices**. Typically, the problem is to compute the shortest path from a starting node to a specific target node, but Dijkstra's algorithm provides the shortest path to all nodes efficiently.

---

## Approach 1: Brute Force (Naive Implementation)

### **Core Idea:**
Repeatedly select the unvisited vertex with the smallest known distance, then update the distances of its neighbors. This process continues until all vertices are processed.

### **Algorithm:**
1. Initialize a `distance[]` array with infinity for all nodes except the source (set to 0).
2. Maintain a `visited[]` array to track processed nodes.
3. For each iteration:
   - Pick the unvisited node with the smallest `distance[]`.
   - Mark it as visited.
   - Update the `distance[]` for all its neighbors if a shorter path is found through this node.
4. Repeat until all nodes are visited or the smallest distance among unvisited nodes is infinity.

---

### **Java Code:**
```java
import java.util.Arrays;

public class DijkstraBruteForce {
    public int[] shortestPath(int[][] graph, int src) {
        int n = graph.length;
        int[] dist = new int[n];
        boolean[] visited = new boolean[n];

        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[src] = 0;

        for (int count = 0; count < n - 1; count++) {
            int u = minDistance(dist, visited);
            visited[u] = true;

            for (int v = 0; v < n; v++) {
                if (!visited[v] && graph[u][v] != 0 &&
                    dist[u] != Integer.MAX_VALUE &&
                    dist[u] + graph[u][v] < dist[v]) {
                    dist[v] = dist[u] + graph[u][v];
                }
            }
        }
        return dist;
    }

    private int minDistance(int[] dist, boolean[] visited) {
        int min = Integer.MAX_VALUE, minIndex = -1;
        for (int v = 0; v < dist.length; v++) {
            if (!visited[v] && dist[v] <= min) {
                min = dist[v];
                minIndex = v;
            }
        }
        return minIndex;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** `O(n^2)` because, for each of the `n` vertices, finding the minimum distance node takes `O(n)`.
- **Space Complexity:** `O(n)` for `dist[]` and `visited[]`.

---

### **Dry Run:**
**Input:**
```plaintext
graph = [
  [0, 4, 0, 0, 0, 0, 0, 8],
  [4, 0, 8, 0, 0, 0, 0, 11],
  [0, 8, 0, 7, 0, 4, 0, 0],
  [0, 0, 7, 0, 9, 14, 0, 0],
  [0, 0, 0, 9, 0, 10, 0, 0],
  [0, 0, 4, 14, 10, 0, 2, 0],
  [0, 0, 0, 0, 0, 2, 0, 1],
  [8, 11, 0, 0, 0, 0, 1, 0]
]
src = 0
```

- Initially, `dist[] = [0, ∞, ∞, ∞, ∞, ∞, ∞, ∞]`.
- Iteration 1: pick node 0, update neighbors.
- Iteration 2: pick node 1 (distance 4), update neighbors.
- Continue until all nodes are processed.

---

## Approach 2: Using Min-Heap (Priority Queue) — **Optimized Implementation**

### **Core Idea:**
Use a min-priority queue (heap) to efficiently select the next closest unvisited node, reducing the selection time from `O(n)` to `O(log n)` per operation.

### **Algorithm:**
1. Initialize `dist[]` array with infinity, `dist[src] = 0`.
2. Use a min-heap (priority queue) to store `(distance, node)`.
3. Insert `(0, src)` into the priority queue.
4. While the priority queue is not empty:
   - Extract the node with the smallest distance.
   - For each neighbor:
     - If the path through this node offers a shorter distance, update `dist[]` and add the neighbor to the queue.
5. Continue until all nodes are processed or the queue is empty.

---

### **Java Code:**
```java
import java.util.*;

public class DijkstraOptimized {
    public int[] shortestPath(int[][] graph, int src) {
        int n = graph.length;
        int[] dist = new int[n];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[src] = 0;

        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        pq.offer(new int[]{0, src});

        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            int currentDist = current[0];
            int u = current[1];

            if (currentDist > dist[u]) continue;

            for (int v = 0; v < n; v++) {
                if (graph[u][v] != 0) {
                    int newDist = dist[u] + graph[u][v];
                    if (newDist < dist[v]) {
                        dist[v] = newDist;
                        pq.offer(new int[]{newDist, v});
                    }
                }
            }
        }
        return dist;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** `O((V + E) log V)` — Each node inserted into the priority queue, and edges are relaxed.
- **Space Complexity:** `O(V + E)` for storing the graph, `O(V)` for `dist[]` and the queue.

---

## **Summary Table**

| Approach | Data Structure | Time Complexity | Space Complexity | Notes |
|------------|-----------------|---------------------|-----------------------|--------|
| Naive | Arrays, Loops | `O(n^2)` | `O(n)` | Suitable for small graphs |
| Optimized with Min-Heap | Priority Queue | `O((V + E) log V)` | `O(V + E)` | Efficient for large graphs |

---

## Final Notes:
- Dijkstra's algorithm **requires non-negative weights**.
- For graphs with negative weights, algorithms like **Bellman-Ford** are used.
- The main optimization is using a **priority queue** to efficiently pick the next closest vertex.

This structured guide should help you understand, implement, and optimize Dijkstra's algorithm effectively during interviews and revision sessions.
