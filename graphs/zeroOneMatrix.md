# 01 Matrix

---

## Problem Overview
Given a binary matrix `mat` of size `m x n`, where each cell is either `0` or `1`, the task is to find the distance of the **nearest 0** for each cell. The distance is calculated as the minimum number of steps to reach a cell containing `0`, moving only up, down, left, or right.

---

## Approach 1: Brute Force - Multiple BFS/DFS from Each Cell

### **Core Idea**
- For each cell containing `1`, perform a BFS or DFS to find the nearest `0`.
- This approach is straightforward but inefficient because it repeats searches from each `1`.

### **Algorithm**
1. Iterate over each cell:
   - If the cell is `1`, initiate a BFS/DFS to find the nearest `0`.
   - Record the distance when a `0` is found.
2. Return the result matrix with distances.

### **Java Code (Naive BFS from each `1`)**
```java
import java.util.LinkedList;
import java.util.Queue;

public int[][] updateMatrix(int[][] mat) {
    int m = mat.length;
    int n = mat[0].length;
    int[][] result = new int[m][n];

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (mat[i][j] == 1) {
                result[i][j] = bfs(mat, i, j);
            } else {
                result[i][j] = 0; // Already zero
            }
        }
    }
    return result;
}

private int bfs(int[][] mat, int startX, int startY) {
    int m = mat.length;
    int n = mat[0].length;
    boolean[][] visited = new boolean[m][n];
    Queue<int[]> queue = new LinkedList<>();
    queue.offer(new int[]{startX, startY, 0}); // {x, y, distance}
    int[][] directions = {{-1,0},{1,0},{0,-1},{0,1}};

    while (!queue.isEmpty()) {
        int[] cell = queue.poll();
        int x = cell[0], y = cell[1], dist = cell[2];
        if (x < 0 || y < 0 || x >= m || y >= n || visited[x][y]) continue;
        visited[x][y] = true;
        if (mat[x][y] == 0) return dist;
        for (int[] dir : directions) {
            queue.offer(new int[]{x + dir[0], y + dir[1], dist + 1});
        }
    }
    return Integer.MAX_VALUE; // shouldn't reach here
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - O((M*N) * (M*N)) = O(M² * N²), because for each cell, BFS can explore up to all other cells.
- **Space Complexity:**  
  - O(M*N) for the visited array and queue per BFS.

**Note:** This brute-force approach is highly inefficient and not suitable for large matrices.

---

## Approach 2: Better Solution - Multi-Source BFS

### **Core Idea**
- Instead of running BFS from each `1`, enqueue **all** `0` cells initially.
- Perform a multi-source BFS:
  - All `0`s are starting points (distance 0).
  - Expand outwards, updating neighboring `1`s with their shortest distance to a `0`.
- This ensures each cell's shortest distance is computed efficiently.

### **Algorithm**
1. Initialize a queue.
2. Loop through the grid:
   - For each `0`, enqueue its position with distance 0.
   - For each `1`, initialize the distance as a large number or leave as default.
3. Process the queue:
   - For each cell, explore neighbors:
     - If the neighbor's current distance > current cell's distance + 1:
       - Update neighbor's distance.
       - Enqueue neighbor.
4. Return the updated distance matrix.

### **Java Code**
```java
import java.util.LinkedList;
import java.util.Queue;

public int[][] updateMatrix(int[][] mat) {
    int m = mat.length;
    int n = mat[0].length;
    int[][] dist = new int[m][n];
    Queue<int[]> queue = new LinkedList<>();

    // Initialize distances and enqueue all '0's
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (mat[i][j] == 0) {
                dist[i][j] = 0;
                queue.offer(new int[]{i, j});
            } else {
                dist[i][j] = Integer.MAX_VALUE;
            }
        }
    }

    int[][] directions = {{-1,0},{1,0},{0,-1},{0,1}};

    // Multi-source BFS
    while (!queue.isEmpty()) {
        int[] cell = queue.poll();
        int x = cell[0], y = cell[1];
        for (int[] dir : directions) {
            int nx = x + dir[0], ny = y + dir[1];
            if (nx >= 0 && ny >= 0 && nx < m && ny < n) {
                if (dist[nx][ny] > dist[x][y] + 1) {
                    dist[nx][ny] = dist[x][y] + 1;
                    queue.offer(new int[]{nx, ny});
                }
            }
        }
    }
    return dist;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - O(M*N), each cell processed at most once, as distances are updated with minimum values.
- **Space Complexity:**  
  - O(M*N) for the `dist` array and queue.

