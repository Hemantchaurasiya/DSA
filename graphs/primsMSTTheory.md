# Prim’s Algorithm for Minimum Spanning Tree (MST)

---

## **Overview**

Prim’s algorithm is a greedy algorithm used to find the **Minimum Spanning Tree (MST)** of a connected, weighted graph. The MST connects all vertices with the minimum total edge weight, with no cycles.

---

## **Approach 1: Brute Force (Naive) Approach**

### **Core Idea**
- For each step, select the smallest edge that connects a vertex in the MST to a vertex outside the MST.
- Repeat until all vertices are included.

### **Algorithm**
1. Initialize an array `key[]` to store minimum edge weights to include each vertex in MST, with `key[0] = 0` and others as infinity.
2. Maintain a boolean array `mstSet[]` to track vertices already included in MST.
3. For each iteration:
   - Pick the vertex `u` with minimum `key[]` value not yet in `mstSet[]`.
   - Include `u` in MST (`mstSet[u] = true`).
   - Update the `key[]` for adjacent vertices of `u`, if the edge weight is smaller than their current `key[]`.

### **Java Code**

```java
int primMST(int[][] graph) {
    int V = graph.length;
    int[] key = new int[V];
    boolean[] mstSet = new boolean[V];
    int[] parent = new int[V];

    // Initialize all keys as INF
    Arrays.fill(key, Integer.MAX_VALUE);
    key[0] = 0; // Start from vertex 0
    parent[0] = -1;

    for (int count = 0; count < V - 1; count++) {
        int u = minKey(key, mstSet);
        mstSet[u] = true;

        for (int v = 0; v < V; v++) {
            if (graph[u][v] != 0 && !mstSet[v] && graph[u][v] < key[v]) {
                parent[v] = u;
                key[v] = graph[u][v];
            }
        }
    }

    // Calculate total weight
    int mstWeight = 0;
    for (int i = 1; i < V; i++) {
        mstWeight += graph[parent[i]][i];
    }
    return mstWeight;
}

int minKey(int[] key, boolean[] mstSet) {
    int min = Integer.MAX_VALUE, minIndex = -1;
    for (int v = 0; v < key.length; v++) {
        if (!mstSet[v] && key[v] < min) {
            min = key[v];
            minIndex = v;
        }
    }
    return minIndex;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - Selecting minimum vertex: O(V) per iteration → O(V²) total.  
  - Updating adjacent vertices: O(V) per iteration.  
  - **Total:** **O(V²)**

- **Space Complexity:**  
  - `key[]`, `mstSet[]`, and `parent[]`: O(V)

---

## **Approach 2: Using Min-Heap / Priority Queue (Better Solution)**

### **Core Idea**
- Use a priority queue (min-heap) to efficiently extract the minimum edge.
- Reduce the selection time from O(V) to O(log V).

### **Algorithm**
1. Initialize `key[]`, `parent[]`, and a priority queue containing all vertices with their current key values.
2. While the priority queue is not empty:
   - Extract the vertex `u` with the smallest `key[]` value.
   - Mark `u` as included in MST.
   - For each adjacent vertex `v`:
     - If `v` is not yet in MST and the weight `graph[u][v]` is less than `key[v]`, update `key[v]` and `parent[v]`, and update `v` in the priority queue.

### **Java Code**

```java
import java.util.*;

int primMST(int[][] graph) {
    int V = graph.length;
    int[] key = new int[V];
    int[] parent = new int[V];
    boolean[] inMST = new boolean[V];

    PriorityQueue<Vertex> pq = new PriorityQueue<>(Comparator.comparingInt(v -> v.weight));
    for (int i = 0; i < V; i++) {
        key[i] = Integer.MAX_VALUE;
        parent[i] = -1;
    }
    key[0] = 0;
    pq.offer(new Vertex(0, 0));

    while (!pq.isEmpty()) {
        Vertex u = pq.poll();
        int uIdx = u.id;
        if (inMST[uIdx]) continue;
        inMST[uIdx] = true;

        for (int v = 0; v < V; v++) {
            if (graph[uIdx][v] != 0 && !inMST[v] && graph[uIdx][v] < key[v]) {
                key[v] = graph[uIdx][v];
                parent[v] = uIdx;
                pq.offer(new Vertex(v, key[v]));
            }
        }
    }

    // Calculate total weight
    int totalWeight = 0;
    for (int i = 1; i < V; i++) {
        totalWeight += graph[parent[i]][i];
    }
    return totalWeight;
}

class Vertex {
    int id, weight;
    Vertex(int id, int weight) {
        this.id = id;
        this.weight = weight;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - Building and updating the priority queue: O(E log V), where E is the number of edges.  
  - Since the graph can be dense, in worst case, O(V²) edges.  
  - **Total:** **O(E log V)**

- **Space Complexity:**  
  - `key[]`, `parent[]`, `inMST[]`, and priority queue: O(V)

---

## **Approach 3: Using Adjacency List + Min-Heap (Most Optimal for Sparse Graphs)**

### **Core Idea**
- Use an adjacency list representation to efficiently handle sparse graphs.
- Similar to the previous approach but optimized with adjacency list.

### **Algorithm**
- Same as Approach 2, but iterate only over adjacency lists of each vertex, reducing unnecessary checks.

### **Dry Run Example**

Suppose the graph is represented as:

```plaintext
Vertices: 0, 1, 2, 3
Edges:
0 - 1 (weight 2)
0 - 3 (weight 6)
1 - 2 (weight 3)
1 - 3 (weight 8)
2 - 3 (weight 5)
```

**Step-by-step:**

| Step | Selected Vertex | Edges considered | Updated `key[]` | MST Set | Total MST Weight |
|-------|-------------------|--------------------|-----------------|---------|------------------|
| 1     | 0                 | neighbors: 1(2), 3(6) | key[1]=2, key[3]=6 | {0} | 0 |
| 2     | 1                 | neighbors: 2(3), 3(8) | key[2]=3, key[3]=min(6,8)=6 | {0,1} | 2 |
| 3     | 2                 | neighbor: 3(5) | key[3]=min(6,5)=5 | {0,1,2} | 0+2+3+5=10 |

Total MST weight = 10.

---

## **Summary Table**

| Approach | Data Structures Used | Time Complexity | Space Complexity | Best Use Case |
|------------|----------------------|-------------------|------------------|--------------|
| Naive      | Matrix + Arrays     | O(V²)            | O(V)             | Dense graphs |
| Min-Heap   | Priority Queue + Array | O(E log V)     | O(V)             | Sparse graphs |
| Adjacency List + Min-Heap | Adjacency List + PQ | O(E log V) | O(V + E) | Large sparse graphs |

---

## **Final Tips for Interview & Revision**
- Focus on understanding the greedy nature: always pick the smallest edge connecting the MST to a new vertex.
- Know the difference in data structures’ impact on complexity.
- Practice implementing the approach with adjacency lists for large graphs.
- Remember that Prim’s algorithm works only for connected graphs.

---

**End of Revision Note**
