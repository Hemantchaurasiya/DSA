# All Ancestors of a Node in a Directed Acyclic Graph (DAG)

---

## Problem Statement
Given a **Directed Acyclic Graph (DAG)** with `n` nodes labeled from `0` to `n-1`, and a target node `k`, find all the **ancestors** of node `k`. An ancestor of `k` is any node from which there is a directed path to `k`.

---

## Approach 1: Brute Force - DFS from Each Node to Find Paths to Target

### **Core Idea:**
- For each node, perform a DFS to check if there's a path from that node to `k`.
- Collect all nodes from which such a path exists.

### **Algorithm:**
1. Represent the graph using an adjacency list.
2. Initialize an empty list `ancestors`.
3. For each node `i` in `[0, n-1]`:
   - Perform DFS starting from node `i`.
   - If during DFS, you reach node `k`, add `i` to `ancestors`.
4. Return the `ancestors` list.

### **Java Code:**
```java
import java.util.*;

public class AllAncestors {
    public List<Integer> findAllAncestors(int n, int[][] edges, int target) {
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            graph.add(new ArrayList<>());
        }
        for (int[] edge : edges) {
            graph.get(edge[0]).add(edge[1]);
        }

        List<Integer> ancestors = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if (i != target && dfs(i, target, graph, new boolean[n]))
                ancestors.add(i);
        }
        return ancestors;
    }

    private boolean dfs(int current, int target, List<List<Integer>> graph, boolean[] visited) {
        if (current == target)
            return true;
        visited[current] = true;
        for (int neighbor : graph.get(current)) {
            if (!visited[neighbor]) {
                if (dfs(neighbor, target, graph, visited))
                    return true;
            }
        }
        return false;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N * (V + E))** — For each node, DFS may traverse the entire graph.
- **Space Complexity:** **O(N + E)** for adjacency list and recursion stack.

---

## Approach 2: Reverse Graph + BFS (Optimal for Large Graphs)

### **Core Idea:**
- Reverse the direction of all edges.
- Find all nodes from which the target node `k` is reachable in the reversed graph.
- These nodes are the ancestors of `k`.

### **Algorithm:**
1. Build a reverse graph where edges point from each node's neighbors to the node itself.
2. Use a queue to perform BFS starting from node `k`.
3. Mark node `k` as visited.
4. For each node in the BFS:
   - For each neighbor in the reverse graph:
     - If not visited, mark visited and enqueue.
5. All visited nodes (except `k`) are ancestors.

### **Java Code:**
```java
import java.util.*;

public class AllAncestors {
    public List<Integer> findAllAncestors(int n, int[][] edges, int target) {
        List<List<Integer>> revGraph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            revGraph.add(new ArrayList<>());
        }

        for (int[] edge : edges) {
            revGraph.get(edge[1]).add(edge[0]);
        }

        boolean[] visited = new boolean[n];
        Queue<Integer> queue = new LinkedList<>();
        queue.offer(target);
        visited[target] = true;
        List<Integer> ancestors = new ArrayList<>();

        while (!queue.isEmpty()) {
            int current = queue.poll();
            for (int neighbor : revGraph.get(current)) {
                if (!visited[neighbor]) {
                    visited[neighbor] = true;
                    ancestors.add(neighbor);
                    queue.offer(neighbor);
                }
            }
        }
        Collections.sort(ancestors);
        return ancestors;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(V + E)** — Building reverse graph and BFS traversal.
- **Space Complexity:** **O(V + E)** for adjacency list and visited array.

---

## Approach 3: DFS with Memoization (Efficient for Repeated Queries)

### **Core Idea:**
- Use DFS with memoization to mark nodes as **reachable** to `k`.
- Once a node's reachability to `k` is determined, reuse the result.
- Avoid repeated computations for nodes.

### **Algorithm:**
1. Create an adjacency list for the graph.
2. Initialize a `memo` array:
   - **null**: not visited yet
   - **true**: reachable to `k`
   - **false**: not reachable
3. For each node:
   - Perform DFS to check if node can reach `k`.
   - Mark the result in `memo`.
4. Collect all nodes marked as reachable in `memo`.

### **Java Code:**
```java
import java.util.*;

public class AllAncestors {
    public List<Integer> findAllAncestors(int n, int[][] edges, int target) {
        List<List<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            graph.add(new ArrayList<>());
        }
        for (int[] edge : edges) {
            graph.get(edge[0]).add(edge[1]);
        }

        Boolean[] memo = new Boolean[n];
        List<Integer> ancestors = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            if (i != target && canReach(i, target, graph, memo))
                ancestors.add(i);
        }
        Collections.sort(ancestors);
        return ancestors;
    }

    private boolean canReach(int node, int target, List<List<Integer>> graph, Boolean[] memo) {
        if (node == target)
            return true;
        if (memo[node] != null)
            return memo[node];

        for (int neighbor : graph.get(node)) {
            if (canReach(neighbor, target, graph, memo)) {
                memo[node] = true;
                return true;
            }
        }
        memo[node] = false;
        return false;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N + E)** — Each node is processed once in memoized DFS.
- **Space Complexity:** **O(N + E)** for adjacency list and memoization storage.

---

## **Summary Table**

| Approach | Key Idea | Cycle Detection / Reachability | Time Complexity | Space Complexity | Notes |
|------------|--------------|------------------------------|------------------|------------------|--------|
| Brute Force DFS | Check reachability from each node | Yes | O(N * (V + E)) | O(N + E) | Simple but inefficient for large graphs |
| Reverse Graph + BFS | Find nodes that can reach `k` via reverse edges | N/A | O(V + E) | O(V + E) | Most efficient for large graphs |
| DFS with Memoization | Cache reachability results during DFS | N/A | O(N + E) | O(N + E) | Optimal for repeated queries or large graphs |

---

## **Final Tips:**
- Using **reverse graph BFS** is typically the most efficient and straightforward method.
- **Memoized DFS** is useful when multiple queries are involved.
- For small graphs, brute-force DFS works fine.
- Always consider the problem constraints to choose the most suitable approach.

---

Practice with various graphs to deepen understanding!
