# Topological Sorting

# 📐 Topological Sort using DFS

---

## ✅ Core Idea

A **topological sort** of a **directed acyclic graph (DAG)** is a **linear ordering of vertices** such that for every directed edge `u → v`, vertex `u` comes **before** `v` in the ordering.

> 🔺 Only DAGs (Directed Acyclic Graphs) have valid topological sorts.
> 

---

## 💡 Use Cases

| Use Case | Description |
| --- | --- |
| Task/Job Scheduling | Prerequisites: Task A must finish before B |
| Build/Dependency Resolution | Compile modules in the correct order |
| Course Scheduling | Finish prerequisites before next course |

---

## 🧠 Topological Sort via DFS: Algorithm

1. Initialize a `visited[]` array
2. Create an empty **stack** or **list** to store the ordering
3. For every unvisited node, run DFS:
    - Mark node as visited
    - Recursively visit all its neighbors
    - After visiting all neighbors, **add the node to the stack**
4. Once all nodes are processed, **reverse the stack** (or use list’s `addFirst()`)

---

## ☕ Java Code – Topological Sort using DFS

```java
java
CopyEdit
import java.util.*;

public class TopologicalSortDFS {
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
            adj.get(u).add(v); // Directed edge u → v
        }

        // Main method to get topological order
        List<Integer> topologicalSort() {
            boolean[] visited = new boolean[V];
            Stack<Integer> stack = new Stack<>();

            for (int i = 0; i < V; i++) {
                if (!visited[i]) {
                    dfs(i, visited, stack);
                }
            }

            // Pop from stack to get the topological ordering
            List<Integer> topoOrder = new ArrayList<>();
            while (!stack.isEmpty()) {
                topoOrder.add(stack.pop());
            }

            return topoOrder;
        }

        void dfs(int node, boolean[] visited, Stack<Integer> stack) {
            visited[node] = true;

            for (int neighbor : adj.get(node)) {
                if (!visited[neighbor]) {
                    dfs(neighbor, visited, stack);
                }
            }

            stack.push(node); // Add after visiting all neighbors
        }
    }

    public static void main(String[] args) {
        Graph g = new Graph(6);
        g.addEdge(5, 2);
        g.addEdge(5, 0);
        g.addEdge(4, 0);
        g.addEdge(4, 1);
        g.addEdge(2, 3);
        g.addEdge(3, 1);

        List<Integer> topoOrder = g.topologicalSort();
        System.out.println("Topological Sort (DFS): " + topoOrder);
    }
}

```

---

## 🔎 Example

### Input:

```
makefile
CopyEdit
Edges:
5 → 2
5 → 0
4 → 0
4 → 1
2 → 3
3 → 1

```

### One Valid Topo Sort Output:

```
CopyEdit
5 4 2 3 1 0

```

> Note: There can be multiple valid topological sorts.
> 

---

## ⏱ Time & Space Complexity

| Metric | Value |
| --- | --- |
| Time Complexity | O(V + E) |
| Space Complexity | O(V + E) |
| Stack Depth | O(V) (worst) |

---

## 🎨 Console-Based DFS Stack Diagram

```
perl
CopyEdit
DFS Stack Trace:

Start at 5 → 2 → 3 → 1 → push 1 → push 3 → push 2
Back to 5 → push 5

Start at 4 → 0 → push 0 → push 4

Final stack: [4, 0, 5, 1, 3, 2] → reverse it: [5, 4, 2, 3, 1, 0]

```

---

## 🛑 Important Notes

- If the graph **has a cycle**, topological sorting is **not possible**.
- Can be combined with **cycle detection** to validate a DAG.

---

## 🧩 Related Problems

- ✅ LeetCode 210 – Course Schedule II
- ✅ LeetCode 207 – Course Schedule (Cycle detection)
- ✅ [GFG – Topological Sort]

---

## ✅ Summary

| Feature | Description |
| --- | --- |
| Graph Type | Directed Acyclic Graph (DAG) |
| DFS Role | Post-order node processing |
| Output | Linear order respecting all dependencies |
| Edge Direction | `u → v` ⇒ `u` appears before `v` in output |

---

# 🔄 Kahn’s Algorithm – Topological Sort using BFS

---

## ✅ Core Idea

Kahn’s Algorithm builds a **topological order** using:

