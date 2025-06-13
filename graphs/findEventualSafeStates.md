# Find Eventual Safe States

---

## Problem Statement
Given a directed graph, identify all **safe nodes** — nodes from which every possible path leads to a terminal node (a node with no outgoing edges) or a node that is itself safe. In other words, find nodes that are not part of any cycle and do not lead to cycles.

**Example:**
```plaintext
Input: graph = [[1,2],[2,3],[5],[0],[5],[],[]]
Output: [2,4,5,6]
```

---

## Approach 1: Brute Force - Detect Cycles from Each Node

### **Core Idea:**
- For each node, perform a DFS to check if starting from that node leads to a cycle.
- If a node is part of a cycle or leads to a cycle, it is unsafe.
- Nodes that do not lead to cycles are safe.

### **Algorithm:**
1. For each node:
   - Use DFS to detect if there's a cycle starting from that node.
2. Cycle detection:
   - Maintain a recursion stack (visited nodes in current path).
   - If a node is revisited in the current recursion path, a cycle exists.
3. If no cycle is detected from a node, mark it as safe.
4. Collect all such safe nodes.

### **Java Code:**
```java
import java.util.*;

public class FindEventualSafeStates {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            adj.add(new ArrayList<>());
        }
        for (int i = 0; i < n; i++) {
            for (int nei : graph[i]) {
                adj.get(i).add(nei);
            }
        }

        boolean[] visited = new boolean[n];
        boolean[] pathVisited = new boolean[n];
        List<Integer> safeNodes = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            if (dfs(i, adj, visited, pathVisited))
                continue; // node leads to cycle, not safe
            else
                safeNodes.add(i);
        }

        return safeNodes;
    }

    private boolean dfs(int node, List<List<Integer>> adj, boolean[] visited, boolean[] pathVisited) {
        if (pathVisited[node]) return true; // cycle detected
        if (visited[node]) return false;    // already processed, no cycle from this node

        visited[node] = true;
        pathVisited[node] = true;

        for (int neighbor : adj.get(node)) {
            if (dfs(neighbor, adj, visited, pathVisited))
                return true; // cycle found downstream
        }

        pathVisited[node] = false; // backtrack
        return false; // no cycle detected from this node
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N*(V+E))**, where N = number of nodes, because for each node, DFS is performed, potentially traversing the entire graph.
- **Space Complexity:** **O(N + E)** for storing the adjacency list and recursion stack.

---

## Approach 2: Topological Sorting with Cycle Detection (Using Kahn's Algorithm or DFS)

### **Core Idea:**
- Nodes involved in cycles are unsafe.
- Nodes with no outgoing edges are terminal and safe.
- Use **reverse graph traversal** or **indegree analysis** to identify safe nodes by repeatedly removing terminal nodes and their influence.

### **Method 1: Using Reverse Graph & BFS (Kahn's Algorithm):**
1. Build a reverse graph where edges point from neighbors to the current node.
2. Compute the indegree for each node (number of outgoing edges in the original graph).
3. Initialize a queue with all terminal nodes (nodes with zero outgoing edges).
4. While the queue is not empty:
   - Pop a node and consider it safe.
   - For each predecessor in the reverse graph:
     - Decrement its indegree.
     - If its indegree becomes zero, add it to the queue.
5. The nodes processed are all safe nodes.

### **Java Code (Kahn's Algorithm):**
```java
import java.util.*;

public class FindEventualSafeStates {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        List<List<Integer>> revGraph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            revGraph.add(new ArrayList<>());
        }
        int[] outDegree = new int[n];

        // Build reverse graph and compute outdegree
        for (int i = 0; i < n; i++) {
            outDegree[i] = graph[i].length;
            for (int nei : graph[i]) {
                revGraph.get(nei).add(i);
            }
        }

        Queue<Integer> queue = new LinkedList<>();
        // Initialize queue with terminal nodes (outDegree=0)
        for (int i = 0; i < n; i++) {
            if (outDegree[i] == 0)
                queue.offer(i);
        }

        List<Integer> safeNodes = new ArrayList<>();
        while (!queue.isEmpty()) {
            int node = queue.poll();
            safeNodes.add(node);
            for (int neighbor : revGraph.get(node)) {
                outDegree[neighbor]--;
                if (outDegree[neighbor] == 0)
                    queue.offer(neighbor);
            }
        }

        Collections.sort(safeNodes);
        return safeNodes;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N + E)**, as each node and edge is processed once.
- **Space Complexity:** **O(N + E)** for adjacency and reverse adjacency lists.

---

## Approach 3: DFS with Memoization (Most Optimal for Cycle Detection & Safe Nodes)

### **Core Idea:**
- Use DFS with memoization to mark nodes as **safe** or **unsafe**.
- Once a node’s safety status is computed, reuse it to avoid repeated DFS calls.
- Detect cycles during DFS; nodes involved in cycles are unsafe.

### **Algorithm:**
1. Maintain a `status` array:
   - **0**: unprocessed
   - **1**: processing (in recursion stack)
   - **2**: safe
   - **-1**: unsafe (part of or leads to a cycle)
2. For each node:
   - If already processed, return its status.
   - If currently processing, a cycle is detected → mark unsafe.
   - Recurse over neighbors:
     - If any neighbor leads to a cycle, mark current node unsafe.
   - If all neighbors are safe, mark current node safe.
3. Collect all nodes marked safe.

### **Java Code:**
```java
import java.util.*;

public class FindEventualSafeStates {
    public List<Integer> eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        int[] status = new int[n]; // 0=unprocessed, 1=processing, 2=safe, -1=unsafe
        List<Integer> safeNodes = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            if (dfs(i, graph, status))
                safeNodes.add(i);
        }

        Collections.sort(safeNodes);
        return safeNodes;
    }

    private boolean dfs(int node, int[][] graph, int[] status) {
        if (status[node] != 0) {
            return status[node] == 2; // already processed
        }

        status[node] = 1; // mark as processing
        for (int nei : graph[node]) {
            if (status[nei] == 1 || (status[nei] == 0 && !dfs(nei, graph, status))) {
                status[node] = -1; // unsafe
                return false;
            }
        }
        status[node] = 2; // safe
        return true;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N + E)**, since each node and edge is processed once.
- **Space Complexity:** **O(N + E)** for recursion stack and status array.

---

## **Summary Table**

| Approach | Key Idea | Cycle Detection | Complexity | Notes |
|------------|--------------|------------------|--------------|--------|
| Brute Force DFS | Detect cycles from each node individually | Yes | O(N*(V+E)), O(N+E) | Straightforward but less efficient for large graphs |
| Topological Sorting (Kahn's) | Remove terminal nodes iteratively | Implicit | O(N+E), O(N+E) | Efficient, suitable for large graphs |
| DFS with Memoization | Mark nodes as safe/unsafe during traversal | Yes | O(N+E), O(N+E) | Most elegant and optimized |

---

## **Final Tips:**
- **Cycle detection** is crucial as nodes involved in cycles are unsafe.
- Use **memoization** or **topological sorting** for optimal performance.
- For large graphs, prefer the **topological approach** with BFS.
- For understanding, start with the **brute-force DFS** approach.

---

Feel free to practice with different graph configurations to solidify your understanding!
