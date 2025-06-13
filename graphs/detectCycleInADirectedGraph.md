# Detect Cycle in a Directed Graph

This comprehensive guide covers multiple approaches to detect cycles in a **directed graph**, from naive to optimal solutions. It is tailored for quick revision and interview preparation.

---

## 1. Brute Force Approach

### **Approach Name:**  
**DFS with Recursion Stack (Naive Detection)**

### **Core Idea:**  
- Use DFS to explore nodes.
- Detect a cycle if during DFS, a node is revisited **while it is still in the recursion stack** (meaning a back edge exists).

### **Algorithm:**  
1. Maintain two boolean arrays:
   - **visited[]**: marks nodes visited at least once.
   - **recStack[]**: marks nodes currently in the recursion stack.
2. For each unvisited node:
   - Call DFS.
   - Mark the node as visited and in recStack.
   - For each neighbor:
     - If not visited, recurse.
     - If visited and in recStack, cycle detected.
3. Remove node from recStack after exploring all neighbors.
4. If any DFS call detects a cycle, return true.

### **Java Code:**
```java
public class DetectCycleInDirectedGraph {

    public boolean isCyclic(List<List<Integer>> adj, int V) {
        boolean[] visited = new boolean[V];
        boolean[] recStack = new boolean[V];

        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                if (dfs(i, adj, visited, recStack))
                    return true;
            }
        }
        return false;
    }

    private boolean dfs(int node, List<List<Integer>> adj, boolean[] visited, boolean[] recStack) {
        visited[node] = true;
        recStack[node] = true;

        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                if (dfs(neighbor, adj, visited, recStack))
                    return true;
            } else if (recStack[neighbor]) {
                // Cycle detected
                return true;
            }
        }
        recStack[node] = false; // backtrack
        return false;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(V + E)**  
  Each node and edge is explored once during DFS.  
- **Space Complexity:** **O(V)**  
  For visited and recursion stack arrays.

---

## 2. Better Solution (Topological Sort using BFS - Kahn's Algorithm)

### **Approach Name:**  
**Detect Cycle using Topological Sorting (BFS)**

### **Core Idea:**  
- If a graph has a topological ordering, it is acyclic.
- Count the number of nodes in topological order using BFS.
- If the count of processed nodes equals total nodes, no cycle exists. Otherwise, cycle exists.

### **Algorithm:**  
1. Compute in-degree for all nodes.
2. Enqueue nodes with zero in-degree.
3. While queue is not empty:
   - Dequeue node, increment count.
   - For each neighbor:
     - Reduce its in-degree by 1.
     - If in-degree becomes zero, enqueue it.
4. If count == V, graph is acyclic; else, cycle exists.

### **Java Code:**
```java
public class DetectCycleUsingTopoSort {

    public boolean isCyclic(List<List<Integer>> adj, int V) {
        int[] inDegree = new int[V];

        // Compute in-degree
        for (int i = 0; i < V; i++) {
            for (int neighbor : adj.get(i)) {
                inDegree[neighbor]++;
            }
        }

        Queue<Integer> queue = new LinkedList<>();
        // Enqueue nodes with in-degree zero
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0)
                queue.offer(i);
        }

        int count = 0;
        while (!queue.isEmpty()) {
            int node = queue.poll();
            count++;
            for (int neighbor : adj.get(node)) {
                inDegree[neighbor]--;
                if (inDegree[neighbor] == 0)
                    queue.offer(neighbor);
            }
        }
        // If all nodes are processed, no cycle; else cycle exists
        return count != V;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(V + E)**  
  Each node and edge is processed once.  
- **Space Complexity:** **O(V)**  
  For in-degree array and queue.

---

## 3. Most Optimal Solution (Using DFS with Topological Sorting / Detecting Back Edges)

### **Approach Name:**  
**DFS with Recursion Stack (Efficient Cycle Detection)**

*(This is similar to the first approach but emphasizes the use of DFS recursion stack for cycle detection, which is standard for directed graphs.)*

### **Core Idea:**  
- During DFS traversal, if we revisit a node that is currently in the recursion stack, a cycle exists.
- This method efficiently detects cycles without needing to perform a topological sort explicitly.

### **Algorithm:**  
Same as the first approach, but emphasizing the detection of back edges via recursion stack.

### **Java Code:**  
*(Same as Approach 1)*

```java
// Reusing the first approach's code as it is optimal for cycle detection
public class DetectCycleInDirectedGraph {

    public boolean isCyclic(List<List<Integer>> adj, int V) {
        boolean[] visited = new boolean[V];
        boolean[] recStack = new boolean[V];

        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                if (dfs(i, adj, visited, recStack))
                    return true;
            }
        }
        return false;
    }

    private boolean dfs(int node, List<List<Integer>> adj, boolean[] visited, boolean[] recStack) {
        visited[node] = true;
        recStack[node] = true;

        for (int neighbor : adj.get(node)) {
            if (!visited[neighbor]) {
                if (dfs(neighbor, adj, visited, recStack))
                    return true;
            } else if (recStack[neighbor]) {
                // Cycle detected via back edge
                return true;
            }
        }
        recStack[node] = false; // Backtrack
        return false;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(V + E)**  
  DFS explores each vertex and edge once.  
- **Space Complexity:** **O(V)**  
  For visited and recursion stack arrays.

---

## **Summary Table**

| **Approach** | **Core Idea** | **Method** | **Detection** | **Time Complexity** | **Space Complexity** |
|--------------|----------------|------------|--------------|---------------------|----------------------|
| **1. DFS with Recursion Stack** | Detect back edge during DFS | DFS + Recursion stack | Back edge (node in recStack) | O(V + E) | O(V) |
| **2. Topological Sort (BFS)** | Use in-degree and queue | Kahn's Algorithm | If total processed nodes != V | O(V + E) | O(V) |
| **3. DFS with Recursion Stack (Optimal)** | Same as approach 1, standard detection | DFS + Recursion stack | Back edge detection | O(V + E) | O(V) |

---

## **Final Tips for Interviews:**
- For detecting cycles in directed graphs, **DFS with recursion stack** is the most straightforward and widely used.
- **Topological sort** can also be used to detect cycles by checking if a topological order exists.
- Always consider the graph's representation (adjacency list) for efficiency.
- Handle disconnected components by running DFS/BFS from each unvisited node.

---

**Happy Revising!**
