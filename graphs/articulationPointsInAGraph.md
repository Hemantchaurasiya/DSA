# Articulation Points in a Graph

---

## Introduction
**Articulation Points (or Cut Vertices)** are vertices in a connected graph such that removing them (and their associated edges) increases the number of connected components. Finding articulation points helps identify critical points in networks, such as network resilience, critical infrastructure points, etc.

---

## 1. Brute Force Approach

### **Core Idea**
- For each vertex, remove it from the graph.
- Check if the remaining graph is still connected.
- If not, the vertex is an articulation point.

### **Algorithm**
1. For each vertex `v` in the graph:
   - Remove `v` and all edges incident on `v`.
   - Check if the graph is still connected (using DFS/BFS).
   - If disconnected, mark `v` as an articulation point.
   - Restore `v` and its edges.
   
### **Java Code**
```java
import java.util.*;

public class ArticulationPointsBruteForce {
    static int V;
    static List<List<Integer>> graph;

    public static void main(String[] args) {
        V = 5;
        graph = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            graph.add(new ArrayList<>());
        }
        // Example graph
        addEdge(0, 1);
        addEdge(1, 2);
        addEdge(2, 3);
        addEdge(3, 4);
        addEdge(4, 1);
        
        findArticulationPoints();
    }

    static void addEdge(int u, int v) {
        graph.get(u).add(v);
        graph.get(v).add(u);
    }

    static void findArticulationPoints() {
        boolean[] isArticulation = new boolean[V];

        for (int v = 0; v < V; v++) {
            // Remove the vertex v
            List<List<Integer>> tempGraph = cloneGraph();
            removeVertex(tempGraph, v);
            // Check connectivity
            if (!isConnected(tempGraph, v)) {
                isArticulation[v] = true;
            }
        }

        for (int i = 0; i < V; i++) {
            if (isArticulation[i]) {
                System.out.println("Articulation Point: " + i);
            }
        }
    }

    static List<List<Integer>> cloneGraph() {
        List<List<Integer>> clone = new ArrayList<>();
        for (List<Integer> neighbors : graph) {
            clone.add(new ArrayList<>(neighbors));
        }
        return clone;
    }

    static void removeVertex(List<List<Integer>> g, int v) {
        g.set(v, new ArrayList<>()); // Remove all edges from v
        for (int i = 0; i < g.size(); i++) {
            g.get(i).remove(Integer.valueOf(v));
        }
    }

    static boolean isConnected(List<List<Integer>> g, int start) {
        boolean[] visited = new boolean[V];
        dfs(g, start, visited);
        for (int i = 0; i < V; i++) {
            if (i != start && g.get(i).size() > 0 && !visited[i]) {
                return false;
            }
        }
        return true;
    }

    static void dfs(List<List<Integer>> g, int u, boolean[] visited) {
        visited[u] = true;
        for (int v : g.get(u)) {
            if (!visited[v]) {
                dfs(g, v, visited);
            }
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(V * (V + E))  
  For each vertex, we perform a DFS/BFS to check connectivity, which takes O(V + E). Repeating for V vertices results in O(V * (V + E)).

- **Space Complexity:** O(V + E)  
  For storing the graph and auxiliary data structures during each check.

---

## 2. Better Solution (Using DFS and Low-Link Values)

### **Core Idea**
- Use **Depth-First Search (DFS)** to discover vertices and compute **low-link values** for each node.
- A node `u` is an articulation point if:
  - `u` is root of DFS and has two or more children.
  - `u` is not root and has a child `v` such that no vertex in subtree rooted at `v` can connect to ancestors of `u`.

### **Algorithm**
1. Initialize:
   - Arrays `discovery[]` and `low[]` to store discovery times and low-link values.
   - A parent array to keep track of DFS tree.
2. DFS traversal:
   - Assign discovery time and low value.
   - For each child:
     - Recurse DFS.
     - Update low-link values.
     - Check articulation point conditions.
3. Record articulation points during traversal.

### **Java Code**
```java
import java.util.*;

