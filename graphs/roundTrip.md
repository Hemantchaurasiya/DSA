# Round Trip

---

## Problem Overview
Given a **directed graph** with `n` nodes labeled from `0` to `n-1`, determine **if there exists a cycle that includes all nodes** (a "round trip" or Hamiltonian cycle). Alternatively, if the problem asks to check whether a path exists that visits all nodes exactly once and returns to the start, it is a **Hamiltonian cycle detection** problem.

*(Note: Since the problem statement isn't explicitly provided, this revision assumes the common interpretation of the "Round Trip" problem as checking for a cycle that covers all nodes, i.e., a Hamiltonian cycle.)*

---

## Approach 1: Brute Force (Backtracking for Hamiltonian Cycle)
### Core Idea
Try all permutations of nodes to find a sequence that visits every node exactly once and returns to the starting node, forming a cycle.

### Algorithm
1. Choose a starting node (say node 0).
2. Use backtracking to:
   - Explore all unvisited nodes from the current node.
   - Mark the current node as visited.
   - Recurse to the next node.
   - If all nodes are visited and there's an edge back to the starting node, a cycle exists.
3. If any permutation satisfies the cycle condition, return true.

### Java Code
```java
public class HamiltonianCycle {
    private boolean[] visited;
    private int[][] graph;
    private int n;
    private int start;

    public boolean canFormCycle(int[][] graph) {
        this.graph = graph;
        this.n = graph.length;
        this.visited = new boolean[n];
        this.start = 0; // starting node
        visited[start] = true;

        return dfs(start, 1);
    }

    private boolean dfs(int current, int count) {
        if (count == n) {
            // Check if there's a path back to start
            return graph[current][start] == 1;
        }
        for (int next = 0; next < n; next++) {
            if (!visited[next] && graph[current][next] == 1) {
                visited[next] = true;
                if (dfs(next, count + 1))
                    return true;
                visited[next] = false;
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N!), because in the worst case, it explores all permutations of nodes.
- **Space Complexity:** O(N), for the recursion stack and visited array.

---

## Approach 2: Dynamic Programming with Bitmasking (Held-Karp Algorithm)
### Core Idea
Use DP with bitmasking to efficiently check all subsets and paths, reducing the exponential complexity compared to brute-force permutation.

### Algorithm
1. Represent each subset of nodes with a bitmask.
2. Use a DP array `dp[mask][i]` indicating whether there's a path covering nodes in `mask` ending at node `i`.
3. Initialize `dp[1 << start][start] = true`.
4. For each subset `mask`, for each node `i` in `mask`:
   - For each neighbor `j` not in `mask`, if there's an edge from `i` to `j`, update `dp[mask | (1 << j)][j]`.
5. After filling the DP table, check if there's a path covering all nodes and returning to the start node.

### Java Code
```java
public class HamiltonianCycleDP {
    public boolean canFormCycle(int[][] graph) {
        int n = graph.length;
        int allVisitedMask = (1 << n) - 1;
        boolean[][] dp = new boolean[1 << n][n];

        // Starting from node 0
        dp[1][0] = true;

        for (int mask = 0; mask <= allVisitedMask; mask++) {
            for (int i = 0; i < n; i++) {
                if (dp[mask][i]) {
                    for (int j = 0; j < n; j++) {
                        if ((mask & (1 << j)) == 0 && graph[i][j] == 1) {
                            dp[mask | (1 << j)][j] = true;
                        }
                    }
                }
            }
        }

        // Check if there's a path that visits all nodes and returns to start
        for (int i = 1; i < n; i++) {
            if (dp[allVisitedMask][i] && graph[i][0] == 1) {
                return true;
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N^2 * 2^N), due to the DP over all subsets.
- **Space Complexity:** O(N * 2^N), for the DP table.

---

## Approach 3: BFS/DFS to Detect Cycles Covering All Nodes
### Core Idea
Perform a BFS or DFS attempting to find a cycle that includes all nodes:
- Starting from any node, traverse and mark visited nodes.
- Use a global counter to verify if all nodes are visited in a cycle.

*(Note: This approach is less practical for large graphs but can be used for small graphs or as a heuristic.)*

### Algorithm
1. For each node, perform DFS/BFS:
   - Track visited nodes.
   - If a cycle is detected that covers all nodes, return true.
2. If no such cycle exists after trying all starting nodes, return false.

### Java Code (DFS)
```java
public class CycleCoverAllNodes {
    private boolean[] visited;
    private int[][] graph;
    private int n;
    private boolean found;

    public boolean hasRoundTrip(int[][] graph) {
        this.graph = graph;
        this.n = graph.length;
        for (int i = 0; i < n; i++) {
            visited = new boolean[n];
            dfs(i, i, 0);
            if (found) return true;
        }
        return false;
    }

    private void dfs(int start, int current, int count) {
        if (count == n && graph[current][start] == 1) {
            found = true;
            return;
        }
        if (found) return;
        visited[current] = true;

        for (int neighbor = 0; neighbor < n; neighbor++) {
            if (graph[current][neighbor] == 1 && !visited[neighbor]) {
                dfs(start, neighbor, count + 1);
            } else if (neighbor == start && count + 1 == n) {
                // Cycle found covering all nodes
                found = true;
                return;
            }
        }
        visited[current] = false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N * (N!)), since exploring all permutations starting from each node.
- **Space Complexity:** O(N), for the visited array and recursion stack.

---

## Summary
| Approach | Use Case | Pros | Cons |
|------------|--------------|-------|-------|
| Brute Force (Backtracking) | Small graphs or initial understanding | Simple, straightforward | Factorial time complexity, impractical for large N |
| DP with Bitmasking | Larger graphs, optimized exact solution | More efficient than brute-force | Exponential but less than factorial, complex implementation |
| Cycle Detection via DFS/BFS | For small graphs, heuristic | Can detect cycles covering all nodes | Not scalable, complex in large graphs |

---

## Final Recommendation
Use **DP with bitmasking** for optimal exact solutions on moderately sized graphs, or **backtracking** for small graphs or conceptual understanding.

---

**Happy Revising!**
