# Find Critical and Pseudo-Critical Edges in MST

---

## **Overview**

Given a weighted undirected graph, the goal is to determine which edges are **critical** and which are **pseudo-critical** in the context of Minimum Spanning Trees (MST):

- **Critical Edges:** Edges that, when removed, **increase** the MST's total weight or make it impossible to form an MST. Removing a critical edge means it must be in **every** MST.
- **Pseudo-Critical Edges:** Edges that can be part of some MST but are **not** in all MSTs. When forcibly included, the MST's total weight remains the same.

This problem involves **testing the importance** of edges via MST computations.

---

## **Approach 1: Brute Force / Naive Approach**

### **Core Idea**
- For each edge:
  - Remove the edge and compute the MST. If the MST's weight increases or MST not possible, it's **critical**.
  - Force include the edge first, then compute MST for remaining edges. If the total weight equals the original MST, it's **pseudo-critical**.

### **Algorithm**
1. **Compute the original MST** with all edges (using Kruskal’s or Prim’s).
2. For each edge:
   - **Test removal:**
     - Remove the edge from the list.
     - Recompute MST.
     - If MST is invalid or weight > original, mark as **critical**.
   - **Test inclusion:**
     - Force include this edge first.
     - Compute MST with this edge included (skip it in normal MST process).
     - If total weight == original MST, mark as **pseudo-critical**.

### **Java Code**

```java
import java.util.*;

class Edge {
    int u, v, weight, index;
    public Edge(int u, int v, int weight, int index) {
        this.u = u;
        this.v = v;
        this.weight = weight;
        this.index = index;
    }
}

public class CriticalPseudoCriticalEdges {
    public List<List<Integer>> findCriticalAndPseudoCriticalEdges(int n, int[][] edgesInput) {
        int m = edgesInput.length;
        List<Edge> edges = new ArrayList<>();
        for (int i = 0; i < m; i++) {
            edges.add(new Edge(edgesInput[i][0], edgesInput[i][1], edgesInput[i][2], i));
        }

        // Compute the original MST weight
        int originalMST = buildMST(n, edges, null, false);

        List<Integer> critical = new ArrayList<>();
        List<Integer> pseudoCritical = new ArrayList<>();

        for (Edge e : edges) {
            // Test removal
            int weightWithoutEdge = buildMST(n, edges, e, false);
            if (weightWithoutEdge > originalMST || weightWithoutEdge == -1) {
                critical.add(e.index);
            } else {
                // Test inclusion
                int weightWithEdge = buildMST(n, edges, e, true);
                if (weightWithEdge == originalMST) {
                    pseudoCritical.add(e.index);
                }
            }
        }

        return Arrays.asList(critical, pseudoCritical);
    }

    private int buildMST(int n, List<Edge> edges, Edge forcedEdge, boolean includeEdge) {
        UnionFind uf = new UnionFind(n);
        int totalWeight = 0;

        // If forced include, add it first
        if (forcedEdge != null && includeEdge) {
            if (uf.union(forcedEdge.u, forcedEdge.v))
                totalWeight += forcedEdge.weight;
        }

        // Process edges
        for (Edge e : edges) {
            if (e == forcedEdge && includeEdge) continue; // Already included
            if (uf.union(e.u, e.v))
                totalWeight += e.weight;
        }

        // Check if all vertices are connected
        int root = uf.find(0);
        for (int i = 1; i < n; i++) {
            if (uf.find(i) != root) return -1; // Not connected
        }
        return totalWeight;
    }

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
        public boolean union(int x, int y) {
            int rx = find(x), ry = find(y);
            if (rx == ry) return false;
            parent[ry] = rx;
            return true;
        }
    }
}
```

---

## **Complexity Analysis**
- **Time Complexity:**  
  - For each of the `m` edges, we run MST computation: O(m * E log E).  
  - Since `E` can be up to O(n²), overall is **O(m * n² log n)** in worst case.

- **Space Complexity:**  
  - Storage for edges and Union-Find: O(n + m).

---

## **Approach 2: Optimized Approach Using MST and Edge Testing**

**Note:** The brute-force approach is often sufficient for moderate constraints, but large graphs require more optimized methods, like Kruskal's with early pruning.

---

## **Dry Run Example**

Suppose input:

```plaintext
n=5
edges = [
  [0, 1, 1],
  [1, 2, 1],
  [2, 3, 2],
  [0, 3, 2],
  [0, 4, 3],
  [3, 4, 3]
]
```

- **Compute original MST:**
  - Edges sorted by weight: (0-1,1), (1-2,1), (2-3,2), (0-3,2), (0-4,3), (3-4,3)
  - MST edges: (0-1), (1-2), (2-3), (0-4) → total weight = 1+1+2+3=7

- **Test edge (0-1):**
  - Remove (0-1): MST weight becomes 1+2+3=6? No, need to test.
  - Recompute MST without this edge, find total weight, if >7 or impossible, it's critical.

- **Test edge (2-3):**
  - Remove (2-3), see if MST increases.

---

## **Summary Table**

| Approach | Data Structures Used | Time Complexity | Space Complexity | Best Use Case |
|------------|----------------------|-------------------|------------------|--------------|
| Naive      | MST computation per edge | O(m * n² log n) | O(n + m) | Small to medium graphs |
| Optimized  | MST + edge testing | Similar but more pruning | O(n + m) | Large graphs, large constraints |

---

## **Final Tips for Interview & Revision**
- Understand the importance of testing edges by including and excluding them to classify critical/pseudo-critical.
- Use Kruskal's algorithm with Union-Find for efficient MST computation.
- Remember that critical edges are in **every** MST, while pseudo-critical edges are in **some** MSTs.
- Practice implementing this approach with different graph inputs.

---

**End of Revision Note**
