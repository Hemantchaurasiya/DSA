## 🔷 HashMap of HashSet (Dynamic/Weighted Graphs)

### ✅ Core Idea:

- Great for **dynamic** graphs (nodes/edges added at runtime).
- HashMap allows fast lookup, HashSet avoids duplicates.
- Works for **non-contiguous or large node IDs** (like city names, strings, etc.)

---

### 🔁 Algorithm/Structure:

- `Map<Integer, Set<Integer>> graph = new HashMap<>();`
- Use `putIfAbsent` and `add` to update neighbors.

---

### 💻 Java Code:

```java
import java.util.*;

public class HashMapGraph {
    public static void main(String[] args) {
        Map<Integer, Set<Integer>> graph = new HashMap<>();

        addEdge(graph, 0, 1);
        addEdge(graph, 0, 4);
        addEdge(graph, 1, 2);
        addEdge(graph, 1, 3);
        addEdge(graph, 1, 4);
        addEdge(graph, 2, 3);
        addEdge(graph, 3, 4);

        printGraph(graph);
    }

    static void addEdge(Map<Integer, Set<Integer>> graph, int u, int v) {
        graph.putIfAbsent(u, new HashSet<>());
        graph.putIfAbsent(v, new HashSet<>());
        graph.get(u).add(v);
        graph.get(v).add(u); // undirected
    }

    static void printGraph(Map<Integer, Set<Integer>> graph) {
        for (int node : graph.keySet()) {
            System.out.print(node + ": ");
            for (int neighbor : graph.get(node)) {
                System.out.print(neighbor + " ");
            }
            System.out.println();
        }
    }
}

```

---

### ⏱️ Time and Space Complexity:

| Operation | Time (avg) | Space |
| --- | --- | --- |
| Add/Delete Edge | O(1) | O(V + E) |
| Check Edge Exists | O(1) |  |
| Iterate Neighbors | O(degree) |  |

---

### 🔍 Dry Run:

```
0: 1 4
1: 0 2 3 4
2: 1 3
3: 1 2 4
4: 0 1 3

```

---

### 📊 Console-Based Diagram:

```
yaml
CopyEdit
HashMap Structure:
{
  0: [1, 4],
  1: [0, 2, 3, 4],
  ...
}

```

---

## 🧠 Summary Table:

| Representation | Edge Check | Add/Delete Edge | Space | Best For |
| --- | --- | --- | --- | --- |
| Adjacency Matrix | O(1) | O(1) | O(V²) | Dense graphs |
| Adjacency List | O(degree) | O(1) | O(V + E) | Sparse graphs |
| Edge List | O(E) | O(1) | O(E) | Edge algorithms (Kruskal) |
| HashMap of HashSet | O(1) | O(1) | O(V + E) | Dynamic, large ID graphs |
