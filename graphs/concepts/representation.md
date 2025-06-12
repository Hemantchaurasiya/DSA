# 📘 Graph Representation Techniques

Every graph (whether directed/undirected, weighted/unweighted) needs to be represented in memory. The three primary techniques are:

## 🔷 1. Adjacency Matrix

### ✅ Core Idea:

- A 2D matrix where `matrix[i][j] = 1` (or weight) if there is an edge from node `i` to node `j`.
- Best for **dense graphs** (many edges) or when **constant-time edge checks** are needed.

### ⚙️ Algorithm:

- Create a `V x V` matrix initialized to `0` . Here, (`n = number of vertices`)
- For each edge `(u, v)`, set `graph[u][v] = 1` (or weight).
- If undirected, also set `graph[v][u] = 1`.

### 💻 Java Code:

```java
int V = 4; // number of vertices
int[][] graph = new int[V][V]; // adjacency matrix

// Add edge from u to v
void addEdge(int u, int v) {
    graph[u][v] = 1;
    graph[v][u] = 1; // for undirected graph
}
```

### 💻 Full Example Java Code:

```java
public class AdjacencyMatrix {
    public static void main(String[] args) {
        int V = 5; // number of vertices
        int[][] graph = new int[V][V];

        // Add edges
        addEdge(graph, 0, 1);
        addEdge(graph, 0, 4);
        addEdge(graph, 1, 2);
        addEdge(graph, 1, 3);
        addEdge(graph, 1, 4);
        addEdge(graph, 2, 3);
        addEdge(graph, 3, 4);

        printGraph(graph);
    }

    static void addEdge(int[][] graph, int u, int v) {
        graph[u][v] = 1;
        graph[v][u] = 1; // for undirected graph
    }

    static void printGraph(int[][] graph) {
        for (int i = 0; i < graph.length; i++) {
            System.out.print(i + ": ");
            for (int j = 0; j < graph[i].length; j++) {
                System.out.print(graph[i][j] + " ");
            }
            System.out.println();
        }
    }
}
```

### ⏱️ Time and Space Complexity:

- **Space**: O(V²)
- **Check edge exists**: O(1)
- **Add edge**: O(1)
- **List neighbors of vertex**: O(V)

### 🧪 Dry Run Example:

Vertices: 0, 1, 2

Edges: (0,1), (1,2)

```
Before:
[ [0,0,0],
  [0,0,0],
  [0,0,0] ]

After:
[ [0,1,0],
  [1,0,1],
  [0,1,0] ]
```

---

### 🖼️ Diagram:

```
0 --- 1 --- 
```

## 🔷 2. **Adjacency List**

### 🧠 Core Idea:

Each vertex maintains a **list of its adjacent vertices**.

- For `V` vertices, use an array/list of `V` linked lists or `ArrayLists`.
- Memory efficient for sparse graphs.

---

### ⚙️ Algorithm:

- Create a list `List<List<Integer>> adj`.
- For each edge `(u, v)`, add `v` to `adj[u]`.
- If undirected, also add `u` to `adj[v]`.

---

### 💻 Java Code:

```java
int V = 4;
List<List<Integer>> adj = new ArrayList<>();

// Initialize
for (int i = 0; i < V; i++) {
    adj.add(new ArrayList<>());
}

// Add edge
void addEdge(int u, int v) {
    adj.get(u).add(v);
    adj.get(v).add(u); // for undirected
}
```

---

### ⏱️ Time and Space Complexity:

- **Space**: O(V + E)
- **Check edge exists**: O(degree of vertex)
- **Add edge**: O(1)
- **List neighbors**: O(degree of vertex)

---

### 🧪 Dry Run Example:

Vertices: 0, 1, 2

Edges: (0,1), (1,2)

```
adj = [
  [1],      // vertex 0
  [0,2],    // vertex 1
  [1]       // vertex 2
]
```

---

### 🖼️ Diagram:

```
0 --- 1 --- 2
```

### 💻 Full Example Java Code:

```java
java
CopyEdit
import java.util.*;

public class AdjacencyList {
    public static void main(String[] args) {
        int V = 5;
        List<List<Integer>> adjList = new ArrayList<>();

        for (int i = 0; i < V; i++) {
            adjList.add(new ArrayList<>());
        }

        addEdge(adjList, 0, 1);
        addEdge(adjList, 0, 4);
        addEdge(adjList, 1, 2);
        addEdge(adjList, 1, 3);
        addEdge(adjList, 1, 4);
        addEdge(adjList, 2, 3);
        addEdge(adjList, 3, 4);

        printGraph(adjList);
    }

    static void addEdge(List<List<Integer>> adjList, int u, int v) {
        adjList.get(u).add(v);
        adjList.get(v).add(u);
    }

    static void printGraph(List<List<Integer>> adjList) {
        for (int i = 0; i < adjList.size(); i++) {
            System.out.print(i + ": ");
            for (int neighbor : adjList.get(i)) {
                System.out.print(neighbor + " ");
            }
            System.out.println();
        }
    }
}
```

### 🔍 Dry Run:

```
makefile
CopyEdit
0: 1 4
1: 0 2 3 4
2: 1 3
3: 1 2 4
4: 0 1 3

```

---

### 📊 Console-Based Diagram:

```
    0 --- 1 --- 2
     \   / \     \
      4     3 ----
```

