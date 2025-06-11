# All Paths From Source to Target

---

## Problem Overview:
Given a directed acyclic graph (DAG) of `n` nodes labeled from `0` to `n-1`, find all possible paths from node `0` (source) to node `n-1` (target).

---

## Input:
- `graph`: List of lists, where `graph[i]` contains all nodes reachable from node `i`.

---

## Output:
- List of all paths from source (`0`) to target (`n-1`).

---

## Approach 1: Brute Force (Backtracking / DFS)

### **Core Idea:**
Explore all possible paths from the source node to the target node. Since the graph is a DAG, we can traverse all paths recursively, and collect paths when reaching the target.

---

### **Algorithm:**
1. Initialize an empty list `result` to store all paths.
2. Define a recursive helper function `dfs(node, path)`:
   - Append the current node to `path`.
   - If `node` is the target (`n-1`), append a copy of `path` to `result`.
   - Else, for each neighbor of `node`, recurse `dfs(neighbor, path)`.
   - Backtrack by removing the current node from `path`.
3. Call `dfs(0, [])`.
4. Return `result`.

---

### **Recursion Tree Diagram:**

```
Start at node 0
├── Explore neighbor 1
│   ├── Explore neighbor 3
│   │   └── Reach target 4 → Save path [0,1,3,4]
│   └── Explore neighbor 2
│       └── Reach target 4 → Save path [0,1,2,4]
├── Explore neighbor 2
│   └── Explore neighbor 3
│       └── Reach target 4 → Save path [0,2,3,4]
```

---

### **Java Code:**
```java
import java.util.*;

public class AllPathsFromSourceToTarget {
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        List<List<Integer>> result = new ArrayList<>();
        List<Integer> path = new ArrayList<>();
        dfs(graph, 0, path, result);
        return result;
    }

    private void dfs(int[][] graph, int node, List<Integer> path, List<List<Integer>> result) {
        path.add(node);
        if (node == graph.length - 1) {
            result.add(new ArrayList<>(path));
        } else {
            for (int neighbor : graph[node]) {
                dfs(graph, neighbor, path, result);
            }
        }
        path.remove(path.size() - 1); // backtrack
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(2^n)** in the worst case, since each node can branch into multiple recursive calls. The number of paths can be exponential in the size of the graph.

- **Space Complexity:**  
  **O(n * 2^n)** for storing all paths (in the worst case), plus recursion stack space **O(n)**.

---

### **Dry Run Example:**

**Input:**
```plaintext
graph = [[1,2], [3], [3], [4], []]
```

**Step-by-step:**

| Step | Current Node | Path       | Action | Result/Next Step             |
|-------|--------------|------------|---------|------------------------------|
| 1     | 0            | [0]      | Explore neighbors | 1, 2                         |
| 2     | 1            | [0, 1]   | Explore neighbors | 3                            |
| 3     | 3            | [0, 1, 3]| Reached target?   | Yes → Save [0,1,3,4]         |
| 4     | Back to 1    | [0, 1]   | Remove 3 | Backtrack to 1             |
| 5     | 2            | [0, 2]   | Explore neighbor 3 | 3                            |
| 6     | 3            | [0, 2, 3]| Reached target? | Yes → Save [0,2,3,4]         |
| 7     | Back to 2    | [0, 2]   | Remove 3 | Backtrack to 2             |
| 8     | Back to 0    | [0]      | Remove 1, 2 | End of recursion |

**Output:**
```plaintext
[[0,1,3,4], [0,2,3,4]]
```

---

## Approach 2: Memoization / Dynamic Programming (Optimized)

### **Core Idea:**
Use memoization to avoid repeated computations. Store paths from each node to the target once computed.

---

### **Algorithm:**
1. Create a memoization map/dictionary: `Map<Integer, List<List<Integer>>> memo`.
2. Define a recursive function `dfs(node)`:
   - If `node` is the target (`n-1`), return a list containing a single path `[node]`.
   - If `node` is in `memo`, return the stored result.
   - Else, for each neighbor:
     - Recursively get all paths from neighbor to target.
     - For each path, prepend current node.
   - Store all paths in `memo[node]` and return.
3. Call `dfs(0)` and return the result.

---

### **Java Code:**
```java
import java.util.*;

public class AllPathsFromSourceToTargetMemo {
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        Map<Integer, List<List<Integer>>> memo = new HashMap<>();
        return dfs(graph, 0, memo);
    }

    private List<List<Integer>> dfs(int[][] graph, int node, Map<Integer, List<List<Integer>>> memo) {
        if (node == graph.length - 1) {
            List<List<Integer>> paths = new ArrayList<>();
            paths.add(Arrays.asList(node));
            return paths;
        }
        if (memo.containsKey(node)) {
            return memo.get(node);
        }
        List<List<Integer>> result = new ArrayList<>();
        for (int neighbor : graph[node]) {
            for (List<Integer> path : dfs(graph, neighbor, memo)) {
                List<Integer> newPath = new ArrayList<>();
                newPath.add(node);
                newPath.addAll(path);
                result.add(newPath);
            }
        }
        memo.put(node, result);
        return result;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  **O(N * 2^N)** in the worst case, due to memoization reducing repeated calculations, but still exponential in the number of paths.

- **Space Complexity:**  
  **O(N * 2^N)** for storing all paths in the worst case, plus recursion stack.

---

## Approach 3: BFS with Path Tracking (Less Common)

### **Core Idea:**
Use BFS to explore all paths iteratively while maintaining the current path in the queue.

---

### **Algorithm:**
1. Initialize a queue of `(node, path)` pairs, starting with `(0, [0])`.
2. While queue not empty:
   - Dequeue a `(node, path)` pair.
   - If `node` == target, add `path` to result.
   - Else, enqueue `(neighbor, path + [neighbor])` for all neighbors.
3. Return all collected paths.

---

### **Java Code:**
```java
import java.util.*;

public class AllPathsBFS {
    public List<List<Integer>> allPathsSourceTarget(int[][] graph) {
        List<List<Integer>> result = new ArrayList<>();
        Queue<Pair> queue = new LinkedList<>();
        queue.offer(new Pair(0, Arrays.asList(0)));

        while (!queue.isEmpty()) {
            Pair current = queue.poll();
            int node = current.node;
            List<Integer> path = current.path;

            if (node == graph.length - 1) {
                result.add(new ArrayList<>(path));
            } else {
                for (int neighbor : graph[node]) {
                    List<Integer> newPath = new ArrayList<>(path);
                    newPath.add(neighbor);
                    queue.offer(new Pair(neighbor, newPath));
                }
            }
        }
        return result;
    }

    private static class Pair {
        int node;
        List<Integer> path;
        Pair(int node, List<Integer> path) {
            this.node = node;
            this.path = path;
        }
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  Similar to DFS, **O(2^n)** in the worst case, as all paths are explored.

- **Space Complexity:**  
  **O(2^n * n)** for storing all paths and the queue.

---

# **Summary & Final Tips:**

- **Backtracking DFS:** Most straightforward; explores all paths; exponential time.
- **Memoization:** Avoids recomputations; helpful for larger graphs with overlapping paths.
- **BFS:** Alternative approach; more iterative; suitable if you prefer level-wise traversal.
- **Optimality:** For generating all paths, exponential time is unavoidable due to the output size.

---

This comprehensive guide covers multiple approaches to solve **All Paths From Source to Target**, suitable for quick revision and interview prep.
