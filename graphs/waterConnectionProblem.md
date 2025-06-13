# Water Connection Problem

---

## **Overview**

The **Water Connection Problem** is a classic graph problem where we need to connect a set of houses (nodes) with water pipelines (edges) such that all houses have water, and the total cost is minimized. The problem is analogous to finding a **Minimum Spanning Tree (MST)** in a weighted graph.

---

## **Approach 1: Brute Force / Naive Method**

### **Core Idea**
- Connect each house directly to the main water source (or city) with the minimal cost.
- For each house, choose the least expensive connection to the source or other houses already connected.

### **Algorithm**
1. Given the cost matrix, for each house, find the minimum cost to connect to the source or any connected house.
2. Sum these minimum costs to get the total minimum cost.
3. This approach doesn't guarantee an optimal global solution if multiple intermediate connections exist; it's a naive heuristic.

**Note:** This approach is generally not recommended for optimal solutions but provides intuition.

---

## **Approach 2: Prim’s Algorithm (Greedy MST approach)**

### **Core Idea**
- Start from the main water source (or any house).
- Repeatedly connect the closest house not yet connected, ensuring minimal increment in total cost.
- This builds the MST greedily, ensuring minimal total connection cost.

### **Algorithm**
1. Initialize a `key[]` array with large values, except for the starting node (cost 0).
2. Use a `minHeap` or priority queue to pick the house with the smallest connection cost.
3. Maintain a `mstSet[]` to track connected houses.
4. For each selected house:
   - Mark it as connected.
   - Update the `key[]` for its neighbors if a cheaper connection is found.
5. Continue until all houses are connected.

### **Java Code**

```java
import java.util.*;

public class WaterConnection {
    public static int minCostToConnectHouses(int[][] cost) {
        int n = cost.length;
        int[] key = new int[n];
        boolean[] inMST = new boolean[n];

        Arrays.fill(key, Integer.MAX_VALUE);
        key[0] = 0;  // Starting from house 0 (city/main source)

        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[1]));
        pq.offer(new int[]{0, 0}); // {node, cost}

        int totalCost = 0;

        while (!pq.isEmpty()) {
            int[] current = pq.poll();
            int u = current[0];

            if (inMST[u]) continue;
            inMST[u] = true;
            totalCost += current[1];

            for (int v = 0; v < n; v++) {
                if (!inMST[v] && cost[u][v] < key[v]) {
                    key[v] = cost[u][v];
                    pq.offer(new int[]{v, key[v]});
                }
            }
        }
        return totalCost;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - For each of the `n` nodes, extracting from priority queue: O(log n)  
  - Updating neighbors: O(n) for each node  
  - Total: **O(n²)** (since each edge is checked at most once in dense graphs)

- **Space Complexity:**  
  - `key[]`, `inMST[]`, and priority queue: O(n)

---

## **Approach 3: Kruskal’s Algorithm (Minimum Spanning Tree with Union-Find)**

### **Core Idea**
- Sort all potential connections (edges) based on cost.
- Connect houses in increasing order of cost, avoiding cycles.
- This ensures the total connection cost is minimized.

### **Algorithm**
1. Convert the cost matrix into an edge list.
2. Sort edges by their cost.
3. Use a Disjoint Set Union (Union-Find) data structure to manage connected components.
4. Iterate over sorted edges:
   - For each edge, if connecting the two houses doesn't form a cycle (their roots are different), union them and add the cost.
5. Continue until all houses are connected (or have `n-1` edges).

### **Java Code**

```java
import java.util.*;

class Edge implements Comparable<Edge> {
    int u, v, cost;
    public Edge(int u, int v, int cost) {
        this.u = u;
        this.v = v;
        this.cost = cost;
    }
    public int compareTo(Edge other) {
        return this.cost - other.cost;
    }
}

class UnionFind {
    int[] parent, rank;
    public UnionFind(int n) {
        parent = new int[n];
        rank = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
    }
    public int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }
    public boolean union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX == rootY) return false;
        if (rank[rootX] < rank[rootY]) {
            parent[rootX] = rootY;
        } else if (rank[rootY] < rank[rootX]) {
            parent[rootY] = rootX;
        } else {
            parent[rootY] = rootX;
            rank[rootX]++;
        }
        return true;
    }
}

public class WaterConnection {
    public static int minCostKruskal(int[][] cost) {
        int n = cost.length;
        List<Edge> edges = new ArrayList<>();
        // Convert matrix to edge list
        for (int i = 0; i < n; i++) {
            for (int j = i+1; j < n; j++) {
                if (cost[i][j] > 0) {
                    edges.add(new Edge(i, j, cost[i][j]));
                }
            }
        }
        Collections.sort(edges);

        UnionFind uf = new UnionFind(n);
        int totalCost = 0;
        int edgesUsed = 0;

        for (Edge e : edges) {
            if (uf.union(e.u, e.v)) {
                totalCost += e.cost;
                edgesUsed++;
                if (edgesUsed == n - 1) break;
            }
        }
        return totalCost;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - Building edge list: O(n²)  
  - Sorting edges: O(E log E), with E ≈ n² → O(n² log n²) = O(n² log n)  
  - Union-Find operations: O(α(n)) (almost constant per operation)  
  - **Total:** **O(n² log n)**

- **Space Complexity:**  
  - Edge list: O(n²)  
  - Union-Find: O(n)

---

## **Dry Run Example**

Suppose the cost matrix:

```plaintext
[
  [0, 4, 0, 0],
  [4, 0, 8, 0],
  [0, 8, 0, 7],
  [0, 0, 7, 0]
]
```

- **Prim's Algorithm:**  
  - Start at node 0, connect to node 1 (cost 4).  
  - Next, connect node 3 (cost 7), then node 2 (cost 8).  
  - Total cost: 4 + 7 + 8 = 19.

- **Kruskal's Algorithm:**  
  - Sorted edges: (0-1,4), (2-3,7), (1-2,8)  
  - Connect 0-1, total=4  
  - Connect 2-3, total=11  
  - Connect 1-2, total=19  
  - All nodes connected, total cost=19.

---

## **Summary Table**

| Approach | Data Structures Used | Time Complexity | Space Complexity | Best Use Case |
|------------|----------------------|-------------------|------------------|--------------|
| Naive      | Matrices, heuristic | O(n²) | O(1) or O(n) | Small graphs, quick approximation |
| Prim’s     | Priority Queue + Array | O(n²) | O(n) | Dense graphs, when adjacency matrix is available |
| Kruskal’s  | Edge list + DSU | O(n² log n) | O(n²) | Sparse or large graphs, when edges are easily accessible |

---

## **Final Tips for Interview & Revision**
- Understand the core MST algorithms: Prim’s and Kruskal’s.
- Prim’s is better for dense graphs; Kruskal’s works well for sparse graphs.
- Use Union-Find with path compression for Kruskal’s.
- Practice converting cost matrices into edge lists for Kruskal’s.
- Always analyze input size to choose the optimal approach.

---

**End of Revision Note**