---

## Approach 3: Optimized Solution - Dynamic Programming (Two Passes)

### **Core Idea**
- Use a DP approach:
  - First pass: from top-left to bottom-right, update distances based on top and left neighbors.
  - Second pass: from bottom-right to top-left, update distances based on bottom and right neighbors.
- This method propagates the minimum distance efficiently in two passes.

### **Algorithm**
1. Initialize a `dist` matrix:
   - Set `dist[i][j]` to 0 if `mat[i][j]` is 0.
   - Else, set to a large number.
2. **First pass (top-left to bottom-right):**
   - For each cell, update `dist[i][j]` as:
     - `min(dist[i][j], dist[i-1][j] + 1, dist[i][j-1] + 1)`
3. **Second pass (bottom-right to top-left):**
   - For each cell, update `dist[i][j]` as:
     - `min(dist[i][j], dist[i+1][j] + 1, dist[i][j+1] + 1)`

### **Java Code**
```java
public int[][] updateMatrix(int[][] mat) {
    int m = mat.length;
    int n = mat[0].length;
    int[][] dist = new int[m][n];
    int max = m + n; // max possible distance

    // Initialize distances
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (mat[i][j] == 0) {
                dist[i][j] = 0;
            } else {
                dist[i][j] = max;
            }
        }
    }

    // Top-left to bottom-right pass
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (i > 0) dist[i][j] = Math.min(dist[i][j], dist[i-1][j] + 1);
            if (j > 0) dist[i][j] = Math.min(dist[i][j], dist[i][j-1] + 1);
        }
    }

    // Bottom-right to top-left pass
    for (int i = m - 1; i >= 0; i--) {
        for (int j = n - 1; j >= 0; j--) {
            if (i < m - 1) dist[i][j] = Math.min(dist[i][j], dist[i+1][j] + 1);
            if (j < n - 1) dist[i][j] = Math.min(dist[i][j], dist[i][j+1] + 1);
        }
    }

    return dist;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - O(M*N), as each cell is processed twice.
- **Space Complexity:**  
  - O(M*N) for the `dist` matrix.

---

## Dry Run Example

### Input:
```
[
  [0,1,1],
  [1,1,1],
  [1,1,0]
]
```

### Process:
- Initialize `dist`:
```
[
  [0, max, max],
  [max, max, max],
  [max, max, 0]
]
```
- First pass (top-left to bottom-right):
```
i=0,j=1: min(max, dist[0][0]+1)=1
i=0,j=2: min(max, dist[0][1]+1)=2
i=1,j=0: min(max, dist[0][0]+1)=1
i=1,j=1: min(max, dist[1][0]+1, dist[0][1]+1)=2
i=1,j=2: min(max, dist[1][1]+1, dist[0][2]+1)=3
i=2,j=0: min(max, dist[1][0]+1)=2
i=2,j=1: min(max, dist[2][0]+1, dist[1][1]+1)=3
i=2,j=2: min(0, dist[2][1]+1)=1
```
- Second pass (bottom-right to top-left):
```
i=2,j=2: no change
i=2,j=1: min(3, dist[2][2]+1)=3
i=2,j=0: min(2, dist[2][1]+1)=2
i=1,j=2: min(3, dist[2][2]+1)=2
i=1,j=1: min(2, dist[2][1]+1, dist[1][2]+1)=2
i=1,j=0: min(1, dist[2][0]+1)=1
i=0,j=2: min(2, dist[1][2]+1)=2
i=0,j=1: min(1, dist[0][2]+1, dist[1][1]+1)=1
i=0,j=0: min(0, dist[0][1]+1)=0
```
- Final `dist`:
```
[
  [0, 1, 2],
  [1, 2, 1],
  [2, 1, 0]
]
```

---

## Summary

| Approach | Key Idea | Pros | Cons | Best Use Case |
|------------|--------------|--------|--------|--------------|
| Naive BFS/DFS | From each `1`, find nearest `0` | Simple concept | Very slow for large grids | Small grids or learning purposes |
| Multi-Source BFS | Enqueue all `0`s and expand | Efficient, linear time | Extra space for queue | Large grids, performance critical |
| DP Two-Pass | Propagate distances in two passes | Optimal, no extra queue | Slightly complex implementation | Large grids, memory-efficient |

---

## Final Tips
- Use multi-source BFS for clarity and efficiency.
- For optimal performance, DP two-pass method is excellent.
- Always initialize distances properly.
- Understand boundary conditions and ensure all directions are explored.

---

Happy Revising!
