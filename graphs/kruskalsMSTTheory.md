# Kruskal’s Algorithm for Minimum Spanning Tree (MST)

---

## **Overview**

Kruskal’s algorithm is a greedy approach to find the **Minimum Spanning Tree (MST)** of a connected, weighted graph. It works by sorting edges and selecting the smallest edges that do not form a cycle, ensuring the total weight is minimized.

---

## **Approach 1: Brute Force / Naive Approach**

### **Core Idea**
- List all edges.
- Sort edges in increasing order of weight.
- Iteratively pick edges from the sorted list.
- For each edge, check if adding it creates a cycle.
- If it doesn't, include it in MST.

### **Algorithm**
1. Extract all edges from the graph.
2. Sort the edges based on their weight.
3. Initialize the MST set as empty.
4. For each edge in sorted order:
   - Use cycle detection (e.g., DFS or Union-Find) to check if adding this edge forms a cycle.
   - If no cycle, include this edge in the MST.
5. Continue until MST contains V-1 edges.

### **Java Code**

```java
import java.util.*;

class Edge implements Comparable<Edge> {
    int src, dest, weight;
    public Edge(int s, int d, int w) {
        this.src = s;
        this.dest = d;
        this.weight = w;
    }
    public int compareTo(Edge other) {
        return this.weight - other.weight;
    }
}

int kruskalMST(int V, List<Edge> edges) {
    Collections.sort(edges);
    int[] parent = new int[V];
    for (int i = 0; i < V; i++) parent[i] = i; // Disjoint set initialization

    int mstWeight = 0;
    int edgesUsed = 0;

    for (Edge edge : edges) {
        int root1 = find(parent, edge.src);
        int root2 = find(parent, edge.dest);
        if (root1 != root2) {
            // No cycle, include edge
            mstWeight += edge.weight;
            union(parent, root1, root2);
            edgesUsed++;
            if (edgesUsed == V - 1) break; // MST complete
        }
    }
    return mstWeight;
}

int find(int[] parent, int i) {
    if (parent[i] != i) parent[i] = find(parent, parent[i]);
    return parent[i];
}

void union(int[] parent, int x, int y) {
    parent[y] = x;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - Sorting edges: O(E log E)  
  - Union-Find operations: Almost O(1) amortized with path compression  
  - **Total:** **O(E log E)**

- **Space Complexity:**  
  - Store edges and parent array: O(E + V)

---

## **Approach 2: Using Disjoint Set Union (Union-Find) with Optimizations**

### **Core Idea**
- Use efficient Union-Find data structure with path compression and union by rank to quickly detect cycles.
- Sort edges by weight.
- Iteratively add edges that connect different sets.

### **Algorithm**
1. Sort all edges based on weight.
2. Initialize Union-Find structure.
3. Iterate over sorted edges:
   - For each edge, check if `src` and `dest` belong to different sets.
   - If yes, union the sets and add the edge to MST.
4. Continue until V-1 edges are included.

### **Java Code**

```java
class UnionFind {
    int[] parent, rank;

    public UnionFind(int size) {
        parent = new int[size];
        rank = new int[size];
        for (int i = 0; i < size; i++) {
            parent[i] = i;
            rank[i] = 0;
        }
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

int kruskalMST(int V, List<Edge> edges) {
    Collections.sort(edges);
    UnionFind uf = new UnionFind(V);
    int mstWeight = 0;
    int edgesCount = 0;

    for (Edge edge : edges) {
        if (uf.union(edge.src, edge.dest)) {
            mstWeight += edge.weight;
            edgesCount++;
            if (edgesCount == V - 1) break;
        }
    }
    return mstWeight;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - Sorting edges: O(E log E)  
  - Union-Find operations: O(α(V)) (inverse Ackermann function, practically constant)  
  - **Total:** **O(E log E)**

- **Space Complexity:**  
  - Union-Find arrays: O(V)  
  - Edges list: O(E)

---

## **Approach 3: Optimized Kruskal for Sparse Graphs**

### **Core Idea**
- Use adjacency list representation for the graph.
- Collect edges efficiently.
- Use Kruskal's algorithm with Union-Find.

### **Algorithm**
1. Convert graph into an edge list using adjacency list.
2. Sort edges.
3. Use Union-Find to pick edges greedily.
4. Terminate when MST has V-1 edges.

### **Dry Run Example**

Suppose the graph:

```plaintext
Vertices: 0, 1, 2, 3
Edges:
0 - 1 (2)
0 - 3 (6)
1 - 2 (3)
1 - 3 (8)
2 - 3 (5)
```

Sorted edges:

| Edge | Weight | Action                         | MST Edges | Total Weight |
|--------|----------|--------------------------------|--------------|----------------|
| 0-1  | 2        | Add (no cycle)                  | (0-1)      | 2              |
| 2-3  | 3        | Add (no cycle)                  | (0-1), (1-2) | 5            |
| 2-3  | 5        | Add (no cycle)                  | (0-1), (1-2), (2-3) | 10    |
| 0-3  | 6        | Skip (cycle formed)             |              |                |
| 1-3  | 8        | Skip (cycle formed)             |              |                |

---

## **Summary Table**

| Approach | Data Structures Used | Time Complexity | Space Complexity | Best Use Case |
|------------|----------------------|-------------------|------------------|--------------|
| Naive      | Edge list + cycle detection | O(E log E) | O(E) | Small graphs |
| Union-Find | Edge list + DSU | O(E log E) | O(V) | Large, sparse graphs |
| Optimized | Adjacency list + DSU | O(E log E) | O(V + E) | Very large graphs |

---

## **Final Tips for Interview & Revision**
- Remember Kruskal's algorithm sorts edges and uses a DSU to quickly detect cycles.
- Always optimize Union-Find with path compression and union by rank.
- Kruskal's is preferred when edges are easily sortable and the graph is sparse.
- Practice implementing Kruskal with adjacency list conversions for large graphs.

---

**End of Revision Note**
