## ✅ 1. **Adjacency List**

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

## ✅ 3. **Directed Adjacency List**

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