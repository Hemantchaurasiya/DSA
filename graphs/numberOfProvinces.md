# Number of Provinces

---

## Problem Overview
Given an `n x n` matrix `isConnected`, where `isConnected[i][j] = 1` indicates that city `i` is directly connected to city `j`, and `0` indicates no direct connection, **determine the number of provinces** (connected components).

- **Input:** 2D matrix `isConnected`
- **Output:** Integer representing the number of provinces

---

## Approach 1: Brute Force (DFS/BFS without careful tracking)
### Core Idea
Treat each city as a node in a graph and explore connections to identify connected components. Initially, without marking visited nodes, you might repeatedly traverse the same nodes, leading to inefficient exploration.

*(Note: This approach highlights the conceptual idea but is impractical without visited tracking.)*

### Algorithm
- For each city, initiate a DFS/BFS if it is not yet visited.
- Expand to all directly connected cities.
- Mark all reachable cities as visited.
- Count how many times you initiate a new traversal; each corresponds to a new province.

### Java Code (with visited array)
```java
public class NumberOfProvinces {
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        boolean[] visited = new boolean[n];
        int count = 0;

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                dfs(i, isConnected, visited);
                count++;
            }
        }
        return count;
    }

    private void dfs(int city, int[][] isConnected, boolean[] visited) {
        visited[city] = true;
        for (int j = 0; j < isConnected.length; j++) {
            if (isConnected[city][j] == 1 && !visited[j]) {
                dfs(j, isConnected, visited);
            }
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N^2), since in the worst case, every element in the matrix is checked once.
- **Space Complexity:** O(N), for the `visited` array and recursion stack.

---

## Approach 2: Union-Find (Disjoint Set Union - DSU)
### Core Idea
Use **Union-Find data structure** to efficiently merge connected cities into sets. The number of disjoint sets after processing all connections gives the number of provinces.

### Algorithm
1. Initialize each city as its own parent (representing separate sets).
2. For each pair `(i, j)`:
   - If `isConnected[i][j] == 1`, perform a union operation on `i` and `j`.
3. Count the number of unique parents (roots) after all unions, which represents the number of provinces.

### Java Code
```java
public class NumberOfProvinces {
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        int[] parent = new int[n];
        int count = n;

        // Initialize each city as its own parent
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }

        // Union connected cities
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (isConnected[i][j] == 1) {
                    if (union(parent, i, j)) {
                        count--;
                    }
                }
            }
        }
        return count;
    }

    private boolean union(int[] parent, int x, int y) {
        int rootX = find(parent, x);
        int rootY = find(parent, y);
        if (rootX != rootY) {
            parent[rootY] = rootX;
            return true;
        }
        return false;
    }

    private int find(int[] parent, int x) {
        if (parent[x] != x) {
            parent[x] = find(parent, parent[x]);
        }
        return parent[x];
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N^2), due to iterating over the matrix.
- **Space Complexity:** O(N), for the parent array.

---

## Approach 3: DFS / BFS with Visited Tracking (Most Optimal)
### Core Idea
Leverage DFS or BFS traversal to count connected components directly, marking each visited city to avoid reprocessing.

### Algorithm
1. Initialize a `visited` array.
2. For each city:
   - If it is not visited:
     - Perform DFS/BFS to mark all connected cities as visited.
     - Increment the province count.
3. Return the total count after processing all cities.

### Java Code (DFS)
```java
public class NumberOfProvinces {
    public int findCircleNum(int[][] isConnected) {
        int n = isConnected.length;
        boolean[] visited = new boolean[n];
        int provinces = 0;

        for (int i = 0; i < n; i++) {
            if (!visited[i]) {
                dfs(i, isConnected, visited);
                provinces++;
            }
        }
        return provinces;
    }

    private void dfs(int city, int[][] isConnected, boolean[] visited) {
        visited[city] = true;
        for (int j = 0; j < isConnected.length; j++) {
            if (isConnected[city][j] == 1 && !visited[j]) {
                dfs(j, isConnected, visited);
            }
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N^2), as each cell in the matrix is examined at most once.
- **Space Complexity:** O(N), for the `visited` array and recursion stack.

---

## Dry Run: Example

### Input:
```plaintext
isConnected = [
  [1, 1, 0],
  [1, 1, 0],
  [0, 0, 1]
]
```

---

### DFS Approach:

| Step | Current City | Visited Array | Action | Connection Checks | Notes |
|-------|----------------|----------------|---------|---------------------|--------|
| 1     | 0              | [true, false, false] | Start DFS from city 0 | Connects to city 1 | |
| 2     | 1              | [true, true, false]  | Visit city 1 | Already connected to city 0 | |
| 3     | 2              | [true, true, true]   | City 2 is isolated | No further connections | |

**Result:** 2 provinces (`{0,1}` and `{2}`)

---

## Summary
| Approach | Best Use Case | Pros | Cons |
|------------|--------------|-------|-------|
| DFS/BFS with visited | Most straightforward and efficient for connected components | Simple, effective, easy to implement | Recursion depth may grow with large inputs |
| Union-Find | Efficient for dynamic connectivity | Fast union/find operations | Slightly more complex to implement |

---

## Final Recommendation
Use **DFS or BFS with visited tracking** for clarity and efficiency in most scenarios.

---

**Happy Revising!**
