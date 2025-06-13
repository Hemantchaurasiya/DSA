# Number of Closed Islands

---

## Problem Overview
Given a 2D grid consisting of `'0'`s (land) and `'1'`s (water), a **closed island** is a group of `'0'`s (connected 4-directionally) that is **completely surrounded** by `'1'`s (water). The boundary `'0'`s are **not** counted as closed islands because they touch the boundary, meaning they are not fully enclosed.

**Goal:** Count the number of closed islands in the grid.

---

## Approach 1: Brute Force - DFS to Count and Mark Enclosed Regions

### **Core Idea**
- Identify `'0'`s on the boundary or connected to boundary, as they cannot be part of a closed island.
- Use DFS to traverse and mark all `'0'`s connected to the boundary as visited (or water).
- Count and traverse remaining `'0'`s that are not connected to boundary; these represent closed islands.

### **Algorithm**
1. Traverse all boundary cells:
   - For each `'0'` on boundary, perform DFS to mark all connected `'0'`s as visited.
2. Initialize a count for closed islands.
3. Iterate through the entire grid:
   - When an unvisited `'0'` is found (not connected to boundary), perform DFS to mark all connected `'0'`s as visited.
   - Increment the closed island count.
4. Return the total count.

### **Java Code**
```java
public int closedIsland(int[][] grid) {
    int m = grid.length;
    int n = grid[0].length;

    // Mark boundary-connected '0's as visited
    for (int i = 0; i < m; i++) {
        if (grid[i][0] == 0) dfs(grid, i, 0);
        if (grid[i][n - 1] == 0) dfs(grid, i, n - 1);
    }
    for (int j = 0; j < n; j++) {
        if (grid[0][j] == 0) dfs(grid, 0, j);
        if (grid[m - 1][j] == 0) dfs(grid, m - 1, j);
    }

    int count = 0;
    // Count remaining unvisited '0's which are enclosed
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 0) {
                dfs(grid, i, j);
                count++;
            }
        }
    }
    return count;
}

private void dfs(int[][] grid, int r, int c) {
    int m = grid.length;
    int n = grid[0].length;
    if (r < 0 || c < 0 || r >= m || c >= n || grid[r][c] != 0) return;
    grid[r][c] = -1; // mark as visited
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - O(M*N), since each cell is visited at most once during boundary marking and counting.
- **Space Complexity:**  
  - O(M*N) for recursion stack in worst case.

---

## Approach 2: BFS for Boundary Marking

### **Core Idea**
- Use BFS instead of DFS to mark `'0'`s connected to boundary as visited.
- Count the number of enclosed `'0'` regions after boundary traversal.

### **Algorithm**
1. Traverse boundary cells:
   - For each `'0'`, enqueue and mark as visited.
2. Use BFS:
   - Dequeue cells, mark connected `'0'`s as visited.
3. After boundary marking, count remaining `'0'`s, each of which represents a closed island.

### **Java Code**
```java
import java.util.LinkedList;
import java.util.Queue;

public int closedIsland(int[][] grid) {
    int m = grid.length;
    int n = grid[0].length;
    Queue<int[]> queue = new LinkedList<>();
    
    // Enqueue boundary '0's
    for (int i = 0; i < m; i++) {
        if (grid[i][0] == 0) queue.offer(new int[]{i, 0});
        if (grid[i][n - 1] == 0) queue.offer(new int[]{i, n - 1});
    }
    for (int j = 0; j < n; j++) {
        if (grid[0][j] == 0) queue.offer(new int[]{0, j});
        if (grid[m - 1][j] == 0) queue.offer(new int[]{m - 1, j});
    }

    int[][] directions = {{-1,0},{1,0},{0,-1},{0,1}};

    // BFS to mark boundary-connected '0's
    while (!queue.isEmpty()) {
        int[] cell = queue.poll();
        int r = cell[0], c = cell[1];
        if (grid[r][c] != 0) continue;
        grid[r][c] = -1; // mark as visited
        for (int[] dir : directions) {
            int nr = r + dir[0], nc = c + dir[1];
            if (nr >= 0 && nr < m && nc >= 0 && nc < n && grid[nr][nc] == 0) {
                queue.offer(new int[]{nr, nc});
            }
        }
    }

    int count = 0;
    // Count remaining '0's which are enclosed
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (grid[i][j] == 0) {
                count++;
                dfs(grid, i, j); // Mark entire enclosed region
            }
        }
    }
    return count;
}

private void dfs(int[][] grid, int r, int c) {
    if (r < 0 || c < 0 || r >= grid.length || c >= grid[0].length || grid[r][c] != 0) return;
    grid[r][c] = -1; // mark as visited
    dfs(grid, r - 1, c);
    dfs(grid, r + 1, c);
    dfs(grid, r, c - 1);
    dfs(grid, r, c + 1);
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - O(M*N), each cell processed once.
- **Space Complexity:**  
  - O(M*N) for the queue.

---

## Approach 3: In-Place Marking with Final Count (Most Optimal)

### **Core Idea**
- First, mark all `'0'`s connected to boundary as non-enclosed.
- Then, iterate through the grid:
  - Any remaining `'0'`s are part of closed islands.
  - Count and mark them as visited.

### **Summary**
This approach combines boundary traversal with in-place marking, minimizing extra space usage and ensuring linear time complexity.

---

## Dry Run

### Example Input:
```
[
  [1,1,1,1,1,1,1],
  [1,0,0,1,0,0,1],
  [1,0,1,1,1,0,1],
  [1,1,1,1,1,1,1]
]
```

**Steps:**
- Boundary `'0'`s at `(1,1)`, `(1,2)`, `(2,1)`, `(2,5)`, `(1,5)`, `(2,5)` are connected to the boundary; mark as safe.
- Remaining `'0'`s not connected to boundary are enclosed.
- Count enclosed `'0'`s.

---

## Summary Table

| Approach | Key Idea | Pros | Cons | Best Use Case |
|------------|--------------|--------|--------|--------------|
| DFS Marking | Recursive boundary marking | Simple implementation | Stack overflow risk on large grids | Small to medium grids |
| BFS Marking | Iterative boundary marking | No recursion limit | Slightly more verbose | Large grids |
| In-Place + Final Counting | Efficient boundary marking and counting | Most optimal, minimal extra space | Slightly complex logic | Large inputs, performance-critical |

---

## Final Tips
- Always mark `'0'`s connected to the boundary first, as they are not part of closed islands.
- Use DFS or BFS based on recursion limits and grid size.
- After boundary marking, count and mark remaining `'0'`s as enclosed.
- Focus on in-place modifications to optimize space.

---

Happy Revising!
