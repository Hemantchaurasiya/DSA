# Largest Color Value in a Directed Graph

---

## Problem Statement
Given a directed graph where each node has a color represented by a lowercase letter, find the **largest number of occurrences of any single color** along any path in the graph. If the graph contains a cycle, return `-1` because no valid path exists.

**Input:**
- `colors`: String representing colors of nodes (e.g., `"abaca"` where each position corresponds to a node).
- `edges`: 2D array representing directed edges, e.g., `[[0,1],[0,2],[2,3],[3,4]]`.

**Output:**
- An integer representing the maximum frequency of any color along any path, or `-1` if a cycle exists.

---

## Approach 1: Brute Force (Naive Path Enumeration)

### **Core Idea:**
- Enumerate all paths in the graph.
- Count the maximum occurrence of any color along these paths.
- Detect cycles to avoid infinite enumeration.

### **Algorithm:**
1. For each node, perform DFS to explore all possible paths.
2. Maintain a count of colors along the current path.
3. Track the maximum count of any color found.
4. Detect cycles during DFS using visited states.
5. Since path enumeration is exponential, this approach is not practical for large graphs.

---

## Approach 2: Dynamic Programming with Topological Sorting (Optimal)

### **Core Idea:**
- Use **topological sorting** to process nodes in order.
- Maintain a **DP table** where `dp[node][color]` represents the maximum count of `color` along any path ending at `node`.
- Update DP based on predecessors and colors.

### **Algorithm:**
1. Build adjacency list of the graph.
2. Calculate in-degree for each node.
3. Perform **Kahn's Algorithm**:
   - Initialize a queue with all nodes having in-degree 0.
   - For each node dequeued:
     - For each neighbor:
       - Update `dp[neighbor][color]` based on `dp[current][color]`.
       - Decrement in-degree of neighbor.
       - If in-degree becomes 0, enqueue neighbor.
4. While processing, keep track of the maximum color count value.
5. If a cycle is detected (not all nodes processed), return `-1`.

---

## Java Implementation: Topological DP Approach

```java
import java.util.*;

public class LargestColorValue {
    public int largestPathValue(String colors, int[][] edges) {
        int n = colors.length();
        List<List<Integer>> graph = new ArrayList<>();
        int[] inDegree = new int[n];

        // Initialize graph
        for (int i = 0; i < n; i++) {
            graph.add(new ArrayList<>());
        }

        // Build graph and in-degree array
        for (int[] edge : edges) {
            graph.get(edge[0]).add(edge[1]);
            inDegree[edge[1]]++;
        }

        // Initialize DP table: dp[node][colorIndex]
        int[][] dp = new int[n][26];
        for (int i = 0; i < n; i++) {
            Arrays.fill(dp[i], 0);
        }

        // Queue for nodes with in-degree 0
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < n; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
                // Increment count for node's own color
                dp[i][colors.charAt(i) - 'a'] = 1;
            }
        }

        int maxColorCount = 0;
        int visitedNodes = 0;

        // Process nodes in topological order
        while (!queue.isEmpty()) {
            int node = queue.poll();
            visitedNodes++;
            for (int neighbor : graph.get(node)) {
                // Update DP for neighbor
                for (int c = 0; c < 26; c++) {
                    dp[neighbor][c] = Math.max(dp[neighbor][c], dp[node][c]);
                }
                // Increment count for neighbor's color
                dp[neighbor][colors.charAt(neighbor) - 'a']++;
                // Decrease in-degree and enqueue if zero
                inDegree[neighbor]--;
                if (inDegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Check if cycle exists
        if (visitedNodes != n) {
            return -1; // cycle detected
        }

        // Find maximum color count
        for (int i = 0; i < n; i++) {
            for (int c = 0; c < 26; c++) {
                maxColorCount = Math.max(maxColorCount, dp[i][c]);
            }
        }
        return maxColorCount;
    }
}
```

---

## **Complexity Analysis:**

| Aspect | Description |
|---------|--------------|
| **Time Complexity** | **O(N + E * 26)**, where `N` is the number of nodes, `E` is the number of edges, and 26 is for each alphabet character. Updating DP for each edge involves iterating over 26 colors. |
| **Space Complexity** | **O(N * 26) + O(N + E)** for DP table, graph, and auxiliary data structures. |

---

## **Dry Run Example:**

### Input:
```plaintext
colors = "abaca"
edges = [[0,1], [0,2], [2,3], [3,4]]
```

### Step-by-step:
| Step | Action | Variables / Data Structures | Result |
|---------|---------|------------------------------|---------|
| 1 | Build graph | 0 -> 1, 0 -> 2, 2 -> 3, 3 -> 4 | Graph adjacency list |
| 2 | Calculate in-degree | inDegree[0]=0, inDegree[1]=1, inDegree[2]=1, inDegree[3]=1, inDegree[4]=1 | Initialize in-degree array |
| 3 | Initialize DP | All zeros, set dp[0][ 'a' - 'a' ] = 1 | Starting from node 0 with color 'a' |
| 4 | Queue = [0] | | Process node 0: update neighbors 1 and 2 |
| 5 | Process node 0 | dp[1]['a']=max(0,1)=1, dp[2]['a']=max(0,1)=1 | inDegree[1]=0, inDegree[2]=0, enqueue 1 and 2 |
| 6 | Process node 1 | no outgoing edges | |
| 7 | Process node 2 | update 3: dp[3][ 'a' - 'a' ]=max(0,1)=1 | inDegree[3]=0, enqueue 3 |
| 8 | Process node 3 | update 4: dp[4][ 'a' - 'a' ]=max(0,1)=1 | inDegree[4]=0, enqueue 4 |
| 9 | Process node 4 | no outgoing edges | |
| 10 | Final DP table contains max counts | Max count is 2 for 'a' in node 0 path | Result: 2 |

---

## **Summary:**

- Build a graph and compute in-degrees.
- Use **topological sort** (Kahn's Algorithm).
- Maintain a **DP table** to keep track of maximum color frequencies along paths.
- Detect cycles by verifying if all nodes are processed.
- The approach efficiently computes the largest color value along any path.

---

Practice with various graphs and color combinations to master this problem!
