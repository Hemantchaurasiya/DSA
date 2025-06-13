# Find the City With the Smallest Number of Neighbors at a Threshold Distance

---

## Problem Overview
Given a graph representing cities and roads between them, find the city with the **smallest number of neighboring cities** within a certain **distance threshold**. If there are multiple, return the city with the greatest index.

---

## Input
- `n`: Number of cities (0-based indices)
- `edges`: List of edges `[u, v, weight]` representing roads
- `distanceThreshold`: The maximum distance to consider a city as a neighbor

---

## Approach 1: Brute Force

### Core Idea
Calculate the shortest distance from each city to every other city using **Dijkstra's Algorithm** for each city. Count how many cities are reachable within the threshold.

### Algorithm
1. For each city `i`:
   - Run Dijkstra's algorithm to find shortest distances to all other cities.
   - Count how many distances are `<= distanceThreshold`.
2. Keep track of the city with the **minimum count** of neighbors.
3. In case of ties, choose the city with the **largest index**.

### Java Code
```java
import java.util.*;

public class FindCityWithSmallestNeighbors {
    public int findTheCity(int n, int[][] edges, int distanceThreshold) {
        int minNeighbors = Integer.MAX_VALUE;
        int resultCity = -1;

        for (int i = 0; i < n; i++) {
            int[] dist = new int[n];
            Arrays.fill(dist, Integer.MAX_VALUE);
            dist[i] = 0;

            PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[1]));
            pq.offer(new int[]{i, 0});

            while (!pq.isEmpty()) {
                int[] current = pq.poll();
                int u = current[0];
                int d = current[1];

                if (d > dist[u]) continue;

                for (int[] edge : edges) {
                    if (edge[0] == u || edge[1] == u) {
                        int v = (edge[0] == u) ? edge[1] : edge[0];
                        int weight = edge[2];
                        if (dist[u] + weight < dist[v]) {
                            dist[v] = dist[u] + weight;
                            pq.offer(new int[]{v, dist[v]});
                        }
                    }
                }
            }

            int count = 0;
            for (int d : dist) {
                if (d <= distanceThreshold) count++;
            }

            if (count <= minNeighbors) {
                minNeighbors = count;
                resultCity = i;
            }
        }
        return resultCity;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  For each city, running Dijkstra's takes `O(E log V)`. Doing this for all `n` cities results in `O(n * E log V)`.
- **Space Complexity:**  
  `O(V + E)` for the adjacency list and `O(V)` for distance array.

---

## Approach 2: Using Floyd-Warshall Algorithm (All-Pairs Shortest Path)

### Core Idea
Precompute shortest paths between every pair of cities using Floyd-Warshall, then count neighbors within the threshold for each city.

### Algorithm
1. Initialize a `dist` matrix with `dist[i][j] =` weight of edge `(i, j)` or `∞` if no direct edge.
2. Set `dist[i][i] = 0` for all `i`.
3. For each intermediate city `k`, update `dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j])`.
4. For each city, count how many are reachable within `distanceThreshold`.
5. Return the city with the smallest neighbor count, breaking ties by larger index.

### Java Code
```java
public class FindCityWithSmallestNeighborsFW {
    public int findTheCity(int n, int[][] edges, int distanceThreshold) {
        int[][] dist = new int[n][n];

        for (int i = 0; i < n; i++) {
            Arrays.fill(dist[i], Integer.MAX_VALUE / 2); // Prevent overflow
            dist[i][i] = 0;
        }

        for (int[] edge : edges) {
            dist[edge[0]][edge[1]] = edge[2];
            dist[edge[1]][edge[0]] = edge[2];
        }

        for (int k = 0; k < n; k++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (dist[i][k] + dist[k][j] < dist[i][j]) {
                        dist[i][j] = dist[i][k] + dist[k][j];
                    }
                }
            }
        }

        int minNeighbors = Integer.MAX_VALUE;
        int resultCity = -1;

        for (int i = 0; i < n; i++) {
            int count = 0;
            for (int j = 0; j < n; j++) {
                if (i != j && dist[i][j] <= distanceThreshold) {
                    count++;
                }
            }
            if (count <= minNeighbors) {
                minNeighbors = count;
                resultCity = i;
            }
        }

        return resultCity;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  Floyd-Warshall runs in `O(n^3)`.
- **Space Complexity:**  
  `O(n^2)` for the distance matrix.

---

## Approach 3: Most Optimal - Dijkstra's with Min-Heap Optimization (Single Source for Each Node)

### Core Idea
Similar to Approach 1 but optimized via adjacency list representation for faster traversals, reducing the overhead.

### Algorithm
1. Convert edges to an adjacency list.
2. For each city:
   - Run Dijkstra's algorithm using a min-heap (priority queue)
   - Count reachable cities within the threshold.
3. Track the city with minimal reachable neighbors.

### Java Code
```java
import java.util.*;

public class FindCityWithSmallestNeighborsOptimized {
    public int findTheCity(int n, int[][] edges, int distanceThreshold) {
        List<List<int[]>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }
        for (int[] edge : edges) {
            adj.get(edge[0]).add(new int[]{edge[1], edge[2]});
            adj.get(edge[1]).add(new int[]{edge[0], edge[2]});
        }

        int minNeighbors = Integer.MAX_VALUE;
        int resultCity = -1;

        for (int i = 0; i < n; i++) {
            int[] dist = new int[n];
            Arrays.fill(dist, Integer.MAX_VALUE);
            dist[i] = 0;

            PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[1]));
            pq.offer(new int[]{i, 0});

            while (!pq.isEmpty()) {
                int[] current = pq.poll();
                int u = current[0], d = current[1];

                if (d > dist[u]) continue;

                for (int[] neighbor : adj.get(u)) {
                    int v = neighbor[0];
                    int weight = neighbor[1];
                    if (dist[u] + weight < dist[v]) {
                        dist[v] = dist[u] + weight;
                        pq.offer(new int[]{v, dist[v]});
                    }
                }
            }

            int count = 0;
            for (int d : dist) {
                if (d <= distanceThreshold) count++;
            }

            if (count <= minNeighbors) {
                minNeighbors = count;
                resultCity = i;
            }
        }

        return resultCity;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  `O(n * (E log V))` due to adjacency list + Dijkstra's.
- **Space Complexity:**  
  `O(V + E)` for adjacency list, `O(V)` for distance array.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|-------------------|------------------|-------|
| Brute Force | Run Dijkstra from each city | `O(n * E log V)` | `O(V + E)` | Simple, but slow for large graphs |
| Floyd-Warshall | Precompute all shortest paths | `O(n^3)` | `O(n^2)` | Suitable for dense graphs, smaller `n` |
| Optimized Dijkstra | Use adjacency list and min-heap | `O(n * (E log V))` | `O(V + E)` | Most efficient for sparse graphs |

---

## Final Tips for Interview
- Prefer the adjacency list + Dijkstra approach for large, sparse graphs.
- Use Floyd-Warshall for dense graphs with smaller `n`.
- Always consider edge cases: disconnected graphs, no edges, or all cities within range.
- Focus on optimizing shortest path computations to improve runtime.

---

**Happy revising!**
