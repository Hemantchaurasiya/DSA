# 📘 Depth First Search (DFS) – Full Study Notes

---

## 1. 🌟 Core Idea of DFS

### 💡 Intuition:

Depth First Search (DFS) explores **as far as possible** along a branch before **backtracking**. Imagine navigating a maze and always going down a path until you hit a dead end — then you backtrack and try a different path.

### 🔍 Where DFS is used:

- **Graphs** (directed/undirected, cyclic/acyclic)
- **Trees** (DFS is implicit in traversals like inorder, preorder, postorder)
- **Backtracking Problems** (e.g., Sudoku, N-Queens, combinations)
- **Cycle detection** in graphs
- **Topological Sorting**
- **Strongly Connected Components** (Tarjan's and Kosaraju's algorithms)
- **Path Finding** (like finding all paths between two nodes)

---

## 2. 🧠 DFS Algorithm – Recursive and Iterative

### ✅ Recursive DFS – Step-by-Step:

1. Start at the source node.
2. Mark the node as visited.
3. Recursively visit all unvisited neighbors.

```java
void dfs(int node, boolean[] visited, List<List<Integer>> adj) {
    visited[node] = true;
    for (int neighbor : adj.get(node)) {
        if (!visited[neighbor]) {
            dfs(neighbor, visited, adj);
        }
    }
}

```

---

### ✅ Iterative DFS – Step-by-Step:

1. Use a **stack** to simulate the recursive call stack.
2. Start at the source node and push it to the stack.
3. While the stack is not empty:
    - Pop the top node.
    - If not visited, mark as visited and push unvisited neighbors.

```java
void iterativeDFS(int start, List<List<Integer>> adj) {
    boolean[] visited = new boolean[adj.size()];
    Stack<Integer> stack = new Stack<>();
    stack.push(start);

    while (!stack.isEmpty()) {
        int node = stack.pop();
        if (!visited[node]) {
            visited[node] = true;
            for (int neighbor : adj.get(node)) {
                if (!visited[neighbor]) {
                    stack.push(neighbor);
                }
            }
        }
    }
}

```

---

## 3. ☕ Java Code – DFS on Graph (Adjacency List)

```java
public class DFSGraph {
    static class Graph {
        int V;
        List<List<Integer>> adj;

        Graph(int V) {
            this.V = V;
            adj = new ArrayList<>();
            for (int i = 0; i < V; i++) {
                adj.add(new ArrayList<>());
            }
        }

        void addEdge(int u, int v) {
            adj.get(u).add(v);
            adj.get(v).add(u); // For undirected graph
        }

        // Recursive DFS
        void dfsRecursive(int start) {
            boolean[] visited = new boolean[V];
            System.out.print("Recursive DFS: ");
            dfsUtil(start, visited);
            System.out.println();
        }

        void dfsUtil(int node, boolean[] visited) {
            visited[node] = true;
            System.out.print(node + " ");
            for (int neighbor : adj.get(node)) {
                if (!visited[neighbor]) {
                    dfsUtil(neighbor, visited);
                }
            }
        }

        // Iterative DFS
        void dfsIterative(int start) {
            boolean[] visited = new boolean[V];
            Stack<Integer> stack = new Stack<>();
            stack.push(start);
            System.out.print("Iterative DFS: ");

            while (!stack.isEmpty()) {
                int node = stack.pop();
                if (!visited[node]) {
                    visited[node] = true;
                    System.out.print(node + " ");
                    for (int neighbor : adj.get(node)) {
                        if (!visited[neighbor]) {
                            stack.push(neighbor);
                        }
                    }
                }
            }
            System.out.println();
        }
    }

    public static void main(String[] args) {
        Graph g = new Graph(6);
        g.addEdge(0, 1);
        g.addEdge(0, 2);
        g.addEdge(1, 3);
        g.addEdge(1, 4);
        g.addEdge(2, 5);

        g.dfsRecursive(0);
        g.dfsIterative(0);
    }
}

```

---

## 4. ⏱️ Time and Space Complexity

| Variant | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- |
| Recursive DFS | O(V + E) | O(V) + O(H) | O(H) recursion stack (H = height of DFS tree) |
| Iterative DFS | O(V + E) | O(V + E) | Stack + visited array |
- **V = vertices**, **E = edges**
- DFS visits each node and edge once.

---

## 5. 🧪 Dry Run Examples

### 🔹 Graph:

```
        0
       / \
      1   2
     / \   \
    3   4   5

```

### 🔹 Adjacency List:

```
0: [1, 2]
1: [0, 3, 4]
2: [0, 5]
3: [1]
4: [1]
5: [2]

```

