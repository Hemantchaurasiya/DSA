# Cheapest Flights Within K Stops

This problem involves finding the cheapest flight price from a source city to a destination city with at most `K` stops. It can be modeled as a shortest path problem with constraints on the number of stops.

---

## Problem Definition:
Given `n` cities numbered from `0` to `n-1`, an array `flights` where each element `[u, v, w]` indicates a flight from city `u` to city `v` with cost `w`, a source city `src`, a destination city `dst`, and a maximum number of stops `K`, find the minimum cost to reach `dst` from `src` with at most `K` stops. Return `-1` if no such route exists.

---

## Approach 1: Brute Force (Recursive DFS)

### **Core Idea:**
Explore all possible routes from `src` to `dst` with at most `K` stops, tracking the minimum cost found.

### **Algorithm:**
- Use DFS starting from `src`.
- For each flight originating from current city:
  - If the next city is `dst`, update the minimum cost.
  - Else, recurse with remaining stops `K-1`.
- Return the global minimum cost after exploring all options.

### **Java Code:**
```java
public class Solution {
    int minCost = Integer.MAX_VALUE;
    Map<Integer, List<int[]>> graph;

    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {
        // Build graph
        graph = new HashMap<>();
        for (int[] flight : flights) {
            graph.computeIfAbsent(flight[0], x -> new ArrayList<>()).add(new int[]{flight[1], flight[2]});
        }
        dfs(src, dst, K, 0);
        return minCost == Integer.MAX_VALUE ? -1 : minCost;
    }

    private void dfs(int current, int dst, int stopsRemaining, int costSoFar) {
        if (stopsRemaining < 0 || costSoFar > minCost) return;
        if (current == dst) {
            minCost = Math.min(minCost, costSoFar);
            return;
        }
        if (!graph.containsKey(current)) return;

        for (int[] neighbor : graph.get(current)) {
            dfs(neighbor[0], dst, stopsRemaining - 1, costSoFar + neighbor[1]);
        }
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** Exponential, `O(N^K)` in worst case, exploring all possible routes.
- **Space Complexity:** `O(N + E)` for the adjacency list and recursion stack.

### **Dry Run:**
Suppose:
```plaintext
n=3, flights=[[0,1,100],[1,2,100],[0,2,500]], src=0, dst=2, K=1
```
- From `0`, options:
  - To `1` with cost 100, remaining stops = 0:
    - From `1`, to `2` with cost 100, total cost = 200 -> update minCost.
  - Direct from `0` to `2` with cost 500:
    - Cost = 500 -> compare with current minCost (200), no update.
- Final minCost = 200.

---

## Approach 2: Breadth-First Search (BFS) with Queue (K-Stop Layered BFS)

### **Core Idea:**
Perform BFS level-by-level, where each level corresponds to the number of stops taken, to ensure at most `K` stops.

### **Algorithm:**
1. Build an adjacency list.
2. Use a queue to store `(currentCity, totalCost, stops)` starting from `(src, 0, 0)`.
3. Maintain an array `dist[]` to record the minimum cost to reach each city.
4. While queue is not empty:
   - Dequeue `(city, cost, stops)`.
   - If `city == dst`, update the answer.
   - If `stops <= K`, explore neighbors:
     - For each neighbor `(v, w)`, if `cost + w < dist[v]`, update `dist[v]` and enqueue `(v, cost + w, stops + 1)`.

### **Java Code:**
```java
public int findCheapestPriceBFS(int n, int[][] flights, int src, int dst, int K) {
    Map<Integer, List<int[]>> graph = new HashMap<>();
    for (int[] flight : flights) {
        graph.computeIfAbsent(flight[0], x -> new ArrayList<>()).add(new int[]{flight[1], flight[2]});
    }

    int[] dist = new int[n];
    Arrays.fill(dist, Integer.MAX_VALUE);
    dist[src] = 0;

    Queue<int[]> queue = new LinkedList<>();
    queue.offer(new int[]{src, 0, 0}); // {currentCity, cost, stops}

    int result = Integer.MAX_VALUE;

    while (!queue.isEmpty()) {
        int[] curr = queue.poll();
        int city = curr[0], cost = curr[1], stops = curr[2];

        if (city == dst) {
            result = Math.min(result, cost);
        }
        if (stops > K) continue;

        if (graph.containsKey(city)) {
            for (int[] neighbor : graph.get(city)) {
                int v = neighbor[0], w = neighbor[1];
                if (cost + w < dist[v]) {
                    dist[v] = cost + w;
                    queue.offer(new int[]{v, cost + w, stops + 1});
                }
            }
        }
    }
    return result == Integer.MAX_VALUE ? -1 : result;
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(E + N * log N)` in worst case, due to BFS and updates.
- **Space Complexity:** `O(N + E)` for adjacency list and queue.

### **Dry Run:**
- Using the same example:
```plaintext
n=3, flights=[[0,1,100],[1,2,100],[0,2,500]], src=0, dst=2, K=1
```
- Start `(0, 0, 0)`.
- From `0`, enqueue:
  - `(1, 100, 1)`
  - `(2, 500, 1)`
- Dequeue `(1, 100, 1)`:
  - From `1`, to `2` with cost 200, enqueue `(2, 200, 2)` (but stops=2 > K? no, we only enqueue if `stops <= K`).
- Dequeue `(2, 200, 2)`:
  - `city == dst`, update answer to 200.
- Dequeue `(2, 500, 1)`:
  - Already have a cheaper route, ignore.

Final answer: 200.

---

## Approach 3: Dijkstra's Algorithm with Priority Queue (Most Optimal)

### **Core Idea:**
Use a modified Dijkstra's algorithm that accounts for stops, ensuring the shortest path within the stop constraint.

### **Algorithm:**
1. Build adjacency list.
2. Use a priority queue with elements `(cost, city, stops)`.
3. Maintain a `dist[][]` array where `dist[city][stops]` stores the minimum cost to reach `city` with `stops` stops.
4. Initialize `dist[src][0] = 0`.
5. While queue is not empty:
   - Extract `(cost, city, stops)`.
   - If `city == dst`, return `cost`.
   - For each neighbor `(v, w)`:
     - If `stops + 1 <= K + 1` and `cost + w < dist[v][stops + 1]`, update and enqueue.

### **Java Code:**
```java
import java.util.*;

public int findCheapestPriceDijkstra(int n, int[][] flights, int src, int dst, int K) {
    // Build graph
    Map<Integer, List<int[]>> graph = new HashMap<>();
    for (int[] flight : flights) {
        graph.computeIfAbsent(flight[0], x -> new ArrayList<>()).add(new int[]{flight[1], flight[2]});
    }

    int[][] dist = new int[n][K + 2];
    for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);
    dist[src][0] = 0;

    PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
    pq.offer(new int[]{0, src, 0}); // {cost, city, stops}

    while (!pq.isEmpty()) {
        int[] current = pq.poll();
        int cost = current[0], city = current[1], stops = current[2];

        if (city == dst) return cost;

        if (graph.containsKey(city)) {
            for (int[] neighbor : graph.get(city)) {
                int v = neighbor[0], w = neighbor[1];
                if (stops + 1 <= K + 1 && cost + w < dist[v][stops + 1]) {
                    dist[v][stops + 1] = cost + w;
                    pq.offer(new int[]{dist[v][stops + 1], v, stops + 1});
                }
            }
        }
    }

    int minCost = Integer.MAX_VALUE;
    for (int i = 0; i <= K + 1; i++) {
        minCost = Math.min(minCost, dist[dst][i]);
    }
    return minCost == Integer.MAX_VALUE ? -1 : minCost;
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(E * log N)` with additional stops dimension; practically similar to Dijkstra.
- **Space Complexity:** `O(N * K)` for the `dist` array and adjacency list.

### **Dry Run:**
- Same example:
```plaintext
n=3, flights=[[0,1,100],[1,2,100],[0,2,500]], src=0, dst=2, K=1
```
- Initialize `dist[0][0]=0`.
- Enqueue `(0,0,0)`.
- Extract `(0,0,0)`:
  - Explore neighbors:
    - `(1,100,1)` -> `dist[1][1]=100`.
    - `(2,500,1)` -> `dist[2][1]=500`.
- Extract `(1,100,1)`:
  - Explore neighbors:
    - `(2,100,2)` -> `dist[2][2]=200` (update since `200 < 500`).
- Extract `(2,200,2)`:
  - `city==dst`, return 200 as answer.

---

## **Summary Table**

| Approach | Best For | Time Complexity | Space Complexity | Notes |
|-------------|------------|------------------|------------------|--------|
| Recursive DFS | Small graphs, understanding | Exponential `O(N^K)` | `O(N + E)` | Not practical for large inputs |
| BFS (Layered) | Moderate constraints | `O(E + N * K)` | `O(N + E)` | Good for small `K` |
| Dijkstra with Stops Array | Larger constraints, efficient | `O(E * log N)` or similar | `O(N * K)` | Most optimal for weighted graphs with stop constraints |

---

## Final Tips:
- For large graphs with positive weights, prefer **Dijkstra's algorithm**.
- For small constraints, simple DFS or BFS may suffice.
- Always consider the maximum allowed stops `K` as a limiting factor.
- Use adjacency lists for efficient graph traversal.
- Dry run with sample inputs to understand how the `dist` array and priority queue evolve.

---

**Happy coding and best of luck in your interviews!**
