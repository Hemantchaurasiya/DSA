# Cycle Detection using DFS

# 🔁 Cycle Detection in **directed Graphs** using DFS

---

## ✅ Core Idea

In a **directed graph**, a cycle exists if you can start at a node and **follow a path that eventually returns to the same node**, respecting the direction of edges.

### ❗Problem:

Given a directed graph, determine **if there is any cycle** in it.

---

## 🧠 DFS-Based Strategy

DFS alone won’t detect cycles **just by visited[]** — because directed edges don’t guarantee connectivity in both directions.

### ✨ So we need two arrays:

1. `visited[]`: marks nodes we've **visited at all**
2. `recStack[]` (recursion stack): tracks **nodes in the current DFS path**

---

### 🔄 Key Idea:

- When DFS revisits a node that is **already in the recursion stack**, we’ve found a **cycle**.

---

## 📝 Step-by-Step Algorithm

1. Initialize `visited[]` and `recStack[]` as `false`
2. For each unvisited node `v`, call `dfs(v)`
3. In `dfs(v)`:
    - Mark `visited[v] = true`, `recStack[v] = true`
    - For each neighbor `u` of `v`:
        - If `u` is not visited → recurse: `dfs(u)`
        - If `u` is in `recStack` → **Cycle found**
    - On backtracking: `recStack[v] = false`

---

## ☕ Java Code – Cycle Detection (Directed Graph)

```java
java
CopyEdit
import java.util.*;

public class DirectedCycleDetectionDFS {
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
            adj.get(u).add(v); // Directed edge
        }

        // Cycle Detection
        boolean hasCycle() {
            boolean[] visited = new boolean[V];
            boolean[] recStack = new boolean[V];

            for (int i = 0; i < V; i++) {
                if (!visited[i]) {
                    if (dfs(i, visited, recStack)) {
                        return true;
                    }
                }
            }
            return false;
        }

        boolean dfs(int node, boolean[] visited, boolean[] recStack) {
            visited[node] = true;
            recStack[node] = true;

            for (int neighbor : adj.get(node)) {
                if (!visited[neighbor]) {
                    if (dfs(neighbor, visited, recStack)) return true;
                } else if (recStack[neighbor]) {
                    return true; // Found a cycle
                }
            }

            recStack[node] = false; // Backtrack
            return false;
        }
    }

    public static void main(String[] args) {
        Graph g1 = new Graph(4);
        g1.addEdge(0, 1);
        g1.addEdge(1, 2);
        g1.addEdge(2, 3);
        g1.addEdge(3, 1); // cycle

        System.out.println("Graph has cycle? " + g1.hasCycle()); // true

        Graph g2 = new Graph(4);
        g2.addEdge(0, 1);
        g2.addEdge(1, 2);
        g2.addEdge(2, 3);
        // no cycle

        System.out.println("Graph has cycle? " + g2.hasCycle()); // false
    }
}

```

---

## ⏱️ Time and Space Complexity

| Metric | Value |
| --- | --- |
| Time Complexity | O(V + E) |
| Space Complexity | O(V) |
| Recursion Depth | O(V) (worst) |

---

## 🧪 Dry Run Example

### Graph 1:

```
markdown
CopyEdit
0 → 1 → 2 → 3
      ↑     ↓
      ← ← ←

```

- Start DFS at `0`: [0 → 1 → 2 → 3]
- `3 → 1` already in `recStack` → Cycle detected ✅

### Graph 2:

```
CopyEdit
0 → 1 → 2 → 3

```

- No node is visited twice in same DFS path → No cycle ❌

---

## 🎨 Console Diagram

### Graph 1 – Has Cycle:

```
cpp
CopyEdit
Visited stack trace:
0
 → 1
   → 2
     → 3
       → 1 (already in recStack → cycle found!)

```

---

## ✅ Summary

