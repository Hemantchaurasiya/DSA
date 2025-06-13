# Is Graph Bipartite?

This comprehensive guide covers methods to determine if a graph is bipartite. It includes from naive approaches to the most optimal solutions, ideal for interview prep and quick revision.

---

## 1. Brute Force Approach (Conceptual)

### **Approach Name:**  
**Naive or Brute Force Checking via Coloring**

### **Core Idea:**  
- Attempt to color the entire graph using two colors (say, 0 and 1).
- If at any point a conflict occurs (neighbor has the same color), the graph isn't bipartite.
- This is essentially a coloring problem.

### **Algorithm:**  
1. Initialize an array `color[]` with -1 indicating uncolored nodes.
2. For each node:
   - If it's not colored, assign a color (0).
   - Perform DFS/BFS:
     - For each neighbor:
       - If uncolored, assign the opposite color.
       - If already colored and has the same color as the current node, return false (not bipartite).
3. If no conflict is found in all components, return true (graph is bipartite).

*Note:* This is not truly brute-force, but a straightforward method.

### **Java Code:**
```java
public class BipartiteGraph {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] color = new int[n];
        Arrays.fill(color, -1);
        
        for (int i = 0; i < n; i++) {
            if (color[i] == -1) {
                if (!dfs(graph, i, 0, color))
                    return false;
            }
        }
        return true;
    }
    
    private boolean dfs(int[][] graph, int node, int c, int[] color) {
        color[node] = c;
        for (int neighbor : graph[node]) {
            if (color[neighbor] == -1) {
                if (!dfs(graph, neighbor, 1 - c, color))
                    return false;
            } else if (color[neighbor] == c) {
                return false; // Same color neighbor found
            }
        }
        return true;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(V + E)** — Each node and edge is processed once during DFS.  
- **Space Complexity:** **O(V)** — For the color array and recursion stack.

---

## 2. BFS-based Approach (Most Efficient)

### **Approach Name:**  
**Breadth-First Search (BFS) for Bipartite Checking**

### **Core Idea:**  
- Use BFS to assign alternating colors to levels.
- If a conflict arises (neighbor has the same color), the graph isn't bipartite.
- BFS ensures shortest level assignment, ideal for bipartite checking.

### **Algorithm:**  
1. Initialize a `color[]` array with -1 (uncolored).
2. For each unvisited node:
   - Assign color 0.
   - Use a queue to perform BFS:
     - For each neighbor:
       - If uncolored, assign the opposite color.
       - If already colored and same as current node, return false.
3. If no conflicts after exploring all components, return true.

### **Java Code:**
```java
public class BipartiteGraph {
    public boolean isBipartite(int[][] graph) {
        int n = graph.length;
        int[] color = new int[n];
        Arrays.fill(color, -1);
        
        for (int i = 0; i < n; i++) {
            if (color[i] == -1) {
                if (!bfsCheck(graph, i, color))
                    return false;
            }
        }
        return true;
    }
    
    private boolean bfsCheck(int[][] graph, int start, int[] color) {
        Queue<Integer> queue = new LinkedList<>();
        color[start] = 0;
        queue.offer(start);
        
        while (!queue.isEmpty()) {
            int node = queue.poll();
            for (int neighbor : graph[node]) {
                if (color[neighbor] == -1) {
                    color[neighbor] = 1 - color[node];
                    queue.offer(neighbor);
                } else if (color[neighbor] == color[node]) {
                    return false; // Same color neighbor found
                }
            }
        }
        return true;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(V + E)** — Each node and edge visited once.  
- **Space Complexity:** **O(V)** — For color array and queue.

---

## 3. Summary of Approaches

| **Approach** | **Core Idea** | **Detection Method** | **Time Complexity** | **Space Complexity** |
|--------------|----------------|----------------------|---------------------|----------------------|
| **1. DFS Coloring** | Recursive coloring with conflict detection | DFS traversal with color assignment | O(V + E) | O(V) |
| **2. BFS Coloring** | Level-based coloring with conflict detection | BFS traversal with color assignment | O(V + E) | O(V) |

---

## **Dry Run Example**

Suppose the graph:

```
0 - 1
|   |
3 - 2
```

- Nodes: 0, 1, 2, 3
- Edges: (0-1), (1-2), (2-3), (3-0)

This is a cycle of length 4, which is bipartite.

### BFS from node 0:

- Initialize `color = [-1, -1, -1, -1]`.
- Set `color[0]=0`, enqueue 0.
- Dequeue 0:
  - Neighbors: 1, 3
  - Assign `color[1]=1`, enqueue 1
  - Assign `color[3]=1`, enqueue 3
- Dequeue 1:
  - Neighbors: 0, 2
  - 0 already colored 0, different from 1 → OK
  - Assign `color[2]=0`, enqueue 2
- Dequeue 3:
  - Neighbors: 2, 0
  - 0 already colored 0, different from 1 → OK
  - 2 already colored 0, different from 3's color 1 → OK

No conflicts encountered; graph is bipartite.

---

## **Final Tips for Interviews:**
- BFS is generally preferred for bipartite checking due to simplicity and level assignment.
- Always handle disconnected components by iterating over all nodes.
- Use color arrays: 0 and 1 to represent two different sets.
- Detect conflicts when an edge connects nodes in the same color.

---

**Happy Revising!**