### 🧵 Recursive DFS from 0:

| Call Stack | Visited | Output |
| --- | --- | --- |
| dfs(0) | [0] | 0 |
| dfs(1) | [0, 1] | 1 |
| dfs(3) | [0, 1, 3] | 3 |
| return |  |  |
| dfs(4) | [0, 1, 3, 4] | 4 |
| return |  |  |
| dfs(2) | [0, 1, 3, 4, 2] | 2 |
| dfs(5) | [0,1,3,4,2,5] | 5 |

### 🔹 Output:

```
Recursive DFS: 0 1 3 4 2 5

```

---

### 🧵 Iterative DFS from 0:

Stack evolves (LIFO):

```
Start: push(0) → [0]
Pop 0 → Output: 0 → push(2,1) → [2,1]
Pop 1 → Output: 1 → push(4,3) → [2,4,3]
Pop 3 → Output: 3 → []
Pop 4 → Output: 4 → []
Pop 2 → Output: 2 → push(5) → [5]
Pop 5 → Output: 5 → []

```

### 🔹 Output:

```
Iterative DFS: 0 1 3 4 2 5
```

---

## 6. 🎨 Console-based Diagrams

### DFS Tree Representation:

```
DFS from node 0:

0
├── 1
│   ├── 3
│   └── 4
└── 2
    └── 5

```

### DFS Recursive Call Stack Visualization:

```
Call dfs(0)
 → dfs(1)
    → dfs(3)
    ← return
    → dfs(4)
    ← return
 ← return
 → dfs(2)
    → dfs(5)
    ← return
 ← return

```

---

## ✅ Summary & Tips

- **Recursive DFS** is more intuitive and concise but risks **stack overflow** on large graphs.
- **Iterative DFS** is safer for large graphs (no recursion limit).
- DFS is foundational in:
    - Cycle detection
    - Connected components
    - Topological sorting
    - Solving puzzles, generating permutations/combinations

---

# 🌐 DFS for Finding Connected Components

---

## ✅ What is a Connected Component?

A **connected component** in an undirected graph is a **subgraph in which any two vertices are connected to each other by paths**, and which is connected to no additional vertices outside the subgraph.

In simple terms:

- If you can reach all nodes in a group from any one of them, it's a connected component.

---

## 🧠 Approach Using DFS

### 🔁 Idea:

Use DFS to **explore and mark** all nodes reachable from a starting node. Repeat DFS from **unvisited** nodes to find all components.

---

## 📝 Step-by-Step Algorithm

1. Initialize a `visited[]` array of size `V` (vertices), all `false`.
2. Loop through each vertex `i` from 0 to V-1:
    - If `visited[i] == false`:
        - Run DFS from node `i`
        - Increment the `component count`
        - (Optionally) store the nodes of this component
3. DFS will mark all nodes in the current component as visited.

---

## ☕ Java Code – Connected Components using DFS

```java
public class ConnectedComponentsDFS {
    static class Graph {
        int V;
        List<List<Integer>> adj;

        Graph(int V) {
            this.V = V;
            adj = new ArrayList<>();
            for (int i = 0; i < V; i++) {
                adj.add(new ArrayList<>());
            }
        }

        void addEdge(int u, int v) {
            adj.get(u).add(v);
            adj.get(v).add(u); // Undirected graph
        }

        void findConnectedComponents() {
            boolean[] visited = new boolean[V];
            int componentCount = 0;

            for (int i = 0; i < V; i++) {
                if (!visited[i]) {
                    List<Integer> component = new ArrayList<>();
                    dfs(i, visited, component);
                    componentCount++;
                    System.out.println("Component " + componentCount + ": " + component);
                }
            }
            System.out.println("Total Connected Components: " + componentCount);
        }

        void dfs(int node, boolean[] visited, List<Integer> component) {
            visited[node] = true;
            component.add(node);
            for (int neighbor : adj.get(node)) {
                if (!visited[neighbor]) {
                    dfs(neighbor, visited, component);
                }
            }
        }
    }

    public static void main(String[] args) {
        Graph g = new Graph(8);
        g.addEdge(0, 1);
        g.addEdge(1, 2);
        g.addEdge(3, 4);
        g.addEdge(5, 6);
        g.addEdge(6, 7);

        g.findConnectedComponents();
    }
}

```

---

## ⏱️ Time and Space Complexity

| Aspect | Complexity |
| --- | --- |
| Time | O(V + E) |
| Space | O(V + E) |
| Components found | ≤ V (worst case) |

---

## 🧪 Dry Run Example

### Input:

Graph with 8 vertices:

```
Edges:
0-1
1-2
3-4
5-6
6-7

```

