# M-Coloring Problem

The **M-Coloring Problem** involves determining whether it is possible to color the vertices of a graph using at most **M** different colors such that no two adjacent vertices share the same color.

---

## 1. Brute Force Approach

### Core Idea:
Try all possible color assignments for each vertex and check if any assignment satisfies the constraints.

### Algorithm:
- For each vertex, try assigning one of the **M** colors.
- Recursively proceed to the next vertex.
- If at any point a color assignment violates the adjacency constraint, backtrack and try a different color.
- If all vertices are assigned valid colors, return true.

### Recursion Tree:
- At each vertex, **M** choices.
- Total nodes: \( O(M^V) \), where V is the number of vertices.
  
```
           [Start]
           /   |   \
        Color1 Color2 ... ColorM
            |
           Next vertex...
```

### Java Code:
```java
public class MColoring {
    public boolean graphColoring(int[][] graph, int m, int[] colors, int vertex) {
        int n = graph.length;
        if (vertex == n) {
            return true; // All vertices are assigned
        }

        for (int c = 1; c <= m; c++) {
            if (isSafe(graph, colors, vertex, c)) {
                colors[vertex] = c;
                if (graphColoring(graph, m, colors, vertex + 1))
                    return true;
                // Backtrack
                colors[vertex] = 0;
            }
        }
        return false;
    }
    
    private boolean isSafe(int[][] graph, int[] colors, int vertex, int c) {
        for (int v = 0; v < graph.length; v++) {
            if (graph[vertex][v] == 1 && colors[v] == c) {
                return false;
            }
        }
        return true;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** \( O(M^V) \) — Each vertex attempts M colors, leading to exponential growth.
- **Space Complexity:** \( O(V) \) — For recursion stack and color array.

### Dry Run Example:
Suppose graph with 3 vertices and edges:
- Edges: (0-1), (1-2)
- M = 2 (colors: 1 and 2)

Initial call:
- vertex 0, try color 1:
  - vertex 1, try color 1: invalid (adjacent to vertex 0 with color 1)
  - try color 2: valid
    - vertex 2, try color 1: valid (adjacent to vertex 1 with color 2)
    - success: return true

---

## 2. Better Solution (Backtracking with Pruning)

### Core Idea:
Use the same backtracking approach but include **pruning** to avoid unnecessary recursive calls when constraints are violated.

### Algorithm:
- Same as brute-force but with an **isSafe** check before assignment.
- Prune the search space by skipping invalid color choices immediately.

### Implementation:
Same as above, emphasizing the **isSafe** check as the pruning step.

### Complexity:
- Still exponential in the worst case, but pruning reduces the search space in practice.

---

## 3. Most Optimal Solution: **Backtracking with Degree Ordering (Heuristic)**

### Core Idea:
Order vertices based on their degree (number of neighbors) and assign colors starting from the most constrained vertices to reduce backtracking.

### Algorithm:
- Calculate degrees of vertices.
- Sort vertices in decreasing order of degree.
- Assign colors following the order, applying the same recursive backtracking with pruning.

### Java Code:
```java
import java.util.*;

public class MColoringOptimized {
    public boolean graphColoring(int[][] graph, int m) {
        int n = graph.length;
        int[] colors = new int[n];

        // Order vertices by degree
        Integer[] vertices = new Integer[n];
        for (int i = 0; i < n; i++) vertices[i] = i;
        Arrays.sort(vertices, (a, b) -> Integer.compare(degree(graph, b), degree(graph, a)));

        return backtrack(graph, m, colors, vertices, 0);
    }

    private boolean backtrack(int[][] graph, int m, int[] colors, Integer[] vertices, int index) {
        if (index == vertices.length) return true;

        int v = vertices[index];
        for (int c = 1; c <= m; c++) {
            if (isSafe(graph, colors, v, c)) {
                colors[v] = c;
                if (backtrack(graph, m, colors, vertices, index + 1))
                    return true;
                colors[v] = 0; // Backtrack
            }
        }
        return false;
    }

    private boolean isSafe(int[][] graph, int[] colors, int v, int c) {
        for (int adj = 0; adj < graph.length; adj++) {
            if (graph[v][adj] == 1 && colors[adj] == c)
                return false;
        }
        return true;
    }

    private int degree(int[][] graph, int v) {
        int count = 0;
        for (int i = 0; i < graph.length; i++) {
            if (graph[v][i] == 1) count++;
        }
        return count;
    }
}
```

### Complexity:
- **Time Complexity:** Still exponential in the worst case, but heuristics often reduce the search space.
- **Space Complexity:** \( O(V) \) for color and recursion stack.

---

## **Summary**

| Approach | Core Idea | Algorithm | Time Complexity | Space Complexity | Remarks |
|------------|--------------|--------------|---------------------|------------------------|-----------|
| Brute Force | Try all color combinations | Recursive backtracking with isSafe check | \( O(M^V) \) | \( O(V) \) | Exponential, naive |
| Better Solution | Pruning with isSafe | Same as brute-force, optimized | \( O(M^V) \) (pruned) | \( O(V) \) | Slight improvement in practice |
| Optimized with Degree Ordering | Heuristic ordering of vertices | Backtracking with degree-based ordering | Typically faster in practice | \( O(V) \) | Often reduces backtracking |

---

## **Final Tips for Interview & Revision**
- Understand the brute-force backtracking approach thoroughly.
- Recognize the importance of the **isSafe** check for pruning.
- Use heuristics like degree ordering to improve practical performance.
- Be clear about the exponential nature of the problem and that it's NP-Complete.
- Practice dry runs with small graphs to build intuition.

---

**Happy Revising!**
