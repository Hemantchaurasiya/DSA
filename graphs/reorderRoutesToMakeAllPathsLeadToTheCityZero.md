# Reorder Routes to Make All Paths Lead to the City Zero

---

## Problem Overview
Given `n` cities and a list of directed roads (connections), determine the minimum number of roads that need to be **reordered** so that **all roads lead to city 0**.

- **Input:** List of connections, where each connection `[a, b]` indicates a road from city `a` to city `b`.
- **Output:** Minimum number of roads to reorient so that every city can reach city 0.

---

## Approach 1: Brute Force (Naive Traversal)
### Core Idea
Check all roads and attempt to reorient roads directly connected to cities not reachable from city 0. This naive approach isn't efficient because it involves multiple redundant traversals and doesn't account for the overall connectivity efficiently.

*(Note: Typically, brute-force is not feasible for large inputs, but it helps understand the problem.)*

### Algorithm
- For each city, determine if it can reach city 0.
- If not, reorient roads leading into that city.
- Repeat until all cities can reach city 0.

**Note:** This approach is not practical due to high time complexity; more optimized solutions are preferred.

---

## Approach 2: Graph Traversal with Reorientation Count (Most Effective)
### Core Idea
Model the network as a graph, then perform a **graph traversal** (DFS or BFS) starting from city 0.
- Count roads that need reorientation.
- During traversal, if you encounter an outgoing road (from current city to a city not yet visited), you need to reorient it so that the path leads towards city 0.
- Keep track of roads needing reorientation.

### Algorithm
1. Build an adjacency list for the graph:
   - For each connection `[a, b]`, add `b` to `a`'s adjacency list (outgoing).
   - Also, create a reverse adjacency list: for each `[a, b]`, add `a` to `b`'s reverse adjacency list.
2. Use a queue or stack to perform BFS/DFS starting from city 0.
3. Maintain a `visited` set to avoid revisiting.
4. For each current city:
   - Explore all neighbors in the **original adjacency list**:
     - If the neighbor is not visited:
       - Increment the reorientation count (since the road is outgoing from current, but we need it to lead to city 0).
       - Continue traversal.
   - Explore neighbors in the **reverse adjacency list**:
     - If not visited, traverse without incrementing reorientation count (since they are correctly oriented towards city 0).
5. Continue until all reachable cities are visited.

### Java Code
```java
import java.util.*;

public class ReorderRoutes {
    public int minReorder(int n, int[][] connections) {
        List<List<Integer>> adj = new ArrayList<>();
        List<List<Integer>> revAdj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
            revAdj.add(new ArrayList<>());
        }
        
        for (int[] conn : connections) {
            int a = conn[0], b = conn[1];
            adj.get(a).add(b); // outgoing edge
            revAdj.get(b).add(a); // incoming edge (reverse)
        }
        
        int reorders = 0;
        boolean[] visited = new boolean[n];
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(0);
        visited[0] = true;

        while (!queue.isEmpty()) {
            int city = queue.poll();

            // Explore neighbors in original adjacency (roads from city)
            for (int neighbor : adj.get(city)) {
                if (!visited[neighbor]) {
                    reorders++; // Need to reorient this road
                    visited[neighbor] = true;
                    queue.offer(neighbor);
                }
            }

            // Explore neighbors in reverse adjacency (roads leading to city)
            for (int neighbor : revAdj.get(city)) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    queue.offer(neighbor);
                }
            }
        }

        return reorders;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N + E), where N is the number of cities and E is the number of roads, because each edge is processed at most twice.
- **Space Complexity:** O(N + E), for adjacency lists and the visited array.

---

## Dry Run: Example

### Input:
```plaintext
n = 6
connections = [[0,1], [1,3], [2,3], [4,0], [4,5]]
```

### Step-by-step Walkthrough:

| Step | Current City | Roads in `adj` | Roads in `revAdj` | Action | Reorders | Visited Set | Queue | Notes |
|-------|----------------|------------------|-------------------|---------|----------|--------------|--------|--------|
| 1     | 0              | 1 (to city 1)    | 4 (from city 4)   | Start from city 0 | 0 | {0} | [0] | |
| 2     | 0              | Explore 1: unvisited | 4 (already visited) | Reorient road 0→1 | 1 | {0,1} | [1] | Reorient needed for 0→1 |
| 3     | 1              | 3 (to city 3)    | 1 (from city 1)   | Reorient 1→3 | 2 | {0,1,3} | [3] | Reorient for 1→3 |
| 4     | 3              | no new outgoing | 1 (from city 1)   | No reorient | 2 | {0,1,3} | [] | No new roads from 3 |
| 5     | 4              | 0 (to city 0)    | 5 (from city 4)   | Already visited | - | {0,1,3,4} | [4] | |
| 6     | 4              | 5 (to city 5)    | 0 (from city 4)   | Reorient 4→5 | 3 | {0,1,3,4,5} | [5] | Reorient for 4→5 |
| 7     | 5              | no outgoing | 4 (already visited) | No reorient | 3 | {0,1,3,4,5} | [] | Done |

**Final answer:** 3 roads need reorientation.

---

## Summary
| Approach | Best Use Case | Pros | Cons |
|------------|--------------|-------|-------|
| Graph Traversal with Reorientation Counting | Efficient, suitable for large graphs | Linear time complexity, clear logic | Requires building adjacency lists |
| Brute Force | Conceptual understanding | Simple for small inputs | Inefficient, high time complexity |

---

## Final Recommendation
Use **graph traversal with adjacency and reverse adjacency lists** to efficiently compute the minimum reorientations needed.

---

**Happy Revising!**
