# Second Minimum Time to Reach Destination

---

## Problem Overview
Given an `n x n` grid representing a city with traffic signals, each cell contains a signal with a cycle (alternating between green and red). You start from `(0,0)` and want to reach `(n-1, n-1)` in the **minimum time**. Moving from one cell to a neighboring cell takes 1 unit of time, but you can only move when the signal is green (or when the signal turns green). The traffic signals change states in cycles, and your movement may be delayed if you arrive when the signal is red.

The goal is to determine the **second minimum time** (i.e., the second shortest possible time) to reach the destination.

---

## Approach 1: Brute Force (Path Enumeration with Delay Calculation)

### **Core Idea:**
Explore all possible paths with their respective waiting times due to traffic signals, then select the second shortest time. This is impractical because the number of paths can be exponential, especially with delays.

### **Algorithm:**
- Generate all paths from `(0,0)` to `(n-1, n-1)` using DFS.
- For each path, calculate the total time, considering waiting times at signals:
  - When arriving at a cell, check the cycle timing.
  - If signal is red, wait until it turns green.
- Collect all such times, sort them, and pick the second smallest.

**Note:** Due to combinatorial explosion, this approach is not feasible for large inputs.

---

### **Complexity Analysis:**
- **Time Complexity:** Exponential, due to exploring all paths.
- **Space Complexity:** `O(n^2)` for recursion stack and storage.

---

## Approach 2: A* Search or Modified BFS with Delay Handling

### **Core Idea:**
Use a BFS or A* search that accounts for waiting times at signals, effectively exploring the shortest and second shortest times by tracking multiple arrival times at each cell.

### **Algorithm:**

1. **Initialize Data Structures:**
   - A priority queue (min-heap) or queue for BFS.
   - A `dist` array or map to keep track of the shortest and second shortest arrival times at each cell.

2. **Starting Point:**
   - Enqueue `(0, 0)` with time `0`.

3. **Process Nodes:**
   - While the queue is not empty:
     - Dequeue the current cell `(x, y)` and current time `t`.
     - For each neighbor `(nx, ny)`:
       - Calculate the earliest time you can move there considering the traffic signal cycle:
         - If arriving during red, wait until the next green cycle.
       - Let the arrival time be `new_time`.
       - Update the `dist` array for `(nx, ny)`:
         - If `new_time` is less than the shortest recorded time, update and enqueue.
         - If `new_time` is between the shortest and second shortest, update and enqueue.
     - Stop when the second shortest time to `(n-1, n-1)` is found.

4. **Result:**
   - Return the second shortest arrival time at `(n-1, n-1)`.

---

### **Java Code (Simplified Version):**
```java
import java.util.*;

public class SecondMinimumTime {
    public int secondMinimum(int n, int[][] edges, int time, int change) {
        // Build adjacency list
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());
        for (int[] edge : edges) {
            graph.get(edge[0]).add(edge[1]);
            graph.get(edge[1]).add(edge[0]);
        }

        // dist[node][0] = shortest, dist[node][1] = second shortest
        int[][] dist = new int[n][2];
        for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);
        dist[0][0] = 0;

        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[1]));
        pq.offer(new int[]{0, 0}); // {node, time}

        while (!pq.isEmpty()) {
            int[] curr = pq.poll();
            int node = curr[0], currTime = curr[1];

            if (node == n - 1 && dist[node][1] != Integer.MAX_VALUE) {
                return dist[node][1]; // Second minimum time
            }

            for (int neighbor : graph.get(node)) {
                int waitTime = 0;
                int arrivalTime = currTime;

                // Wait if signal is red
                while ((arrivalTime / change) % 2 == 1) {
                    arrivalTime++;
                }

                int neighborTime = arrivalTime + time;
                if (neighborTime < dist[neighbor][0]) {
                    dist[neighbor][0] = neighborTime;
                    pq.offer(new int[]{neighbor, neighborTime});
                } else if (neighborTime > dist[neighbor][0] && neighborTime < dist[neighbor][1]) {
                    dist[neighbor][1] = neighborTime;
                    pq.offer(new int[]{neighbor, neighborTime});
                }
            }
        }
        return -1; // If second shortest doesn't exist
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** `O((n + E) log n)` where `E` is number of edges, due to priority queue operations. Typically, for dense graphs, this can be approximated as `O(n^2 log n)`.
- **Space Complexity:** `O(n + E)` for adjacency list and `O(n)` for distance arrays.

---

## **Summary Table**

| Approach | Data Structure | Time Complexity | Space Complexity | Notes |
|------------|-----------------|---------------------|-----------------------|--------|
| Brute Force | DFS Path Enumeration | Exponential | `O(n^2)` | Not feasible for large inputs |
| Modified BFS / Dijkstra | Priority Queue + Distance Arrays | `O((n + E) log n)` | `O(n + E)` | Efficient for larger graphs with signal delays |

---

## **Final Tips:**
- Use **priority queues** or **Dijkstra-like algorithms** to handle delays and find second shortest times.
- Carefully model the waiting time due to traffic signals:
  - Signals change every `change` units.
  - If arriving during red cycle, wait until the signal turns green.
- Track **both** shortest and second shortest times for each node to correctly identify the second minimum time.

This guide provides a clear overview of solving **Second Minimum Time to Reach Destination**, optimized for interview prep and quick revision.