- **Indegree** of each node (number of incoming edges)
- **Queue** to store all nodes with `indegree == 0`

### 🔧 Key Insight:

- A node with `indegree = 0` can be safely placed first.
- Remove it and reduce indegree of its neighbors.
- If a neighbor becomes `indegree == 0`, add it to the queue.

---

## 💡 Use Case

| Use Case | Description |
| --- | --- |
| Job Scheduling | With dependency constraints |
| Course Prerequisites | Some courses must be taken before others |
| Detecting Cycles in DAG | If result size < V → cycle exists |

---

## 🧠 Step-by-Step Algorithm

1. Compute **indegree[]** for all vertices.
2. Push all **indegree 0** nodes into a **queue**.
3. While the queue is not empty:
    - Pop a node `u` and add it to result list
    - For all neighbors `v` of `u`, decrement `indegree[v]`
    - If `indegree[v]` becomes 0, add `v` to the queue
4. After traversal, check:
    - If result size == V → valid topological sort
    - Else → **Cycle detected**

---

## ☕ Java Code – Kahn’s Algorithm (BFS Topo Sort)

```java
java
CopyEdit
import java.util.*;

public class KahnsTopologicalSort {
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
            adj.get(u).add(v); // Directed edge u → v
        }

        List<Integer> kahnsTopoSort() {
            int[] indegree = new int[V];
            for (int u = 0; u < V; u++) {
                for (int v : adj.get(u)) {
                    indegree[v]++;
                }
            }

            Queue<Integer> queue = new LinkedList<>();
            for (int i = 0; i < V; i++) {
                if (indegree[i] == 0) {
                    queue.offer(i);
                }
            }

            List<Integer> topoOrder = new ArrayList<>();
            while (!queue.isEmpty()) {
                int u = queue.poll();
                topoOrder.add(u);

                for (int v : adj.get(u)) {
                    indegree[v]--;
                    if (indegree[v] == 0) {
                        queue.offer(v);
                    }
                }
            }

            if (topoOrder.size() != V) {
                throw new RuntimeException("Graph has a cycle. Topological sort not possible.");
            }

            return topoOrder;
        }
    }

    public static void main(String[] args) {
        Graph g = new Graph(6);
        g.addEdge(5, 2);
        g.addEdge(5, 0);
        g.addEdge(4, 0);
        g.addEdge(4, 1);
        g.addEdge(2, 3);
        g.addEdge(3, 1);

        List<Integer> topo = g.kahnsTopoSort();
        System.out.println("Topological Sort (BFS - Kahn's): " + topo);
    }
}

```

---

## 🔎 Example

```
css
CopyEdit
Input Edges:
5 → 2
5 → 0
4 → 0
4 → 1
2 → 3
3 → 1

```

**Initial Indegree:**

| Node | Indegree |
| --- | --- |
| 0 | 2 |
| 1 | 2 |
| 2 | 1 |
| 3 | 1 |
| 4 | 0 ✅ |
| 5 | 0 ✅ |

**Queue:** [4, 5]

→ Output: [4, 5, 2, 0, 3, 1] (or another valid one)

---

## ⏱ Time & Space Complexity

| Metric | Value |
| --- | --- |
| Time Complexity | O(V + E) |
| Space Complexity | O(V + E) |
| Queue Usage | O(V) |

---

## 🎨 Console-Based Diagram

```
yaml
CopyEdit
Queue: [4, 5]
→ Pop 4 → Result: [4], queue: [5]
→ Pop 5 → Result: [4, 5], queue: [2, 0]
→ Pop 2 → Result: [4, 5, 2], queue: [0, 3]
→ Pop 0 → Result: [4, 5, 2, 0], queue: [3]
→ Pop 3 → Result: [4, 5, 2, 0, 3], queue: [1]
→ Pop 1 → Result: [4, 5, 2, 0, 3, 1]

```

---

## ✅ Summary

| Feature | Description |
| --- | --- |
| Graph Type | Directed Acyclic Graph (DAG) |
| Technique | BFS using indegree tracking |
| Detects Cycles | Yes (if result size < V) |
| Output Order | One of possibly many valid topological orders |

---

## 🧩 Related Problems

- ✅ LeetCode 210 – Course Schedule II
- ✅ [LeetCode 207 – Course Schedule (cycle check)]
- ✅ [GFG – Topological sort using Kahn’s Algorithm]
