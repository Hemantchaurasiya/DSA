# Critical Connections in a Network

---

## Introduction
**Critical connections** (also known as **bridges** or **articulation edges**) are edges in a network (undirected graph) that, if removed, increase the number of connected components. These edges are crucial for network robustness; their failure can fragment the network.

---

## 1. Brute Force Approach

### **Core Idea**
- For each edge:
  - Remove the edge.
  - Check if the graph remains connected.
  - If not, the edge is critical (a bridge).

### **Algorithm**
1. Iterate over each edge `(u, v)`:
   - Remove `(u, v)` from the graph.
   - Check if all nodes are still reachable from a starting node using BFS/DFS.
   - If not reachable, `(u, v)` is a critical connection.
   - Restore the edge.
   
### **Java Code**
```java
import java.util.*;

public class CriticalConnectionsBruteForce {
    static int V;
    static List<List<Integer>> graph;

    public static void main(String[] args) {
        V = 5;
        graph = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            graph.add(new ArrayList<>());
        }
        addEdge(0, 1);
        addEdge(1, 2);
        addEdge(2, 0);
        addEdge(1, 3);
        addEdge(3, 4);
        findCriticalConnections();
    }

    static void addEdge(int u, int v) {
        graph.get(u).add(v);
        graph.get(v).add(u);
    }

    static void findCriticalConnections() {
        List<List<Integer>> criticalEdges = new ArrayList<>();
        List<int[]> edges = new ArrayList<>();

        // Collect edges
        for (int u = 0; u < V; u++) {
            for (int v : graph.get(u)) {
                if (u < v) {
                    edges.add(new int[]{u, v});
                }
            }
        }

        for (int[] edge : edges) {
            // Remove edge
            removeEdge(edge[0], edge[1]);

            if (!isConnected()) {
                criticalEdges.add(Arrays.asList(edge[0], edge[1]));
            }

            // Restore edge
            addEdge(edge[0], edge[1]);
        }

        System.out.println("Critical Connections: " + criticalEdges);
    }

    static void removeEdge(int u, int v) {
        graph.get(u).remove(Integer.valueOf(v));
        graph.get(v).remove(Integer.valueOf(u));
    }

    static boolean isConnected() {
        boolean[] visited = new boolean[V];
        dfs(0, visited);
        for (boolean v : visited) {
            if (!v) return false;
        }
        return true;
    }

    static void dfs(int u, boolean[] visited) {
        visited[u] = true;
        for (int v : graph.get(u)) {
            if (!visited[v]) {
                dfs(v, visited);
            }
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(E * (V + E))  
  For each edge, we perform a connectivity check via DFS/BFS which takes O(V + E). Repeating for all edges results in the above complexity.

- **Space Complexity:** O(V + E)  
  For storing the graph and auxiliary data.

---

## 2. Better Solution: Tarjan's Algorithm for Bridges

### **Core Idea**
- Use **DFS traversal** to find **bridges** using **discovery times** and **low-link values**.
- An edge `(u, v)` is a bridge if:
  - `low[v] > disc[u]`, meaning no back edge from `v` or its descendants to `u` or ancestors of `u`.

### **Algorithm**
1. Initialize arrays:
   - `discovery[]`: time when a node is first visited.
   - `low[]`: earliest visited vertex reachable from subtree rooted at that node.
2. Perform DFS from each unvisited node:
   - Assign `discovery[]` and `low[]`.
   - For each neighbor:
     - If unvisited, recurse DFS.
     - Update `low[]` based on child's `low[]`.
     - Check if `low[v] > disc[u]`; if yes, `(u, v)` is a bridge.
3. Collect all such edges.

### **Java Code**
```java
import java.util.*;

public class TarjanBridges {
    static int V;
    static List<List<Integer>> graph;
    static int time = 0;
    static int[] disc, low;
    static List<List<Integer>> bridges;

    public static void main(String[] args) {
        V = 5;
        graph = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            graph.add(new ArrayList<>());
        }
        addEdge(0, 1);
        addEdge(1, 2);
        addEdge(2, 0);
        addEdge(1, 3);
        addEdge(3, 4);

        findBridges();
        System.out.println("Bridges: " + bridges);
    }

    static void addEdge(int u, int v) {
        graph.get(u).add(v);
        graph.get(v).add(u);
    }

    static void findBridges() {
        disc = new int[V];
        low = new int[V];
        Arrays.fill(disc, -1);
        bridges = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            if (disc[i] == -1) {
                dfs(i, -1);
            }
        }
    }

    static void dfs(int u, int parent) {
        disc[u] = low[u] = ++time;
        for (int v : graph.get(u)) {
            if (v == parent) continue;
            if (disc[v] == -1) {
                dfs(v, u);
                low[u] = Math.min(low[u], low[v]);
                if (low[v] > disc[u]) {
                    bridges.add(Arrays.asList(u, v));
                }
            } else {
                low[u] = Math.min(low[u], disc[v]);
            }
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(V + E)  
  DFS traversal for all nodes and edges.
- **Space Complexity:** O(V + E)  
  For storing the graph, auxiliary arrays, and recursion stack.

---

## 3. Most Optimal Solution: Tarjan's Algorithm for Bridges

### **Core Idea**
- Use a single DFS traversal to find all bridges efficiently based on discovery and low-link values.
- This approach is linear in complexity and optimal.

---

## **Dry Run Example**

### Input Graph:
```
Vertices: 5
Edges:
0 - 1
1 - 2
2 - 0
1 - 3
3 - 4
```

### Expected Critical Edges:
- (1, 3)
- (3, 4)

### Step-by-step (Tarjan’s algorithm):

| Step | u | disc[u] | low[u] | Action | Detects bridge? |
|-------|---|---------|--------|---------|-----------------|
| 1     | 0 | 1       | 1      | DFS start | No |
| 2     | 1 | 2       | 1      | DFS from 1 | No |
| 3     | 2 | 3       | 1      | DFS from 2 | No |
| 4     | 0 | 3       | 1      | Back edge to 0 | No |
| 5     | 1 | 2       | 1      | Visit 3, DFS from 3 | Yes, (1,3) |
| 6     | 3 | 4       | 4      | DFS from 3 | Yes, (3,4) |

---

## **Summary**

| Approach | Core Idea | Algorithm | Time Complexity | Space Complexity | Remarks |
|--------------|--------------|------------|------------------|---------------------|---------|
| **Brute Force** | Remove each edge, check connectivity | Remove edge + BFS/DFS | O(E * (V + E)) | O(V + E) | Inefficient for large graphs |
| **Tarjan's for Bridges** | DFS with discovery and low-link values | Find bridges in O(V + E) | O(V + E) | O(V + E) | Most efficient and standard method |

---

## **Final Tips**
- Use **Tarjan's algorithm** for large graphs for linear time complexity.
- Focus on understanding **discovery times** and **low-link values**.
- Recognize that **bridges** are edges whose removal increases the number of connected components.

---

**End of Revision Note**
