# Strongly Connected Components (SCC)

---

## Introduction
**Strongly Connected Components (SCCs)** in a directed graph are maximal subgraphs where **every vertex is reachable from every other vertex** within the same subgraph. Finding SCCs helps in understanding the structure of directed graphs, detecting cycles, and simplifying graph problems.

---

## 1. Brute Force Approach

### **Core Idea**
- For each vertex, check if it belongs to a strongly connected component by testing reachability to and from every other vertex.
- This involves repeatedly checking connectivity between pairs of vertices.

### **Algorithm**
1. For each vertex `v`:
   - Perform DFS or BFS from `v` to see which vertices are reachable.
   - Perform DFS or BFS in the reverse graph from `v` to see which vertices can reach `v`.
2. Collect vertices that are mutually reachable (i.e., `v` can reach `u` and `u` can reach `v`) into an SCC.
3. Repeat for all vertices, marking visited vertices to avoid duplication.

### **Java Code**
```java
import java.util.*;

public class SCCBruteForce {
    static int V;
    static List<List<Integer>> graph, revGraph;
    static boolean[] visited;

    public static void main(String[] args) {
        V = 5;
        graph = new ArrayList<>();
        revGraph = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            graph.add(new ArrayList<>());
            revGraph.add(new ArrayList<>());
        }
        addEdge(0, 1);
        addEdge(1, 2);
        addEdge(2, 0);
        addEdge(1, 3);
        addEdge(3, 4);

        findSCCs();
    }

    static void addEdge(int u, int v) {
        graph.get(u).add(v);
        revGraph.get(v).add(u);
    }

    static void findSCCs() {
        boolean[] visitedGlobal = new boolean[V];

        for (int v = 0; v < V; v++) {
            if (!visitedGlobal[v]) {
                List<Integer> scc = new ArrayList<>();
                for (int u = 0; u < V; u++) {
                    if (!visitedGlobal[u]) {
                        if (isMutuallyReachable(v, u)) {
                            scc.add(u);
                            visitedGlobal[u] = true;
                        }
                    }
                }
                System.out.println("SCC: " + scc);
            }
        }
    }

    static boolean isMutuallyReachable(int start, int u) {
        return isReachable(graph, start, u) && isReachable(revGraph, start, u);
    }

    static boolean isReachable(List<List<Integer>> g, int start, int target) {
        visited = new boolean[V];
        dfs(g, start);
        return visited[target];
    }

    static void dfs(List<List<Integer>> g, int u) {
        visited[u] = true;
        for (int v : g.get(u)) {
            if (!visited[v]) {
                dfs(g, v);
            }
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(V * (V + E)^2)  
  For each vertex, we perform reachability checks in both the original and reverse graphs, each costing O(V + E). Repeated for all vertices.

- **Space Complexity:** O(V + E)  
  For storing the graph and auxiliary data structures.

---

## 2. Better Solution: Kosaraju's Algorithm

### **Core Idea**
- Use **two DFS passes**:
  - First DFS to compute **finishing times**.
  - Second DFS on the **reversed graph** in order of decreasing finishing times.
- Each DFS in the second pass identifies one SCC.

### **Algorithm**
1. Perform DFS on the original graph to compute **finishing times** for each vertex.
2. Reverse the graph's edges.
3. Process vertices in decreasing order of finishing times:
   - For each unvisited vertex, perform DFS on the reversed graph.
   - All vertices reached in this DFS form an SCC.
4. Repeat until all vertices are visited.

### **Java Code**
```java
import java.util.*;

public class KosarajuSCC {
    static int V;
    static List<List<Integer>> graph, revGraph;
    static boolean[] visited;
    static Stack<Integer> finishStack;

    public static void main(String[] args) {
        V = 5;
        graph = new ArrayList<>();
        revGraph = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            graph.add(new ArrayList<>());
            revGraph.add(new ArrayList<>());
        }
        addEdge(0, 1);
        addEdge(1, 2);
        addEdge(2, 0);
        addEdge(1, 3);
        addEdge(3, 4);

        findSCCs();
    }

    static void addEdge(int u, int v) {
        graph.get(u).add(v);
        revGraph.get(v).add(u);
    }

    static void findSCCs() {
        visited = new boolean[V];
        finishStack = new Stack<>();
        // First pass: fill finish times
        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                dfsFillOrder(i);
            }
        }

        // Second pass: process in reverse order
        Arrays.fill(visited, false);
        while (!finishStack.isEmpty()) {
            int v = finishStack.pop();
            if (!visited[v]) {
                List<Integer> scc = new ArrayList<>();
                dfsOnRev(v, scc);
                System.out.println("SCC: " + scc);
            }
        }
    }

    static void dfsFillOrder(int u) {
        visited[u] = true;
        for (int v : graph.get(u)) {
            if (!visited[v]) {
                dfsFillOrder(v);
            }
        }
        finishStack.push(u);
    }

    static void dfsOnRev(int u, List<Integer> scc) {
        visited[u] = true;
        scc.add(u);
        for (int v : revGraph.get(u)) {
            if (!visited[v]) {
                dfsOnRev(v, scc);
            }
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(V + E)  
  Each DFS runs once or twice, traversing all vertices and edges.

- **Space Complexity:** O(V + E)  
  For storing graphs, recursion stack, and auxiliary data structures.

---

## 3. Most Optimal Solution: Kosaraju's or Tarjan's Algorithm

### **Core Idea**
- Both approaches run in **linear time** and are optimal for finding SCCs in directed graphs.
- Tarjan's algorithm uses a single DFS pass with low-link values, while Kosaraju's uses two DFS passes.

---

## **Dry Run Example**

### Input Graph:
```
Vertices: 5
Edges:
0 -> 1
1 -> 2
2 -> 0
1 -> 3
3 -> 4
```

### Expected SCCs:
- {0, 1, 2}
- {3}
- {4}

### Step-by-step (using Kosaraju):

| Step | Action | Details |
|--------|---------|---------|
| 1 | DFS to fill finish times | Order: 4, 3, 2, 1, 0 |
| 2 | Reverse edges | Create reversed graph |
| 3 | Process in decreasing order | Start with 0, 1, 2 | Form SCC {0, 1, 2} |
| 4 | Next unvisited: 3 | Form SCC {3} |
| 5 | Next unvisited: 4 | Form SCC {4} |

---

## **Summary**

| Approach | Core Idea | Algorithm | Time Complexity | Space Complexity | Remarks |
|--------------|--------------|------------|------------------|---------------------|---------|
| **Brute Force** | Check reachability for each node | Mutual reachability checks | O(V * (V + E)^2) | O(V + E) | Not feasible for large graphs |
| **Kosaraju's Algorithm** | Two-pass DFS with finishing times | DFS + reverse graph | O(V + E) | O(V + E) | Efficient and widely used |
| **Tarjan's Algorithm** | Single pass with low-link values | DFS with low-link tracking | O(V + E) | O(V + E) | Most optimal in linear time |

---

## **Final Tips**
- Use **Kosaraju's or Tarjan's** for large graphs to achieve linear time.
- Understand the concept of **finishing times** and **low-link values**.
- Recognize that **SCCs** correspond to strongly connected subgraphs where mutual reachability exists.

---

**End of Revision Note**
