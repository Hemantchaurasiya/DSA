# Detect Cycles in a 2D Grid

This guide provides comprehensive approaches to detect cycles in a 2D grid (matrix). It covers from naive solutions to the most optimized methods, suitable for interview prep and quick revision.

---

## 1. Brute Force Approach

### **Approach Name:**  
**DFS/BFS Cycle Detection in 2D Grid (Naive)**

### **Core Idea:**  
- Treat each cell as a node in a graph.
- Explore all neighboring cells to find cycles.
- A cycle exists if during traversal, we revisit a node that is not the immediate parent (i.e., back edge detection in an undirected graph).

### **Algorithm:**  
1. For each cell in the grid:
   - If not visited, initiate DFS/BFS.
2. During traversal:
   - Mark the current cell as visited.
   - For each neighbor (up, down, left, right):
     - If neighbor is unvisited and has the same value, recurse.
     - If neighbor is visited and is not the parent, cycle exists.
3. Continue until all cells are processed.
4. Return true if a cycle is detected in any traversal, else false.

### **Java Code:**
```java
public class DetectCycleInGrid {

    private int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};
    private boolean[][] visited;

    public boolean containsCycle(char[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        visited = new boolean[rows][cols];

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (!visited[i][j]) {
                    if (dfs(grid, i, j, -1, -1))
                        return true;
                }
            }
        }
        return false;
    }

    private boolean dfs(char[][] grid, int row, int col, int parentRow, int parentCol) {
        visited[row][col] = true;
        char value = grid[row][col];

        for (int[] dir : directions) {
            int newRow = row + dir[0];
            int newCol = col + dir[1];

            if (newRow >= 0 && newRow < grid.length &&
                newCol >= 0 && newCol < grid[0].length &&
                grid[newRow][newCol] == value) {

                if (!visited[newRow][newCol]) {
                    if (dfs(grid, newRow, newCol, row, col))
                        return true;
                } else if (newRow != parentRow || newCol != parentCol) {
                    // Visited neighbor that is not parent => cycle
                    return true;
                }
            }
        }
        return false;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(R * C)** — Each cell is visited at most once because of the visited array.  
- **Space Complexity:** **O(R * C)** — For visited array and recursion stack in worst case.

---

## 2. Better Solution (Optimized DFS with Parent Tracking)

### **Approach Name:**  
**DFS for Cycle Detection in Undirected Grid**

### **Core Idea:**  
- Similar to the brute-force, but emphasizing careful parent tracking to avoid false positives.
- Detect cycles by checking if a visited neighbor is not the parent.

### **Algorithm:**  
Same as above, but with explicit parent tracking to avoid false cycle detection in an undirected context.

### **Java Code:**  
*(Same as above, explicitly passing parent coordinates)*

```java
private boolean dfs(char[][] grid, int row, int col, int parentRow, int parentCol) {
    visited[row][col] = true;
    char value = grid[row][col];

    for (int[] dir : directions) {
        int newRow = row + dir[0];
        int newCol = col + dir[1];

        if (newRow >= 0 && newRow < grid.length &&
            newCol >= 0 && newCol < grid[0].length &&
            grid[newRow][newCol] == value) {

            if (!visited[newRow][newCol]) {
                if (dfs(grid, newRow, newCol, row, col))
                    return true;
            } else if (newRow != parentRow || newCol != parentCol) {
                // Visited neighbor (not parent) => cycle
                return true;
            }
        }
    }
    return false;
}
```

### **Complexity:**  
Same as brute-force.

---

## 3. Most Optimal Solution (Union-Find / Disjoint Set Union)

### **Approach Name:**  
**Union-Find (Disjoint Set Union - DSU) for Cycle Detection**

### **Core Idea:**  
- Use Union-Find data structure to keep track of connected components.
- For each pair of neighboring cells with the same value:
  - If they belong to the same set, a cycle exists.
  - Otherwise, union their sets.
- If at any point, a union operation detects an already connected set, a cycle exists.

### **Algorithm:**  
1. Initialize Union-Find structure for all cells.
2. For each cell:
   - For each neighbor with the same value:
     - If find(parent) of both cells is same, cycle detected.
     - Else, union their sets.
3. Return true if cycle found, else false.

### **Implementation Details:**  
- Map 2D grid cells to a 1D index for Union-Find.
- Perform union/find operations efficiently.

### **Java Code:**
```java
public class DetectCycleInGridUnionFind {

    int[] parent;
    int[] rank;

    public boolean containsCycle(char[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        int size = rows * cols;
        parent = new int[size];
        rank = new int[size];

        for (int i = 0; i < size; i++) {
            parent[i] = i;
            rank[i] = 0;
        }

        int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};

        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                int index1 = r * cols + c;
                for (int[] dir : directions) {
                    int nr = r + dir[0], nc = c + dir[1];
                    if (nr >= 0 && nr < rows && nc >= 0 && nc < cols &&
                        grid[nr][nc] == grid[r][c]) {
                        int index2 = nr * cols + nc;
                        if (find(index1) == find(index2))
                            return true; // cycle detected
                        union(index1, index2);
                    }
                }
            }
        }
        return false;
    }

    private int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);
        return parent[x];
    }

    private void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] < rank[rootY]) {
                parent[rootX] = rootY;
            } else if (rank[rootY] < rank[rootX]) {
                parent[rootY] = rootX;
            } else {
                parent[rootY] = rootX;
                rank[rootX]++;
            }
        }
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(R * C)** — Each cell and neighbor processed once with efficient union-find.  
- **Space Complexity:** **O(R * C)** — For parent and rank arrays.

---

## **Summary Table**

| **Approach** | **Core Idea** | **Detection Method** | **Time Complexity** | **Space Complexity** |
|--------------|----------------|----------------------|---------------------|----------------------|
| **1. DFS with Parent Tracking** | Explore all paths for cycles | Backtracking with parent check | O(R * C) | O(R * C) |
| **2. Union-Find (Disjoint Set)** | Use DSU to detect cycles | Union-Find operations | O(R * C) | O(R * C) |

---

## **Final Tips for Interviews:**
- For grid-based cycle detection, DFS with parent tracking is straightforward.
- Union-Find is efficient for large grids with many connections.
- Always mark visited cells to avoid reprocessing.
- When dealing with large grids, prefer Union-Find for optimal performance.

---

**Happy Revising!**
