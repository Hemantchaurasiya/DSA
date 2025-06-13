# Number of Enclaves

---

## Problem Overview
Given a binary grid (`0`s and `1`s), where `1` represents land and `0` represents water, an **enclave** is a group of land cells (**connected 4-directionally**) that are **completely enclosed** by water and **do not** touch the boundary of the grid. The task is to find the number of land cells that are part of enclaves.

---

## Approach 1: Brute Force (Identify and Remove boundary-connected land)

### Core Idea
Any land connected to the boundary cannot be part of an enclave. So, first identify all boundary-connected land cells and mark them as visited or remove them. The remaining land cells are enclaves.

### Algorithm
1. Traverse all boundary cells:
   - For each boundary cell (first row, last row, first column, last column), if it is land (`1`) and unvisited, perform DFS/BFS to mark all connected land cells as visited (or water).
2. After removing all boundary-connected land, count all remaining land cells (`1`) as enclaves.
3. Return the count.

### Java Code
```java
public int numEnclaves(int[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;

    // Remove boundary-connected land
    for (int i = 0; i < rows; i++) {
        if (grid[i][0] == 1) dfs(grid, i, 0);
        if (grid[i][cols - 1] == 1) dfs(grid, i, cols - 1);
    }
    for (int j = 0; j < cols; j++) {
        if (grid[0][j] == 1) dfs(grid, 0, j);
        if (grid[rows - 1][j] == 1) dfs(grid, rows - 1, j);
    }

    // Count remaining land cells (enclaves)
    int enclaveCount = 0;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 1) {
                enclaveCount++;
            }
        }
    }
    return enclaveCount;
}

private void dfs(int[][] grid, int r, int c) {
    int rows = grid.length;
    int cols = grid[0].length;
    if (r < 0 || c < 0 || r >= rows || c >= cols || grid[r][c] == 0) {
        return;
    }
    grid[r][c] = 0; // Mark as visited
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}
```

### Complexity Analysis
- **Time Complexity:**  
  - O(N*M), where N and M are grid dimensions.  
  - Each cell is visited at most once during the boundary removal and counting.
- **Space Complexity:**  
  - O(N*M) for recursion stack in worst case (deep DFS).

---

## Approach 2: BFS for Boundary Removal (More iterative)

### Core Idea
Similar to DFS, but use BFS to iteratively traverse and remove boundary-connected land cells.

### Algorithm
1. Initialize a queue for BFS.
2. Enqueue all boundary land cells.
3. Pop from queue, mark visited (or remove), and enqueue their land neighbors.
4. After BFS completes, count remaining land cells for enclaves.

### Java Code
```java
import java.util.LinkedList;
import java.util.Queue;

public int numEnclaves(int[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    Queue<int[]> queue = new LinkedList<>();

    // Add boundary land cells to queue
    for (int i = 0; i < rows; i++) {
        if (grid[i][0] == 1) queue.offer(new int[]{i, 0});
        if (grid[i][cols - 1] == 1) queue.offer(new int[]{i, cols - 1});
    }
    for (int j = 0; j < cols; j++) {
        if (grid[0][j] == 1) queue.offer(new int[]{0, j});
        if (grid[rows - 1][j] == 1) queue.offer(new int[]{rows - 1, j});
    }

    int[][] directions = {{-1,0},{1,0},{0,-1},{0,1}};

    // Remove boundary connected land
    while (!queue.isEmpty()) {
        int[] cell = queue.poll();
        int r = cell[0], c = cell[1];
        if (grid[r][c] == 1) {
            grid[r][c] = 0; // Mark as visited
            for (int[] dir : directions) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && grid[nr][nc] == 1) {
                    queue.offer(new int[]{nr, nc});
                }
            }
        }
    }

    // Count remaining land cells (enclaves)
    int enclaveCount = 0;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 1) {
                enclaveCount++;
            }
        }
    }
    return enclaveCount;
}
```

### Complexity Analysis
- **Time Complexity:**  
  - O(N*M), since each cell is processed at most once.
- **Space Complexity:**  
  - O(N*M) for the queue in the worst case.

---

## Approach 3: Optimal Solution - Using DFS/BFS with In-Place Modification

### Core Idea
Same as above, but optimized for clarity and efficiency:
- Start from boundary land cells.
- Remove all boundary-connected land cells via DFS or BFS.
- Count remaining land cells.

### Summary
This approach effectively "peels off" all land connected to the boundary, leaving only enclaves, which are then counted.

---

## Dry Run

### Example Input:
```
grid = [
  [0,0,0,0],
  [1,0,1,0],
  [0,1,1,0],
  [0,0,0,0]
]
```

| Step | Boundary Cells | Action | Remaining Grid | Enclaves to Count |
|-------|------------------|---------|------------------|-------------------|
| Initial | Boundary land? No | - | Same | - |
| Remove boundary-connected land | None | No land on boundary | Same | Count land in inner cells |
| Final | - | Count remaining land cells | (1,2), (2,1), (2,2) | 3 |

**Result:** 3 enclaves.

---

## Summary Table

| Approach | Key Idea | Pros | Cons | Use Case |
|------------|--------------|--------|--------|------------|
| Brute Force (DFS) | Remove boundary-connected land via recursion | Simple, easy to implement | Can cause stack overflow on large grids | Small grids, debugging |
| BFS | Remove boundary land iteratively | Less risk of stack overflow, iterative | Slightly more verbose | Larger grids, iterative preference |
| In-Place Removal + Counting | Efficient boundary removal + counting remaining land | Most optimal, minimal extra space | Slightly complex logic | Large inputs, performance-critical |

---

## Final Tips
- Always remove the land connected to the boundary first, as these cannot be enclaves.
- Use DFS or BFS based on preference; BFS avoids recursion stack issues.
- After removal, count remaining land cells for the result.
- Think of the problem as "peeling off" the boundary-connected land to reveal enclaves.

---

Happy Revising!