### Visualization:

```
Component 1: 0 — 1 — 2
Component 2: 3 — 4
Component 3: 5 — 6 — 7
```

### DFS Traversal:

1. Start at node 0 → discovers 0, 1, 2
2. Node 3 → discovers 3, 4
3. Node 5 → discovers 5, 6, 7

### Output:

```
Component 1: [0, 1, 2]
Component 2: [3, 4]
Component 3: [5, 6, 7]
Total Connected Components: 3
```

---

## 🎨 Console-based Diagram

```
Graph:

  Component 1       Component 2       Component 3
   0---1---2         3---4             5---6---7

DFS from 0: visits [0, 1, 2]
DFS from 3: visits [3, 4]
DFS from 5: visits [5, 6, 7]
```

---

## ✅ Summary

- Use DFS to explore each connected group.
- Every time you start DFS from an **unvisited node**, you're entering a **new component**.
- Works best on **undirected graphs**.

---

# 📘 DFS for Directed Graphs

---

## 1. 🌐 Understanding Directed Graphs

A **directed graph (or digraph)** is a graph where each edge has a direction — from node `u` to node `v`, denoted as `u → v`. This means:

- `u → v` does not imply `v → u`
- Edge direction **matters** in traversal

---

## 2. 🤔 DFS Behavior in Directed Graphs

### 🔍 Differences from Undirected DFS:

- The traversal only follows **outgoing edges**
- Nodes may be **reachable in one direction but not the other**
- Used heavily in:
    - **Topological sort**
    - **Cycle detection**
    - **Strongly connected components (SCCs)**

---

## 3. ✅ DFS Algorithm for Directed Graphs (Step-by-Step)

1. Start DFS from each **unvisited node**
2. In DFS, follow only **outgoing edges**
3. Mark each node as visited
4. Traverse to all unvisited neighbors

---

## ☕ Java Code – DFS on Directed Graph (Adjacency List)

```java
public class DirectedGraphDFS {
    static class Graph {
        int V;
        List<List<Integer>> adj;

        Graph(int V) {
            this.V = V;
            adj = new ArrayList<>();
            for (int i = 0; i < V; i++) {
                adj.add(new ArrayList<>());
            }
        }

        // Add directed edge u → v
        void addEdge(int u, int v) {
            adj.get(u).add(v);
        }

        // DFS on directed graph
        void dfs(int start) {
            boolean[] visited = new boolean[V];
            System.out.print("DFS from node " + start + ": ");
            dfsUtil(start, visited);
            System.out.println();
        }

        void dfsUtil(int node, boolean[] visited) {
            visited[node] = true;
            System.out.print(node + " ");
            for (int neighbor : adj.get(node)) {
                if (!visited[neighbor]) {
                    dfsUtil(neighbor, visited);
                }
            }
        }

        // DFS for all components (like finding all reachable groups)
        void dfsAll() {
            boolean[] visited = new boolean[V];
            for (int i = 0; i < V; i++) {
                if (!visited[i]) {
                    System.out.print("Component from " + i + ": ");
                    dfsUtil(i, visited);
                    System.out.println();
                }
            }
        }
    }

    public static void main(String[] args) {
        Graph g = new Graph(6);
        g.addEdge(0, 1);
        g.addEdge(1, 2);
        g.addEdge(2, 3);
        g.addEdge(4, 5);

        g.dfsAll();
    }
}

```

---

## ⏱️ Time and Space Complexity

| Aspect | Complexity |
| --- | --- |
| Time | O(V + E) |
| Space | O(V + E) |
| Recursive stack | O(H) |

---

## 4. 🧪 Dry Run Example

### 🔹 Graph:

```
Edges:
0 → 1
1 → 2
2 → 3
4 → 5

```

### 🔹 Visual:

```
Component 1: 0 → 1 → 2 → 3
Component 2: 4 → 5
```

### 🔹 Output:

```
Component from 0: 0 1 2 3
Component from 4: 4 5
```

---

## 5. 🎨 Console-based Diagram

```
DFS Traversal (Directed):

Component 1:
0 → 1 → 2 → 3

Component 2:
4 → 5

Traversal:
Start at 0:
- visit 0
  - visit 1
    - visit 2
      - visit 3

Start at 4:
- visit 4
  - visit 5

```

---

## ✅ Use Cases in Directed Graphs

| Application | DFS Role |
| --- | --- |
| **Topological Sort** | Post-order DFS + stack |
| **Cycle Detection** | DFS with recursion stack tracking |
| **SCCs (Kosaraju’s)** | Two DFS passes: forward and reverse graph |
| **Reachability Queries** | Use DFS to check if `u` can reach `v` |