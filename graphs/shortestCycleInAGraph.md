# Shortest Cycle in a Graph

This guide provides a comprehensive overview of approaches to find the **shortest cycle** in a graph. It covers from naive methods to the most efficient algorithms, suitable for quick revision and interview prep.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Cycle Detection by Enumerating All Cycles**

### **Core Idea:**  
- Find all cycles in the graph and determine the minimum length among them.
- This involves exploring all possible paths to detect cycles, which is highly inefficient.

### **Algorithm:**  
1. For each node in the graph:
   - Perform DFS or backtracking to find all cycles starting from that node.
2. During DFS:
   - Keep track of visited nodes and the current path.
   - If a neighbor is already in the current path, a cycle is detected.
   - Record the cycle length (distance between the repeated node and current node).
3. After exploring all nodes and paths, select the minimum cycle length found.

### **Note:**  
This approach is **exponential** in complexity, impractical for large graphs, mainly conceptual.

### **Java Code:**  
(Not recommended due to inefficiency; omitted for brevity)

### **Complexity Analysis:**  
- **Time Complexity:** Exponential, O(V!), practically infeasible as it explores all paths.  
- **Space Complexity:** O(V) for recursion stack and visited path tracking.

---

## 2. BFS-based Approach (Most Efficient)

### **Approach Name:**  
**Breadth-First Search (BFS) for Shortest Cycle Detection**

### **Core Idea:**  
- Use BFS from each node to find the shortest cycle involving that node.
- BFS naturally finds shortest paths, making it suitable for detecting shortest cycles.

### **Algorithm:**  
1. For each node `u` in the graph:
   - Initialize a queue for BFS.
   - Maintain arrays:
     - `distance[]`: to store distance from `u`.
     - `parent[]`: to keep track of the BFS tree.
   - Set distance of `u` to 0, others to -1.
   - Enqueue `u`.
2. While queue is not empty:
   - Dequeue a node `v`.
   - For each neighbor `w` of `v`:
     - If `w` not visited:
       - Set `distance[w] = distance[v] + 1`.
       - Set `parent[w] = v`.
       - Enqueue `w`.
     - Else if `w` is visited and `w` is not the parent of `v`:
       - A cycle exists involving `v` and `w`.
       - Calculate cycle length: `distance[v] + distance[w] + 1`.
       - Update minimum cycle length if smaller.
3. Repeat for all nodes, track the global minimum cycle length.

### **Java Code:**
```java
import java.util.*;

public class ShortestCycleInGraph {
    public int findShortestCycle(List<List<Integer>> adj, int V) {
        int shortestCycle = Integer.MAX_VALUE;

        for (int start = 0; start < V; start++) {
            int[] dist = new int[V];
            int[] parent = new int[V];
            Arrays.fill(dist, -1);
            Arrays.fill(parent, -1);

            Queue<Integer> queue = new LinkedList<>();
            dist[start] = 0;
            queue.offer(start);

            while (!queue.isEmpty()) {
                int v = queue.poll();

                for (int neighbor : adj.get(v)) {
                    if (dist[neighbor] == -1) {
                        dist[neighbor] = dist[v] + 1;
                        parent[neighbor] = v;
                        queue.offer(neighbor);
                    } else if (parent[v] != neighbor && dist[neighbor] != -1) {
                        // Found a cycle
                        int cycleLength = dist[v] + dist[neighbor] + 1;
                        shortestCycle = Math.min(shortestCycle, cycleLength);
                    }
                }
            }
        }

        return shortestCycle == Integer.MAX_VALUE ? -1 : shortestCycle;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(V * (V + E))** — For each node, BFS runs in O(V + E).  
- **Space Complexity:** **O(V + E)** — For distance, parent arrays, and adjacency list.

---

## 3. Summary of Approaches

| **Approach** | **Core Idea** | **Detection Method** | **Time Complexity** | **Space Complexity** |
|--------------|----------------|----------------------|---------------------|----------------------|
| **1. Naive (All Cycles)** | Enumerate all cycles | Backtracking DFS | Exponential | O(V) |
| **2. BFS from each node** | Shortest cycle detection | BFS + parent tracking | O(V*(V+E)) | O(V+E) |

---

## **Dry Run Example**

Suppose we have the following undirected graph:

```
Vertices: 0, 1, 2, 3
Edges:
0 - 1
1 - 2
2 - 0
2 - 3
```

This graph contains a cycle of length 3: `0 - 1 - 2 - 0`.

### BFS from node 0:
- Initialize `dist = [0, -1, -1, -1]`, `parent = [-1, -1, -1, -1]`.
- Queue: [0]
- Dequeue 0:
  - Neighbors: 1, 2
  - Set `dist[1]=1`, `parent[1]=0`; enqueue 1
  - Set `dist[2]=1`, `parent[2]=0`; enqueue 2
- Dequeue 1:
  - Neighbors: 0, 2
  - 0 visited, parent of 1 is 0 → ignore
  - 2 visited, parent of 2 is 0 → 2's neighbor 1 is visited, but parent of 2 is 0, so cycle detected:
    - Cycle length = `dist[1] + dist[2] + 1` = 1 + 1 + 1 = 3
- Minimum cycle length so far: 3

Similarly, BFS from other nodes will also detect this cycle, confirming the shortest cycle length as 3.

---

## **Final Tips for Interviews:**
- Use BFS from each node for shortest cycle detection in unweighted graphs.
- For weighted graphs, consider algorithms like Johnson's or Dijkstra's for specific variants.
- Keep track of parent nodes during BFS to detect back edges indicating cycles.
- For dense graphs, BFS-based solutions are generally efficient.

---

**Happy Revising!**
