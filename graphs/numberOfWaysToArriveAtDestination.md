# Number of Ways to Arrive at Destination

---

## Problem Overview
Given a directed graph with `n` nodes numbered from `0` to `n-1`, and a list of edges, determine **the number of different shortest paths** from a source node `start` to a destination node `end`. The result should be modulo `10^9 + 7`.

---

## Approach 1: Brute Force (DFS with Path Enumeration)

### **Core Idea:**
Explore all possible paths from `start` to `end` and count the number of paths that have the shortest total distance. This approach is highly inefficient because it explores all paths, leading to exponential complexity.

### **Algorithm:**
1. Build an adjacency list from the edges.
2. Use DFS to explore all paths from `start` to `end`.
3. Maintain:
   - A variable `shortestDistance` to keep track of the shortest path found so far.
   - A counter `ways` to count how many shortest paths exist.
4. For each path:
   - If the total distance exceeds the current shortest, prune.
   - If the total distance equals the shortest, increment `ways`.
5. Return the count of shortest paths.

---

### **Java Code:**
```java
import java.util.*;

public class NumberOfWaysBruteForce {
    private long shortestDistance = Long.MAX_VALUE;
    private int ways = 0;
    private final long MOD = 1_000_000_007;

    public int countPaths(int n, int[][] edges, int start, int end) {
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] edge : edges) {
            adj.get(edge[0]).add(new int[]{edge[1], edge[2]});
        }
        dfs(adj, start, end, 0, 0);
        return ways;
    }

    private void dfs(List<List<int[]>> adj, int current, int end, long dist, long shortestDist) {
        if (dist > shortestDistance) return; // prune paths longer than shortest
        if (current == end) {
            if (dist < shortestDistance) {
                shortestDistance = dist;
                ways = 1;
            } else if (dist == shortestDistance) {
                ways = (ways + 1) % MOD;
            }
            return;
        }
        for (int[] neighbor : adj.get(current)) {
            dfs(adj, neighbor[0], end, dist + neighbor[1], shortestDist);
        }
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** Exponential, **O(2^E)** in the worst case, because exploring all paths is exponential.
- **Space Complexity:** `O(n)` for recursion stack, plus adjacency list storage.

---

### **Dry Run:**
Suppose:
```plaintext
n=3, edges = [[0,1,1],[0,2,1],[1,2,1]], start=0, end=2
```

- Paths:
  - 0 -> 2 (distance 1)
  - 0 -> 1 -> 2 (distance 2)
- Shortest distance: 1
- Ways: 1 (only path 0->2)

---

## Approach 2: Using Dijkstra's Algorithm with Path Counting — **Optimized Solution**

### **Core Idea:**
Leverage a shortest path algorithm (Dijkstra's) to find the shortest distances from `start` to all nodes, and simultaneously count the number of shortest paths to each node.

### **Algorithm:**
1. Build an adjacency list from edges.
2. Initialize:
   - `dist[]` array with infinity, `dist[start] = 0`.
   - `ways[]` array with 0, `ways[start] = 1`.
3. Use a min-heap (priority queue) to select the node with the smallest current distance.
4. For each neighbor `(v, w)` of the current node:
   - If `dist[current] + w < dist[v]`:
     - Update `dist[v]` and set `ways[v] = ways[current]`.
   - Else if `dist[current] + w == dist[v]`:
     - Add `ways[current]` to `ways[v]`.
5. Continue until all nodes are processed.
6. Return `ways[end]` modulo `10^9 + 7`.

---

### **Java Code:**
```java
import java.util.*;

public class NumberOfWaysDijkstra {
    private static final int MOD = 1_000_000_007;

    public int countPaths(int n, int[][] edges, int start, int end) {
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] edge : edges) {
            adj.get(edge[0]).add(new int[]{edge[1], edge[2]});
        }

        long[] dist = new long[n];
        Arrays.fill(dist, Long.MAX_VALUE);
        int[] ways = new int[n];
        dist[start] = 0;
        ways[start] = 1;

        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingLong(a -> a[0]));
        pq.offer(new int[]{0, start});

        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            long currentDist = current[0];
            int u = current[1];

            if (currentDist > dist[u]) continue;

            for (int[] neighbor : adj.get(u)) {
                int v = neighbor[0], w = neighbor[1];
                long newDist = currentDist + w;
                if (newDist < dist[v]) {
                    dist[v] = newDist;
                    ways[v] = ways[u];
                    pq.offer(new int[]{(int) newDist, v});
                } else if (newDist == dist[v]) {
                    ways[v] = (ways[v] + ways[u]) % MOD;
                }
            }
        }
        return dist[end] == Long.MAX_VALUE ? 0 : ways[end];
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** `O((V + E) log V)` due to Dijkstra's algorithm with a priority queue.
- **Space Complexity:** `O(V + E)` for adjacency list, `O(V)` for `dist` and `ways` arrays.

---

## **Summary Table**

| Approach | Data Structure | Time Complexity | Space Complexity | Notes |
|------------|-----------------|---------------------|-----------------------|--------|
| DFS Path Enumeration | Recursion + Adjacency List | Exponential | `O(n + E)` | Not feasible for large graphs |
| Dijkstra with Path Counting | Min-Heap + Arrays | `O((V + E) log V)` | `O(V + E)` | Efficient for large graphs with positive weights |

---

## **Final Tips:**
- Use **Dijkstra's algorithm with path counting** for optimal efficiency.
- Ensure to take modulo `10^9 + 7` when counting ways.
- Handle disconnected graphs by returning `0` if the destination is unreachable.

This comprehensive guide equips you with strategies to solve **Number of Ways to Arrive at Destination** efficiently and prepares you for related interview questions.
