## ✅ 1. **Edge List**

### 🧠 Core Idea:

Store the graph as a list of **edges (u, v)**. Useful for algorithms like Kruskal's.

### 🧠 Core Idea:

- You **don’t have direct neighbors** like in adj. list/matrix.
- You must **scan the entire edge list** to find neighbors.

---

### ⚙️ Structure:

Just store an array of edge pairs or triplets (u, v, weight).

---

### 💻 Java Code:

```java
class Edge {
    int src, dest, weight;
    Edge(int s, int d, int w) {
        src = s;
        dest = d;
        weight = w;
    }
}

List<Edge> edgeList = new ArrayList<>();
edgeList.add(new Edge(0, 1, 10));
edgeList.add(new Edge(1, 2, 20));
```

---

### ⏱️ Time and Space Complexity:

- **Space**: O(E)
- **Add edge**: O(1)
- **Check edge exists**: O(E)
- **List neighbors**: O(E)

---

### 🧪 Dry Run Example:

Edges: (0,1,10), (1,2,20)

```
edgeList: [ (0,1,10), (1,2,20) ]
```

---

### 🖼️ Diagram:

```
0 --10--> 1 --20--> 2
```

---

### 💻 Full Example Java Code:

```java
import java.util.*;

class Edge {
    int src, dest;
    Edge(int u, int v) {
        this.src = u;
        this.dest = v;
    }
}

public class EdgeList {
    public static void main(String[] args) {
        List<Edge> edgeList = new ArrayList<>();

        edgeList.add(new Edge(0, 1));
        edgeList.add(new Edge(0, 4));
        edgeList.add(new Edge(1, 2));
        edgeList.add(new Edge(1, 3));
        edgeList.add(new Edge(1, 4));
        edgeList.add(new Edge(2, 3));
        edgeList.add(new Edge(3, 4));

        printGraph(edgeList);
    }

    static void printGraph(List<Edge> edgeList) {
        for (Edge edge : edgeList) {
            System.out.println(edge.src + " - " + edge.dest);
        }
    }
}
```

### 🔍 Dry Run:

```
Edges:
0 - 1
0 - 4
1 - 2
1 - 3
1 - 4
2 - 3
3 - 4
```

## 📊 Summary Comparison Table

| Representation | Space | Best For | Lookup Edge | List Neighbors |
| --- | --- | --- | --- | --- |
| Adjacency Matrix | O(V²) | Dense Graphs | O(1) | O(V) |
| Adjacency List | O(V + E) | Sparse Graphs | O(degree) | O(degree) |
| Edge List | O(E) | Edge-centric Algos | O(E) | O(E) |

## ✅ 2. **Weighted Edge List**

### 🧠 Core Idea:

- Store each edge as a **triplet (u, v, weight)**.

---

### 💻 Java Code:

```java
java
CopyEdit
class Edge {
    int src, dest, weight;
    Edge(int s, int d, int w) {
        src = s;
        dest = d;
        weight = w;
    }
}

List<Edge> edgeList = new ArrayList<>();

// Add edge
void addEdge(int u, int v, int weight) {
    edgeList.add(new Edge(u, v, weight));
    edgeList.add(new Edge(v, u, weight)); // omit for directed
}

```

---

### 🧪 Dry Run Example:

Edges: (0,1,10), (0,2,5), (1,2,2)

```
ini
CopyEdit
edgeList = [
  (0,1,10), (1,0,10),
  (0,2,5),  (2,0,5),
  (1,2,2),  (2,1,2)
]

```

---

### 🖼️ Console Diagram:

```
lua
CopyEdit
0 --10-- 1
 \      /
  5    2
   \  /
     2
0 --5-- 2 --2-- 1

```

---

### ⏱️ Time and Space:

- **Space**: O(E)
- **Edge lookup**: O(E)
- **Good for**: Kruskal’s algorithm, minimum spanning tree problems

---

## 🔚 Summary Table

| Representation | Stores | Space | Best For |
| --- | --- | --- | --- |
| Adjacency Matrix | 2D matrix | O(V²) | Dense Graphs |
| Adjacency List | List of lists | O(V + E) | Sparse Graphs |
| Edge List | List of edges | O(E) | Edge-centric Algos |

## ✅ 3. **Directed Edge List**

### 🧠 Core Idea:

Each edge is stored as a **triplet (u, v, weight)** indicating a one-way connection from `u` to `v`.

---

### 💻 Java Code:

```java
java
CopyEdit
class Edge {
    int src, dest, weight;
    Edge(int s, int d, int w) {
        src = s;
        dest = d;
        weight = w;
    }
}

List<Edge> edgeList = new ArrayList<>();

void addDirectedEdge(int u, int v, int weight) {
    edgeList.add(new Edge(u, v, weight)); // no reverse edge
}

```

---

### 🧪 Dry Run:

Edges: (0→1,10), (0→2,5), (1→2,2)

```
makefile
CopyEdit
edgeList:
[
  (0,1,10),
  (0,2,5),
  (1,2,2)
]

```

---

### 🖼️ Diagram:

```
lua
CopyEdit
0 --10--> 1
 \        |
  \--5--> 2
       ↑
       |
       2

```

---

## 🔚 Summary – Directed vs Undirected

| Representation | Undirected Edge | Directed Edge |
| --- | --- | --- |
| Matrix | `u→v & v→u` | `u→v only` |
| List | Add to both lists | Add to `u` only |
| Edge List | Add `(u,v)` & `(v,u)` | Add only `(u,v)` |
