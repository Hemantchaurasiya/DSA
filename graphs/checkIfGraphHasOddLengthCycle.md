# Check if Graph Has an Odd Length Cycle

This guide details approaches to determine whether an undirected or directed graph contains a cycle of odd length. Understanding this problem is crucial as it relates to graph bipartiteness and cycle detection. The note covers from naive to optimal solutions, suitable for quick revision and interview prep.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Cycle Search via DFS in All Paths**

### **Core Idea:**  
- For each node, explore all possible paths up to a certain length.
- Check if any cycle formed has odd length.
- Since this is computationally expensive, it is mainly conceptual.

### **Algorithm (High-Level):**  
1. For each node, perform DFS to find all cycles starting and ending at that node.
2. Keep track of the current path length.
3. If a back edge is found to a node in the current path:
   - Calculate cycle length (current depth - depth of the visited node).
   - If cycle length is odd, report true.
4. If no odd cycle is found after exploring all paths, return false.

### **Note:**  
This approach is highly inefficient (exponential time) and mainly serves as a theoretical foundation.

### **Java Code:**  
(Not practical due to complexity, so omitted for brevity)

### **Complexity Analysis:**  
- **Time Complexity:** Exponential, O(V!), practically infeasible for large graphs.  
- **Space Complexity:** O(V) for recursion stack and visited path tracking.

---

## 2. Better Solution (Using Graph Coloring / BFS / DFS)

### **Approach Name:**  
**Detecting Odd Cycle via Bipartiteness Check**

### **Core Idea:**  
- A graph contains an **odd cycle** if and only if it is **not bipartite**.
- A bipartite graph can be colored with two colors such that no adjacent vertices share the same color.
- If during coloring, a conflict occurs (adjacent nodes with same color), it indicates an odd cycle.

### **Algorithm:**  
1. Initialize a color array with all nodes uncolored (e.g., -1).
2. For each unvisited node:
   - Assign a color (say 0).
   - Perform BFS or DFS:
     - For each neighbor:
       - If uncolored, assign alternate color.
       - If already colored and has the same color as current node, then an odd cycle exists.
3. If conflict found, return true; else, after all components checked, no odd cycle.

### **Java Code:**
```java
public class OddCycleDetection {

    public boolean hasOddCycle(List<List<Integer>> adj, int V) {
        int[] color = new int[V];
        Arrays.fill(color, -1);

        for (int i = 0; i < V; i++) {
            if (color[i] == -1) {
                if (bfsCheck(i, adj, color))
                    return true; // Odd cycle exists
            }
        }
        return false; // No odd cycle
    }

    private boolean bfsCheck(int start, List<List<Integer>> adj, int[] color) {
        Queue<Integer> queue = new LinkedList<>();
        color[start] = 0;
        queue.offer(start);

        while (!queue.isEmpty()) {
            int node = queue.poll();

            for (int neighbor : adj.get(node)) {
                if (color[neighbor] == -1) {
                    color[neighbor] = 1 - color[node];
                    queue.offer(neighbor);
                } else if (color[neighbor] == color[node]) {
                    // Same color neighbor implies odd cycle
                    return true;
                }
            }
        }
        return false;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(V + E)** — Each node and edge is processed once during BFS.  
- **Space Complexity:** **O(V)** — For color array and queue.

---

## 3. Most Optimal Solution (Using BFS/DFS for Bipartiteness Check)

### **Approach Name:**  
**Bipartiteness Check via BFS/DFS**

### **Core Idea:**  
- An undirected graph has an **odd length cycle** if and only if it is **not bipartite**.
- Use BFS or DFS to color nodes with two colors.
- Detect conflict when an edge connects two nodes with the same color.

### **Algorithm (Summary):**  
- Same as approach 2, but emphasizing the bipartite nature.

### **Java Code:**
*(Same as Approach 2)*

```java
// Reuse the same code as above for bipartite detection
public class OddCycleDetection {

    public boolean hasOddCycle(List<List<Integer>> adj, int V) {
        int[] color = new int[V];
        Arrays.fill(color, -1);

        for (int i = 0; i < V; i++) {
            if (color[i] == -1) {
                if (bfsCheck(i, adj, color))
                    return true; // Odd cycle detected
            }
        }
        return false;
    }

    private boolean bfsCheck(int start, List<List<Integer>> adj, int[] color) {
        Queue<Integer> queue = new LinkedList<>();
        color[start] = 0;
        queue.offer(start);

        while (!queue.isEmpty()) {
            int node = queue.poll();

            for (int neighbor : adj.get(node)) {
                if (color[neighbor] == -1) {
                    color[neighbor] = 1 - color[node];
                    queue.offer(neighbor);
                } else if (color[neighbor] == color[node]) {
                    // Same color neighbor indicates an odd cycle
                    return true;
                }
            }
        }
        return false;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(V + E)**  
- **Space Complexity:** **O(V)**

---

## **Summary**

| **Approach** | **Core Idea** | **Detection Method** | **Time Complexity** | **Space Complexity** |
|--------------|----------------|----------------------|---------------------|----------------------|
| **1. Naive Search** | Explore all paths for odd cycles | Backtracking all paths (inefficient) | Exponential | O(V) |
| **2. BFS/DFS for Bipartiteness** | Graph is bipartite iff no odd cycle | Coloring with 2 colors | O(V + E) | O(V) |
| **3. Optimal (Same as 2)** | Same as approach 2 | Bipartite test | O(V + E) | O(V) |

---

## **Final Tips for Interviews:**
- Recognize that **odd cycles** in an undirected graph imply **non-bipartiteness**.
- Use BFS or DFS to check for bipartiteness efficiently.
- For directed graphs, the concept of odd cycles relates more to cycle detection, but the bipartite approach is mainly for undirected graphs.
- Always handle disconnected components by iterating through all nodes.

---

**Happy Revising!**
