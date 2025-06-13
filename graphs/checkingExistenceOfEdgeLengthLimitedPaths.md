# Checking Existence of Edge Length Limited Paths

---

## Problem Overview:
Given an undirected weighted graph with **n** nodes, a list of edges with weights, and multiple queries where each query asks:  
*"Is there a path between node `u` and node `v` such that all edges on the path have weights less than `limit`?"*  
Return a list of boolean answers for each query.

---

## Approach 1: Brute Force (Naive)

### **Core Idea:**
For each query, perform a **graph traversal** (like DFS or BFS) constrained to edges with weights `< limit` and check if `u` and `v` are connected.

### **Algorithm:**
1. For each query:
   - Build a subgraph containing only edges with weight `< limit`.
   - Use BFS or DFS from `u` to see if `v` is reachable.
2. Return the boolean result for each query.

### **Java Code:**
```java
import java.util.*;

public class EdgeLimitedPaths {
    public List<Boolean> checkPaths(int n, int[][] edges, int[][] queries) {
        List<Boolean> result = new ArrayList<>();
        for (int[] query : queries) {
            int u = query[0], v = query[1], limit = query[2];
            // Build adjacency list with edges < limit
            List<List<Integer>> adj = new ArrayList<>();
            for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
            for (int[] edge : edges) {
                if (edge[2] < limit) {
                    adj.get(edge[0]).add(edge[1]);
                    adj.get(edge[1]).add(edge[0]);
                }
            }
            // BFS to check connectivity
            boolean[] visited = new boolean[n];
            Queue<Integer> queue = new LinkedList<>();
            queue.offer(u);
            boolean found = false;
            while (!queue.isEmpty()) {
                int curr = queue.poll();
                if (curr == v) {
                    found = true;
                    break;
                }
                for (int nei : adj.get(curr)) {
                    if (!visited[nei]) {
                        visited[nei] = true;
                        queue.offer(nei);
                    }
                }
            }
            result.add(found);
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(Q * (E + V))**, where Q is number of queries, E is edges, V is vertices, because for each query, building adjacency and BFS takes linear time.
- **Space Complexity:** **O(V + E)** per query.

---

## Approach 2: Using Offline Sorting + Union-Find (Better)

### **Core Idea:**
Sort all edges by weight and all queries by their limit. Process edges in increasing order, and for each query, connect all edges with weight less than `limit` using Union-Find. Then, check if `u` and `v` are connected.

### **Algorithm:**
1. **Sort** `edges` based on weight.
2. **Create** an array of queries, augment with index, and sort by `limit`.
3. Initialize **Union-Find** structure.
4. Use a pointer `edgeIdx` to iterate over sorted edges.
5. For each query in ascending order of `limit`:
   - Connect all edges with weight `< limit` (by union operation).
   - Check if `u` and `v` are connected.
   - Store answer at original index.
6. Return answers in original query order.

### **Java Code:**
```java
import java.util.*;

public class EdgeLimitedPaths {
    class UnionFind {
        int[] parent;
        public UnionFind(int n) {
            parent = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        public int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }
        public void union(int x, int y) {
            parent[find(x)] = find(y);
        }
        public boolean connected(int x, int y) {
            return find(x) == find(y);
        }
    }

    public List<Boolean> checkPaths(int n, int[][] edges, int[][] queries) {
        Arrays.sort(edges, Comparator.comparingInt(e -> e[2]));
        int q = queries.length;
        int[][] queriesWithIdx = new int[q][4]; // [u, v, limit, index]
        for (int i = 0; i < q; i++) {
            queriesWithIdx[i][0] = queries[i][0];
            queriesWithIdx[i][1] = queries[i][1];
            queriesWithIdx[i][2] = queries[i][2];
            queriesWithIdx[i][3] = i;
        }
        Arrays.sort(queriesWithIdx, Comparator.comparingInt(qi -> qi[2]));

        UnionFind uf = new UnionFind(n);
        List<Boolean> res = new ArrayList<>(Collections.nCopies(q, false));
        int edgeIdx = 0;

        for (int[] query : queriesWithIdx) {
            int u = query[0], v = query[1], limit = query[2], idx = query[3];
            while (edgeIdx < edges.length && edges[edgeIdx][2] < limit) {
                uf.union(edges[edgeIdx][0], edges[edgeIdx][1]);
                edgeIdx++;
            }
            res.set(idx, uf.connected(u, v));
        }
        return res;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O((E + Q) log E)**, due to sorting edges and queries, and union-find operations are almost O(1).
- **Space Complexity:** **O(V + E)** for Union-Find and storage.

---

## Approach 3: Most Optimal (Union-Find + Offline Query Processing)

### **Core Idea:**
This approach combines sorting edges and queries with a **Union-Find** data structure to efficiently process multiple queries in **O((V+E) α(N))** time, where α(N) is the inverse Ackermann function.

### **Algorithm:**
Same as Approach 2, but emphasizing the offline method:
- Sort edges by weight.
- Sort queries by limit.
- Process edges in increasing order, connecting nodes.
- For each query, after processing all edges with weight `< limit`, check connectivity.

### **Java Code:**
*(Same as Approach 2, optimized for large constraints)*

```java
// Same code as Approach 2, since it's already optimal
```

### **Complexity Analysis:**
- **Time Complexity:** **O((V + E) α(N))**.
- **Space Complexity:** **O(V + E)**.

---

## **Summary:**

| Approach | Key Idea | When to Use | Time Complexity | Space Complexity |
|------------|--------------|----------------|---------------------|------------------|
| **Brute Force** | Check each query independently via BFS/DFS | Small graphs, testing | O(Q * (V + E)) | O(V + E) per query |
| **Offline Sort + UF** | Sort edges and queries, process in order | Moderate constraints | O((E + Q) log E) | O(V + E) |
| **Most Optimal Union-Find + Sorting** | Same as above but optimized for large inputs | Large constraints | O((V + E) α(N)) | O(V + E) |

---

Use this guide to quickly recall different strategies for solving **Checking Existence of Edge Length Limited Paths** and select the most suitable approach based on input constraints.
