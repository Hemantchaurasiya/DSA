# Hamiltonian Cycle (or Path)

---

## Problem Overview
- **Goal:** Given a graph, determine whether there exists a Hamiltonian Cycle (a cycle visiting each vertex exactly once and returning to the starting vertex) or a Hamiltonian Path (visiting each vertex exactly once, not necessarily returning).
- **Input:** Graph represented as an adjacency list or matrix.
- **Output:** Boolean indicating whether such a cycle/path exists.

---

## Approach 1: Brute Force

### Core Idea
- Generate all permutations of vertices.
- Check if any permutation forms a Hamiltonian Path or Cycle.

### Algorithm
1. Generate all permutations of vertices.
2. For each permutation:
    - Check if every consecutive pair of vertices is connected.
    - For cycle: also check if last vertex connects back to first.
3. If any permutation satisfies the condition, return `true`; else `false`.

### Recursion Tree Diagram
```
Start
 ├─ Pick vertex 0 as start
 ├─ Permute remaining vertices
      ├─ For each permutation, check adjacency
      └─ Return true if valid
```

### Java Code
```java
import java.util.*;

public class HamiltonianCycle {
    public boolean isHamiltonianCycle(int[][] graph) {
        int V = graph.length;
        List<Integer> path = new ArrayList<>();
        boolean[] visited = new boolean[V];

        // Start from vertex 0 (arbitrary choice)
        path.add(0);
        visited[0] = true;

        return dfs(graph, path, visited, V);
    }

    private boolean dfs(int[][] graph, List<Integer> path, boolean[] visited, int V) {
        if (path.size() == V) {
            // Check if last vertex connects to the first (cycle)
            int last = path.get(path.size() - 1);
            int first = path.get(0);
            return graph[last][first] == 1;
        }

        int current = path.get(path.size() - 1);
        for (int v = 0; v < V; v++) {
            if (!visited[v] && graph[current][v] == 1) {
                visited[v] = true;
                path.add(v);
                if (dfs(graph, path, visited, V))
                    return true;
                // Backtrack
                visited[v] = false;
                path.remove(path.size() - 1);
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(V!)**  
  Because it explores all permutations of vertices.
- **Space Complexity:** **O(V)**  
  For recursion stack and auxiliary data structures.

### Dry Run (Sample Input)
```
Graph:
0 -- 1
|    |
2 -- 3

Vertices: 0, 1, 2, 3

Start at 0:
 Path: [0]
 Explore neighbors:
  - 1: Path: [0,1]
    - Explore neighbors of 1:
      - 2: Path: [0,1,2]
        - Explore neighbors of 2:
          - 3: Path: [0,1,2,3]
            - Check if 3 connects back to 0 for cycle → No (edge 3-0 missing)
            → Backtrack
      - 3: Path: [0,1,3]
        - Explore neighbors of 3:
          - 2: Path: [0,1,3,2]
            - Check if 2 connects back to 0? No.
  - 2: Path: [0,2]
    - Similar exploration...
```

---

## Approach 2: Backtracking with Pruning (Optimized)

### Core Idea
- Use backtracking but prune paths early if they cannot lead to a solution, e.g., if the next vertex is not connected or already visited.
- Implement a recursive function that tries to build the Hamiltonian Path/Cycle from a starting vertex.

### Algorithm
1. Choose a starting vertex (say 0).
2. Recursively explore neighbors not yet visited.
3. If all vertices are visited:
    - For cycle: check if last vertex connects to start.
    - For path: just return true.
4. Backtrack if dead-end.

### Java Code
```java
public class HamiltonianCycle {
    public boolean isHamiltonianCycle(int[][] graph) {
        int V = graph.length;
        boolean[] visited = new boolean[V];
        int start = 0; // arbitrary start
        return backtrack(graph, start, visited, 1, V);
    }

    private boolean backtrack(int[][] graph, int current, boolean[] visited, int count, int V) {
        if (count == V) {
            // For cycle, check if last connects to start
            for (int v = 0; v < V; v++) {
                if (graph[current][v] == 1 && v == 0)
                    return true;
            }
            return false;
        }

        for (int v = 0; v < V; v++) {
            if (!visited[v] && graph[current][v] == 1) {
                visited[v] = true;
                if (backtrack(graph, v, visited, count + 1, V))
                    return true;
                visited[v] = false; // backtrack
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(V!)** in the worst case, but pruning reduces the search space.
- **Space Complexity:** **O(V)** for recursion stack and visited array.

---

## Approach 3: Dynamic Programming (Held-Karp Algorithm)

### Core Idea
- Use DP with bitmasking to track visited vertices.
- For each vertex, store whether there's a path visiting a subset of vertices ending at that vertex.

### Algorithm
1. Initialize a DP table `dp[mask][v]`, where:
    - `mask` represents visited vertices (bitmask).
    - `v` is the current vertex.
2. Set `dp[1 << start][start] = true`.
3. For each subset of vertices:
    - For each vertex `v` in subset:
        - For each neighbor `u` not in subset:
            - If `dp[mask][v]` is true and `graph[v][u]` is 1:
                - Set `dp[mask | (1 << u)][u] = true`.
4. After filling, check if any `dp[(1 << V) - 1][v]` is true and if `graph[v][start]` is 1 (for cycle).

### Java Code
```java
public class HamiltonianCycle {
    public boolean isHamiltonianCycle(int[][] graph) {
        int V = graph.length;
        int ALL_VISITED = (1 << V) - 1;
        boolean[][] dp = new boolean[1 << V][V];

        // Start from vertex 0
        dp[1][0] = true;

        for (int mask = 1; mask <= ALL_VISITED; mask++) {
            for (int v = 0; v < V; v++) {
                if (dp[mask][v]) {
                    for (int u = 0; u < V; u++) {
                        if ((mask & (1 << u)) == 0 && graph[v][u] == 1) {
                            dp[mask | (1 << u)][u] = true;
                        }
                    }
                }
            }
        }

        // Check for cycle
        for (int v = 0; v < V; v++) {
            if (dp[ALL_VISITED][v] && graph[v][0] == 1) {
                return true;
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(V^2 * 2^V)** — filling DP table for all subsets.
- **Space Complexity:** **O(V * 2^V)** — for DP table.

---

## Summary of Approaches

| Approach | Idea | Time Complexity | Space Complexity | Notes |
|------------|-------|-------------------|------------------|--------|
| Brute Force | Generate all permutations | O(V!) | O(V) | Not feasible for large V, exponential time. |
| Backtracking | Prune invalid paths early | O(V!) in worst case | O(V) | Better than brute force with pruning. |
| DP (Held-Karp) | Use bitmask DP for efficient traversal | O(V^2 * 2^V) | O(V * 2^V) | Most optimal for exact solution, suitable for small V (~20). |

---

## Final Tips for Interview
- Understand the problem constraints: For small graphs, brute-force or backtracking suffices.
- For larger graphs, DP (Held-Karp) is optimal.
- Always consider pruning in backtracking to improve practical performance.
- Use bitmasking techniques to optimize state representation in DP.
- Remember to check for cycle existence explicitly in cycle problems.

---

**Happy Revising!**