public class ArticulationPointsDFS {
    static int V;
    static List<List<Integer>> graph;
    static int time = 0;
    static boolean[] visited, articulationPoints;
    static int[] disc, low, parent;

    public static void main(String[] args) {
        V = 5;
        graph = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            graph.add(new ArrayList<>());
        }
        addEdge(0, 1);
        addEdge(1, 2);
        addEdge(2, 3);
        addEdge(3, 4);
        addEdge(4, 1);

        findArticulationPoints();
    }

    static void addEdge(int u, int v) {
        graph.get(u).add(v);
        graph.get(v).add(u);
    }

    static void findArticulationPoints() {
        visited = new boolean[V];
        disc = new int[V];
        low = new int[V];
        parent = new int[V];
        articulationPoints = new boolean[V];

        Arrays.fill(parent, -1);
        Arrays.fill(articulationPoints, false);
        Arrays.fill(visited, false);

        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                dfs(i);
            }
        }

        for (int i = 0; i < V; i++) {
            if (articulationPoints[i]) {
                System.out.println("Articulation Point: " + i);
            }
        }
    }

    static void dfs(int u) {
        visited[u] = true;
        disc[u] = low[u] = ++time;
        int children = 0;

        for (int v : graph.get(u)) {
            if (!visited[v]) {
                children++;
                parent[v] = u;
                dfs(v);
                low[u] = Math.min(low[u], low[v]);

                // Condition 1: u is root of DFS tree and has two or more children
                if (parent[u] == -1 && children > 1) {
                    articulationPoints[u] = true;
                }

                // Condition 2: u is not root and low value of one of its children is more than discovery value of u
                if (parent[u] != -1 && low[v] >= disc[u]) {
                    articulationPoints[u] = true;
                }
            } else if (v != parent[u]) {
                low[u] = Math.min(low[u], disc[v]);
            }
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(V + E)  
  DFS traverses all vertices and edges once.
  
- **Space Complexity:** O(V + E)  
  For storing the graph, recursion stack, and auxiliary arrays.

---

## 3. **Most Optimal Solution: Tarjan's Algorithm / DFS with Low-Link Values**
- The above DFS approach is the standard, optimal method.
- It leverages **Tarjan's algorithm** concepts to efficiently identify articulation points in linear time.

---

## **Dry Run Example**

### Input Graph:
```
Vertices: 5
Edges:
0 - 1
1 - 2
2 - 3
3 - 4
4 - 1
```

**Expected articulation points:** 1, 2, 3, 4 (in some configurations)

### Step-by-step (using DFS + Low-Link):

| Step | u | Discovery Time | Low Value | Parent | Action | Articulation? |
|-------|---|----------------|------------|---------|---------|--------------|
| 1     | 0 | 1              | 1          | -1      | Start DFS at 0 | No (single node) |
| 2     | 1 | 2              | 1          | 0       | DFS from 1 | Potential articulation |
| 3     | 2 | 3              | 1          | 1       | DFS from 2 | Potential articulation |
| 4     | 3 | 4              | 1          | 2       | DFS from 3 | Potential articulation |
| 5     | 4 | 5              | 1          | 3       | DFS from 4 | Potential articulation |
| ...   |   |                |            |         |         |               |

(Note: Actual low and discovery values are updated based on back edges and traversal order).

---

## **Summary**

| Approach | Core Idea | Algorithm | Time Complexity | Space Complexity | Remarks |
|------------|--------------|------------|------------------|---------------------|---------|
| **Brute Force** | Remove each vertex, check connectivity | Remove vertex, DFS/BFS | O(V * (V + E)) | O(V + E) | Simple but inefficient for large graphs |
| **DFS + Low-Link** | Use DFS tree and low-link values to detect articulation points | Standard DFS with low array | O(V + E) | O(V + E) | Most efficient and widely used |

---

## Final Tip
- For large graphs, always prefer **DFS + Low-Link** approach.
- Understand the conditions for articulation points:
  - Root of DFS tree with ≥2 children.
  - Non-root with a child that cannot connect to ancestors.

---

**End of Revision Note**
