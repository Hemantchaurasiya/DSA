## ✅ 1 Adjacency Matrix

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

## ✅ 2. **Weighted Adjacency Matrix Represantion**

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

## ✅ 3. **Directed Adjacency Matrix**

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
