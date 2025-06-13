# Detect Cycle in an Undirected Graph

This guide covers various approaches to detect a cycle in an undirected graph, from naive to optimal solutions. It is structured to aid quick revision and interview preparation.

---

## 1. Brute Force Approach

### Core Idea:
- Check for cycles by exploring all possible paths in the graph.
- Since the graph is undirected, a cycle exists if during DFS/BFS, we encounter a previously visited node that is not the parent.

### Algorithm:
1. For each unvisited node, perform DFS/BFS.
2. During traversal, mark nodes as visited.
3. If you encounter a visited node which is **not** the parent of the current node, a cycle exists.
4. If no such condition is found after traversal, no cycle exists.

### Java Code:
```java
import java.util.*;

public class DetectCycleInUndirectedGraph {

    public boolean isCyclic(List<List<Integer>> adj, int V) {
        boolean[] visited = new boolean[V];

        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                if (dfs(i, -1, visited, adj))
                    return true;
            }
        }
        return false;
    }

    private boolean dfs(int node, int parent, boolean[] visited, List<List<Integer>> adj) {
        visited[node] = true;

        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                if (dfs(neighbor, node, visited, adj))
                    return true;
            } else if (neighbor != parent) {
                // Visited neighbor which is not the parent indicates a cycle
                return true;
            }
        }
        return false;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(V + E)  
  Each node and edge is visited once during DFS.
- **Space Complexity:** O(V)  
  For the visited array and recursion stack.

### Dry Run:
**Input Graph:**
```
0 -- 1 -- 2
|         
3
```
- Start DFS from node 0:
  - Visit 0 → neighbors: 1, 3
  - Visit 1 (from 0), neighbor: 2, parent: 1
  - Visit 2 (from 1), neighbors: 1 (visited, parent: 1)
  - No cycle found in this path.
- Visit 3 (from 0), neighbor: 0 (visited, parent: -1), but since 0 is parent, no cycle.
- Result: **No cycle detected**.

---

## 2. Better Solution (Using BFS)

### Core Idea:
- Similar to DFS but uses BFS traversal.
- Detects back edges indicating cycles when encountering a visited node that is **not** the parent.

### Algorithm:
1. For each unvisited node, run BFS.
2. Keep track of parent nodes.
3. If a visited node is encountered again and it's not the parent, a cycle exists.

### Java Code:
```java
public boolean isCyclicBFS(List<List<Integer>> adj, int V) {
    boolean[] visited = new boolean[V];

    for (int i = 0; i < V; i++) {
        if (!visited[i]) {
            if (bfsCheck(i, visited, adj))
                return true;
        }
    }
    return false;
}

private boolean bfsCheck(int start, boolean[] visited, List<List<Integer>> adj) {
    Queue<int[]> queue = new LinkedList<>();
    visited[start] = true;
    queue.offer(new int[]{start, -1}); // {node, parent}

    while (!queue.isEmpty()) {
        int[] current = queue.poll();
        int node = current[0];
        int parent = current[1];

        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(new int[]{neighbor, node});
            } else if (neighbor != parent) {
                // Visited neighbor that's not the parent → cycle
                return true;
            }
        }
    }
    return false;
}
```

### Complexity Analysis:
- **Time Complexity:** O(V + E)
- **Space Complexity:** O(V) for visited array and queue.

### Dry Run:
Same input as above:
- BFS starting from node 0:
  - Visit 0, enqueue neighbors 1, 3.
  - Visit 1, enqueue neighbor 2.
  - Visit 2, neighbor: 1 (already visited, parent: 1) → no cycle.
  - Visit 3, neighbor: 0 (visited, parent: 0) → no cycle.
- No cycle detected.

---

## 3. Most Optimal Solution (Union-Find / Disjoint Set Union - DSU)

### Core Idea:
- Use Union-Find data structure to detect cycles efficiently.
- For each edge, if the two vertices belong to the same set, a cycle exists.
- Otherwise, union their sets.

### Algorithm:
1. Initialize DSU for all nodes.
2. For each edge (u, v):
   - Find the leaders/parents of u and v.
   - If they are same, cycle exists.
   - Else, union their sets.
3. If no union finds a cycle, the graph is acyclic.

### Java Code:
```java
public class DetectCycleUnionFind {

    int[] parent, rank;

    public DetectCycleUnionFind(int V) {
        parent = new int[V];
        rank = new int[V];
        for (int i = 0; i < V; i++) {
            parent[i] = i;
            rank[i] = 0;
        }
    }

    private int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);
        return parent[x];
    }

    private boolean union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);

        if (rootX == rootY)
            return false; // Cycle detected

        if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else if (rank[rootX] > rank[rootY]) {
            parent[rootY] = rootX;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }
        return true;
    }

    public boolean isCyclic(List<List<Integer>> edges, int V) {
        for (List<Integer> edge : edges) {
            int u = edge.get(0);
            int v = edge.get(1);
            if (!union(u, v))
                return true; // Cycle detected
        }
        return false;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(V + E * α(V)), where α(V) is the inverse Ackermann function (almost constant).
- **Space Complexity:** O(V) for parent and rank arrays.

### Dry Run:
**Input Edges:**
```
Edges: (0,1), (1,2), (2,0)
```
- Initialize parents: [0,1,2]
- Process edge (0,1):
  - find(0) = 0, find(1) = 1 → union successful.
- Process edge (1,2):
  - find(1)=1, find(2)=2 → union successful.
- Process edge (2,0):
  - find(2)=2, find(0)=0
  - Different parents? No → cycle detected.

---

## Summary Table

| Approach | Core Idea | Data Structures | Key Operations | Time Complexity | Space Complexity |
|------------|--------------|------------------|----------------|-----------------|------------------|
| Brute Force (DFS) | Explore all paths, check for back edges | Adjacency list, recursion | DFS, visited, parent check | O(V + E) | O(V) |
| BFS | Level-wise traversal, check for cross edges | Queue, visited, parent tracking | BFS, visited, parent check | O(V + E) | O(V) |
| Union-Find | Use DSU to detect cycles during edge addition | DSU data structure | find, union | O(V + E * α(V)) | O(V) |

---

## Final Tips for Interviews:
- Always clarify whether the graph is directed or undirected.
- Use DFS or Union-Find for optimal solutions.
- Remember to handle disconnected graphs by checking all components.
- Union-Find is very efficient for cycle detection in sparse graphs.

---

**Happy Revising!**