| Feature | Description |
| --- | --- |
| Uses DFS | Yes, recursively |
| Uses recursion stack | To track current path |
| Detects **directed cycles** | YES (not suitable for undirected cycles) |
| Important for | Course scheduling, build systems, dependency trees |

---

## 🧩 Related Problems

- [ ]  Course Schedule (LeetCode 207)
- [ ]  Course Schedule II (LeetCode 210)
- [ ]  Detect Cycle in a Directed Graph (GFG)

# 🔁 Cycle Detection in **Undirected Graphs** using DFS

---

## ✅ Core Idea

In an **undirected graph**, a cycle exists if during DFS traversal, we reach a visited node **that is not the parent** of the current node.

### Why “not parent”?

- Because in an undirected graph, every edge is bidirectional. So when you go from `u → v`, you'll naturally come back `v → u` — that’s not a cycle.
- A cycle occurs **only** if you revisit a node from a neighbor that's **not your parent**.

---

## 🧠 Strategy (DFS)

1. Use a `visited[]` array
2. For every unvisited node:
    - Run DFS
    - During DFS, if a neighbor is already visited and it's **not the parent**, then a cycle exists

---

## ☕ Java Code – Cycle Detection (Undirected Graph)

```java
java
CopyEdit
import java.util.*;

public class UndirectedCycleDetectionDFS {
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

        // Add undirected edge u — v
        void addEdge(int u, int v) {
            adj.get(u).add(v);
            adj.get(v).add(u);
        }

        boolean hasCycle() {
            boolean[] visited = new boolean[V];

            for (int i = 0; i < V; i++) {
                if (!visited[i]) {
                    if (dfs(i, -1, visited)) {
                        return true;
                    }
                }
            }
            return false;
        }

        // DFS with parent tracking
        boolean dfs(int current, int parent, boolean[] visited) {
            visited[current] = true;

            for (int neighbor : adj.get(current)) {
                if (!visited[neighbor]) {
                    if (dfs(neighbor, current, visited)) return true;
                }
                else if (neighbor != parent) {
                    // Visited and not the parent → cycle found
                    return true;
                }
            }

            return false;
        }
    }

    public static void main(String[] args) {
        Graph g1 = new Graph(5);
        g1.addEdge(0, 1);
        g1.addEdge(1, 2);
        g1.addEdge(2, 0);  // forms a cycle
        g1.addEdge(3, 4);

        System.out.println("Graph has cycle? " + g1.hasCycle()); // true

        Graph g2 = new Graph(5);
        g2.addEdge(0, 1);
        g2.addEdge(1, 2);
        g2.addEdge(3, 4);  // no cycle

        System.out.println("Graph has cycle? " + g2.hasCycle()); // false
    }
}

```

---

## ⏱️ Time and Space Complexity

| Metric | Value |
| --- | --- |
| Time Complexity | O(V + E) |
| Space Complexity | O(V + E) |
| Stack Depth | O(V) (worst) |

---

## 🧪 Dry Run Example

### Graph 1:

```
CopyEdit
0 — 1
|   |
2 —

```

### DFS Traversal:

- Start at 0 → 1 → 2
- 2 → 0 (already visited, not parent) → **Cycle found**

### Graph 2:

```
CopyEdit
0 — 1 — 2     3 — 4

```

### DFS Traversal:

- No revisiting of visited nodes from other than parent → No cycle

---

## 🎨 Console-Based Diagram

### Graph 1:

```
pgsql
CopyEdit
DFS Path:
0
 → 1
   → 2
     → 0 (already visited, not parent) → cycle found ✅

```

---

## ✅ Summary

| Property | Description |
| --- | --- |
| Applicable to | Undirected graphs only |
| Key Condition | Revisited node that is not the parent |
| DFS State | Tracks `visited[]` and `parent` |
| Usage | Union-Find alternative, graph validation |

---

## 🔗 Related Problems

- [ ]  Detect Cycle in Undirected Graph (GFG)
- [ ]  Is Graph a Tree?
- [ ]  Redundant Connection (LeetCode 684)