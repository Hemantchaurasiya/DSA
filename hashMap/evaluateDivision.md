# Evaluate Division

---

## Problem Overview

Given a list of equations like `a / b = k`, and queries asking for the value of division between two variables, determine the answers for each query. If the division is not possible, return `-1.0`.

---

## Approach 1: Brute Force (Naive DFS without Preprocessing)

### Core Idea
Attempt to find the division value by traversing the graph of equations directly for each query. Explore all possible paths between the source and destination variables.

### Algorithm
1. **Build Graph:** Construct a graph where each variable is a node, and each equation `a / b = k` creates a bidirectional edge:
   - From `a` to `b` with weight `k`.
   - From `b` to `a` with weight `1/k`.

2. **For each query `(start, end)`**:
   - Run a DFS or BFS starting from `start`.
   - Keep track of the product of weights along the path.
   - If `end` is reached, return the accumulated product.
   - If not reachable, return `-1.0`.

### Java Code
```java
class Solution {
    Map<String, Map<String, Double>> graph = new HashMap<>();

    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        // Build the graph
        for (int i = 0; i < equations.size(); i++) {
            String A = equations.get(i).get(0);
            String B = equations.get(i).get(1);
            graph.putIfAbsent(A, new HashMap<>());
            graph.putIfAbsent(B, new HashMap<>());
            graph.get(A).put(B, values[i]);
            graph.get(B).put(A, 1.0 / values[i]);
        }

        double[] results = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String start = queries.get(i).get(0);
            String end = queries.get(i).get(1);
            Set<String> visited = new HashSet<>();
            results[i] = dfs(start, end, 1.0, visited);
        }
        return results;
    }

    private double dfs(String current, String target, double accProduct, Set<String> visited) {
        if (!graph.containsKey(current) || !graph.containsKey(target)) return -1.0;
        if (current.equals(target)) return accProduct;
        visited.add(current);
        for (Map.Entry<String, Double> neighbor : graph.get(current).entrySet()) {
            if (!visited.contains(neighbor.getKey())) {
                double result = dfs(neighbor.getKey(), target, accProduct * neighbor.getValue(), visited);
                if (result != -1.0) return result;
            }
        }
        return -1.0;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  - Building the graph: `O(E)` where `E` is the number of equations.
  - For each query, DFS may explore all nodes in the worst case: `O(V + E)`.
  - Total: `O(Q * (V + E))`, where `Q` is number of queries.
- **Space Complexity:**  
  - Graph storage: `O(V + E)`.

---

## Approach 2: Using Union-Find (Disjoint Set Union - DSU)

### Core Idea
Model the division relations as connected components with ratios stored as weights. Use Union-Find to merge sets and store ratios that help directly compute division results between connected variables.

### Algorithm
1. **Initialize Union-Find structure:**
   - Each variable is its own parent.
   - Store a `ratio` array/dictionary to keep track of the ratio between a node and its parent.

2. **Union Operation:**
   - When processing an equation `a / b = k`, union the sets containing `a` and `b`.
   - Adjust the ratios accordingly so that `a / b = k` holds after union.

3. **Find Operation:**
   - Find the root parent of a node.
   - During path compression, update the ratio to the root to maintain correct ratios.

4. **Answering Queries:**
   - For `a / b`, if `a` and `b` share the same root, compute `ratio[a] / ratio[b]`.
   - Else, return `-1.0`.

### Java Code
```java
class Solution {
    Map<String, String> parent = new HashMap<>();
    Map<String, Double> ratio = new HashMap<>(); // ratio to parent

    public double[] calcEquation(List<List<String>> equations, double[] values, List<List<String>> queries) {
        // Initialize Union-Find
        for (List<String> eq : equations) {
            String a = eq.get(0);
            String b = eq.get(1);
            if (!parent.containsKey(a)) {
                parent.put(a, a);
                ratio.put(a, 1.0);
            }
            if (!parent.containsKey(b)) {
                parent.put(b, b);
                ratio.put(b, 1.0);
            }
        }

        // Union the variables
        for (int i = 0; i < equations.size(); i++) {
            union(equations.get(i).get(0), equations.get(i).get(1), values[i]);
        }

        double[] results = new double[queries.size()];
        for (int i = 0; i < queries.size(); i++) {
            String a = queries.get(i).get(0);
            String b = queries.get(i).get(1);
            if (!parent.containsKey(a) || !parent.containsKey(b)) {
                results[i] = -1.0;
            } else {
                String rootA = find(a);
                String rootB = find(b);
                if (rootA.equals(rootB)) {
                    results[i] = ratio.get(a) / ratio.get(b);
                } else {
                    results[i] = -1.0;
                }
            }
        }
        return results;
    }

    private String find(String x) {
        if (!parent.get(x).equals(x)) {
            String origParent = parent.get(x);
            parent.put(x, find(origParent));
            ratio.put(x, ratio.get(x) * ratio.get(origParent));
        }
        return parent.get(x);
    }

    private void union(String a, String b, double value) {
        String rootA = find(a);
        String rootB = find(b);
        if (!rootA.equals(rootB)) {
            parent.put(rootA, rootB);
            // Adjust ratio to maintain the equation a / b = value
            ratio.put(rootA, ratio.get(b) * value / ratio.get(a));
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  - Building Union-Find: `O(E)` for equations.
  - Each union and find operation approximately `O(α(N))` (inverse Ackermann), practically constant.
  - Total for queries: `O(Q * α(N))`.
- **Space Complexity:**  
  - `O(V)` for parent and ratio mappings.

---

## Approach 3: Most Optimal Solution — **Union-Find with Path Compression and Ratio Maintenance**

This approach is generally the most efficient for large input sizes, combining the advantages of Union-Find with ratio tracking for constant-time query resolution after preprocessing.

---

## Summary Table

| Approach | Core Idea | Algorithm Highlights | Code Snippet | Time Complexity | Space Complexity |
|------------|------------|------------------------|--------------|-----------------|------------------|
| Brute Force | DFS traversal per query | Build graph, DFS for each query | Provided above | `O(Q*(V+E))` | `O(V+E)` |
| Union-Find | Disjoint sets with ratio tracking | Union sets with ratio adjustments, find with path compression | Provided above | `O(Q*α(N))`, practically constant | `O(V)` |

---

## Final Tips for Interview Preparation
- Understand graph traversal (DFS/BFS) for problems involving relational data.
- Master Union-Find data structure, especially with ratio or weight tracking.
- Practice both approaches to understand their trade-offs.
- Pay attention to edge cases: unknown variables, division by zero, disconnected components.

---

This comprehensive revision should help you quickly recall and understand various strategies to solve the **Evaluate Division** problem efficiently during interviews.