## 🔷 3. **Edge List**

### 🧠 Core Idea:

Store the graph as a list of **edges (u, v)**. Useful for algorithms like Kruskal's.

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

## 🔷 4. HashMap of HashSet (Dynamic/Weighted Graphs)

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
java
CopyEdit
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
makefile
CopyEdit
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

# 🔶 Weighted Graph Representations

We'll assume the graph is **undirected and weighted** for all implementations. (I'll note how to adapt for **directed** too.)

---

## ✅ 1. **Weighted Adjacency Matrix**

### 🧠 Core Idea:

- Instead of `1`, store the **weight of the edge** between two vertices.
- If no edge exists, use `0` or `∞` (`Integer.MAX_VALUE`) to denote no connection.

---

### 💻 Java Code:

```java
java
CopyEdit
int V = 4;
int[][] graph = new int[V][V]; // initialized to 0 by default

void addEdge(int u, int v, int weight) {
    graph[u][v] = weight;
    graph[v][u] = weight; // omit for directed graph
}

```

---

### 🧪 Dry Run Example:

Edges: (0,1,10), (0,2,5), (1,2,2)

```
csharp
CopyEdit
graph[][] =
[ [0, 10, 5, 0],
  [10, 0, 2, 0],
  [5, 2, 0, 0],
  [0, 0, 0, 0] ]

```

---

### 🖼️ Console Diagram:

```
lua
CopyEdit
     10
0 -------- 1
 \        /
  5      2
   \    /
     2
     2
      \
        2
         \
           2
            2
             \
              2
               \
0 ---5--- 2 ---2--- 1

```

---

### ⏱️ Time and Space:

- **Space**: O(V²)
- **Add edge**: O(1)
- **Check weight of edge (u,v)**: O(1)

---

## ✅ 2. **Weighted Adjacency List**

### 🧠 Core Idea:

- Each vertex stores a **list of (neighbor, weight)** pairs.
- Use a custom class or `Pair<Integer, Integer>`.

---

### 💻 Java Code:

```java
java
CopyEdit
class Pair {
    int vertex;
    int weight;
    Pair(int v, int w) {
        vertex = v;
        weight = w;
    }
}

int V = 4;
List<List<Pair>> adj = new ArrayList<>();

// Initialize
for (int i = 0; i < V; i++) {
    adj.add(new ArrayList<>());
}

// Add edge
void addEdge(int u, int v, int weight) {
    adj.get(u).add(new Pair(v, weight));
    adj.get(v).add(new Pair(u, weight)); // omit for directed
}

```

---

### 🧪 Dry Run Example:

Edges: (0,1,10), (0,2,5), (1,2,2)

```
cpp
CopyEdit
adj = [
  [(1,10), (2,5)],      // vertex 0
  [(0,10), (2,2)],      // vertex 1
  [(0,5), (1,2)],       // vertex 2
  []                   // vertex 3
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
      \
        2
         \
           2
0 --5-- 2 --2-- 1

```

---

### ⏱️ Time and Space:

- **Space**: O(V + E)
- **Add edge**: O(1)
- **Get neighbors**: O(degree of vertex)

---

## ✅ 3. **Weighted Edge List**

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

# 🔁 Directed Graph Representations

In a **directed graph**, an edge `(u → v)` means you can go from `u` to `v`, but **not** from `v` to `u`.

We’ll now adapt the following representations:

1. **Adjacency Matrix**
2. **Adjacency List**
3. **Edge List**

For each, you’ll get:

- Core concept
- Java code with comments
- Dry run example
- Console-style diagram

---

## ✅ 1. **Directed Adjacency Matrix**

### 🧠 Core Idea:

Use a 2D matrix where `graph[u][v] = weight` **only if there is an edge from `u` to `v`**.

No need to set `graph[v][u]`.

---

### 💻 Java Code:

```java
java
CopyEdit
int V = 4;
int[][] graph = new int[V][V]; // initialized to 0

void addDirectedEdge(int u, int v, int weight) {
    graph[u][v] = weight; // only one direction
}

```

---

### 🧪 Dry Run:

Edges: (0→1,10), (0→2,5), (1→2,2)

```
csharp
CopyEdit
graph[][] =
[ [0, 10, 5, 0],
  [0, 0, 2, 0],
  [0, 0, 0, 0],
  [0, 0, 0, 0] ]

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
     2 |
      |

```

---

## ✅ 2. **Directed Adjacency List**

### 🧠 Core Idea:

Each vertex `u` maintains a list of pairs `(v, weight)` where **`v` is a neighbor reachable from `u`**.

---

### 💻 Java Code:

```java
java
CopyEdit
class Pair {
    int vertex, weight;
    Pair(int v, int w) {
        vertex = v;
        weight = w;
    }
}

int V = 4;
List<List<Pair>> adj = new ArrayList<>();

// Initialize
for (int i = 0; i < V; i++) {
    adj.add(new ArrayList<>());
}

// Add directed edge
void addDirectedEdge(int u, int v, int weight) {
    adj.get(u).add(new Pair(v, weight)); // only one direction
}

```

---

### 🧪 Dry Run:

Edges: (0→1,10), (0→2,5), (1→2,2)

```
cpp
CopyEdit
adj = [
  [(1,10), (2,5)],  // 0
  [(2,2)],          // 1
  [],               // 2
  []                // 3
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
