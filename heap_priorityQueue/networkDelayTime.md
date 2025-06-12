# Network Delay Time

This problem is a classic example of shortest path algorithms in weighted graphs. The goal is to determine the time it takes for a signal to reach all nodes in a network starting from a source node.

---

## Problem Definition:
Given a directed, weighted graph represented by `times`, where each element is `[u, v, w]` indicating an edge from `u` to `v` with weight `w`, and an integer `k` representing the starting node, find the minimum time for all nodes to receive the signal. If it's impossible for all nodes to receive the signal, return `-1`.

---

## Approach 1: Brute Force (Not practical for large graphs)

### Core Idea:
Attempt to simulate signal propagation to all nodes repeatedly, updating the shortest time for each node until no improvements are possible.

### Algorithm:
- Initialize an array `dist` with infinity for all nodes except the source `k`, which is 0.
- Repeat relaxation over all edges multiple times (like Bellman-Ford):
  - For each edge `[u, v, w]`, if `dist[u] + w < dist[v]`, update `dist[v]`.
- After `n-1` iterations, check if all nodes are reachable (`dist[i] != infinity`).

### Java Code:
```java
public int networkDelayTimeBruteForce(int[][] times, int n, int k) {
    int[] dist = new int[n + 1];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[k] = 0;

    for (int i = 0; i < n - 1; i++) {
        boolean updated = false;
        for (int[] edge : times) {
            int u = edge[0], v = edge[1], w = edge[2];
            if (dist[u] != Integer.MAX_VALUE && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                updated = true;
            }
        }
        if (!updated) break; // No update in this iteration, early stop
    }

    int maxTime = 0;
    for (int i = 1; i <= n; i++) {
        if (dist[i] == Integer.MAX_VALUE) return -1;
        maxTime = Math.max(maxTime, dist[i]);
    }
    return maxTime;
}
```

### Complexity Analysis:
- **Time Complexity:** `O(n * E)`, where `E` is the number of edges, due to repeated relaxation.
- **Space Complexity:** `O(n)`, for the distance array.

### Dry Run:
Suppose `times = [[2,1,1],[2,3,1],[3,4,1]]`, `n=4`, `k=2`.
- Initialize `dist = [∞, 0, ∞, ∞, ∞]`.
- Relax edges:
  - After first iteration:
    - Update `dist[1]` to 1 (from 2→1).
    - Update `dist[3]` to 2 (2→3).
    - Update `dist[4]` to 3 (3→4).
- Repeat until no updates or `n-1` iterations.
- Final `dist`: `[∞, 1, 0, 2, 3]`.
- Result: max time = 3.

---

## Approach 2: Dijkstra's Algorithm (Using Priority Queue)

### Core Idea:
Find the shortest path from the source node to all other nodes using a greedy approach with a min-heap (priority queue). Efficiently updates the shortest distance to each node.

### Algorithm:
1. Build an adjacency list from the `times` array.
2. Initialize a min-heap (priority queue) with `(distance=0, node=k)`.
3. Initialize a `dist` array with infinity, set `dist[k]=0`.
4. While the priority queue is not empty:
   - Extract the node with the smallest current distance.
   - For each neighbor:
     - If `current_distance + weight < dist[neighbor]`, update `dist[neighbor]` and add it to the queue.
5. After processing, check if all nodes are reachable (`dist[i] != infinity`). Return the maximum distance or `-1`.

### Java Code:
```java
import java.util.*;

public int networkDelayTimeDijkstra(int[][] times, int n, int k) {
    // Build adjacency list
    Map<Integer, List<int[]>> graph = new HashMap<>();
    for (int[] time : times) {
        graph.computeIfAbsent(time[0], x -> new ArrayList<>()).add(new int[]{time[1], time[2]});
    }

    int[] dist = new int[n + 1];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[k] = 0;

    PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
    pq.offer(new int[]{0, k}); // {distance, node}

    while (!pq.isEmpty()) {
        int[] current = pq.poll();
        int currDist = current[0];
        int node = current[1];

        if (currDist > dist[node]) continue;

        if (graph.containsKey(node)) {
            for (int[] neighbor : graph.get(node)) {
                int v = neighbor[0], w = neighbor[1];
                if (dist[node] + w < dist[v]) {
                    dist[v] = dist[node] + w;
                    pq.offer(new int[]{dist[v], v});
                }
            }
        }
    }

    int maxTime = 0;
    for (int i = 1; i <= n; i++) {
        if (dist[i] == Integer.MAX_VALUE) return -1;
        maxTime = Math.max(maxTime, dist[i]);
    }
    return maxTime;
}
```

### Complexity Analysis:
- **Time Complexity:** `O((V + E) log V)` because each node is pushed into the priority queue at most once, and each edge is considered once.
- **Space Complexity:** `O(V + E)` for adjacency list and `O(V)` for distance and priority queue.

### Dry Run:
Using the same example:
- Start at node 2 with distance 0.
- Update neighbors:
  - `dist[1] = 1`, push `(1,1)`.
  - `dist[3] = 1`, push `(1,3)`.
  - `dist[4] = 2`, push `(2,4)`.
- Extract min:
  - From `(1,1)`, no further updates.
  - From `(1,3)`, update `dist[4]` if better.
- Final distances: `[∞, 1, 0, 1, 2]`.
- Result: max time = 2.

---

## Approach 3: Bellman-Ford Algorithm (Optional, less efficient)

### Core Idea:
Iteratively relax all edges, similar to brute force but more structured, to find the shortest paths.

### Algorithm:
- Initialize `dist` with infinity except source `k`.
- Relax all edges `n-1` times.
- Check for unreachable nodes.

*(Similar to Approach 1 but more formal)*

---

## Summary:

| Approach | Best For | Time Complexity | Space Complexity | Notes |
|------------|------------|------------------|------------------|--------|
| Brute Force (Bellman-Ford) | Small graphs | `O(n * E)` | `O(n)` | Not efficient for large graphs |
| Dijkstra with Priority Queue | Large graphs, positive weights | `O((V + E) log V)` | `O(V + E)` | Most optimal for positive weighted graphs |

---

## Final Tips:
- Use **Dijkstra's Algorithm** for positive edge weights for optimal performance.
- Use **adjacency list** for efficient graph traversal.
- Always check for unreachable nodes to return `-1`.
- Practice dry runs with sample inputs to understand variable updates.

---

**Happy coding!**
