# Shortest Path in Binary Matrix

This problem asks for the shortest path from the top-left cell `(0, 0)` to the bottom-right cell `(n-1, n-1)` in a binary matrix, where:

- `0` indicates an open cell,
- `1` indicates a blocked cell.

You can move in 8 directions (up, down, left, right, and the four diagonals).

---

## 1. Approach 1: Brute Force (Recursive DFS)

### Algorithm:

- Use **Depth-First Search (DFS)** to explore all possible paths from `(0,0)` to `(n-1, n-1)`.
- Maintain a `visited` matrix to avoid revisiting cells.
- For each cell, recurse into all 8 possible directions if the cell is valid (inside grid, not visited, and value is `0`).
- Track the length of the current path.
- Update the minimum path length whenever the destination is reached.

### Java Code:

```java
class Solution {
    int minPath = Integer.MAX_VALUE;
    int[] directions = {-1, 0, 1}; // for exploring neighbors in 8 directions
    int n;

    public int shortestPathBinaryMatrix(int[][] grid) {
        n = grid.length;
        boolean[][] visited = new boolean[n][n];

        if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1)
            return -1;

        dfs(grid, 0, 0, 1, visited);
        return minPath == Integer.MAX_VALUE ? -1 : minPath;
    }

    private void dfs(int[][] grid, int row, int col, int pathLength, boolean[][] visited) {
        if (row == n - 1 && col == n - 1) {
            minPath = Math.min(minPath, pathLength);
            return;
        }

        visited[row][col] = true;

        for (int dr : directions) {
            for (int dc : directions) {
                if (dr == 0 && dc == 0) continue;
                int newRow = row + dr;
                int newCol = col + dc;
                if (isValid(grid, newRow, newCol, visited)) {
                    dfs(grid, newRow, newCol, pathLength + 1, visited);
                }
            }
        }

        visited[row][col] = false;
    }

    private boolean isValid(int[][] grid, int row, int col, boolean[][] visited) {
        return row >= 0 && row < n && col >= 0 && col < n
                && grid[row][col] == 0
                && !visited[row][col];
    }
}

```

### Complexity Analysis:

- **Time Complexity:** `O(8^(n^2))` in the worst case because exploring all possible paths with 8 directions at each step.
- **Space Complexity:** `O(n^2)` for the recursion stack and the visited matrix.

### Dry Run:

- For a small grid, e.g.,

```
grid = [
  [0, 1],
  [0, 0]
]

```

- Start at `(0,0)`, recurse into neighbors, find the shortest path to `(1,1)` via `(0,0) -> (1,0) -> (1,1)`.

---

## 2. Approach 2: Breadth-First Search (BFS) - Optimal Solution

### Algorithm:

- Use **Breadth-First Search (BFS)** to explore the grid level by level.
- Initialize a queue with the starting cell `(0,0)` if it is `0`.
- Track the number of steps taken.
- For each cell dequeued:
    - Check if it’s the destination `(n-1, n-1)`; if yes, return the current distance.
    - Explore all 8 valid neighbors that are `0` and not visited.
    - Mark visited cells to avoid reprocessing.
- If the destination is unreachable, return `1`.

### Java Code:

```java
import java.util.LinkedList;
import java.util.Queue;

class Solution {
    private static final int[][] DIRECTIONS = {
        {-1, -1}, {-1, 0}, {-1, 1},
        {0, -1},           {0, 1},
        {1, -1},  {1, 0},  {1, 1}
    };

    public int shortestPathBinaryMatrix(int[][] grid) {
        int n = grid.length;
        if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1)
            return -1;
        boolean[][] visited = new boolean[n][n];
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{0, 0, 1}); // {row, col, distance}
        visited[0][0] = true;

        while (!queue.isEmpty()) {
            int[] cell = queue.poll();
            int row = cell[0], col = cell[1], dist = cell[2];

            if (row == n - 1 && col == n - 1)
                return dist;

            for (int[] dir : DIRECTIONS) {
                int newRow = row + dir[0];
                int newCol = col + dir[1];

                if (isValid(grid, newRow, newCol, visited)) {
                    visited[newRow][newCol] = true;
                    queue.offer(new int[]{newRow, newCol, dist + 1});
                }
            }
        }
        return -1;
    }

    private boolean isValid(int[][] grid, int row, int col, boolean[][] visited) {
        int n = grid.length;
        return row >= 0 && row < n && col >= 0 && col < n
                && grid[row][col] == 0
                && !visited[row][col];
    }
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n^2)` because each cell is visited at most once.
- **Space Complexity:** `O(n^2)` for the visited array and queue in the worst case.

### Dry Run:

- For the same example:

```
grid = [
  [0, 1],
  [0, 0]
]

```

- Start at `(0,0)` with distance `1`.
- Enqueue neighbors:
    - `(1,0)` (since open), distance `2`.
- From `(1,0)`, explore neighbors:
    - `(1,1)` (destination), distance `3`.
- Return `3` as shortest path length.

---

## 3. Approach 3: A* Search (Optional, for advanced optimization)

- Typically used for pathfinding with heuristics.
- Not necessary here unless the grid is exceedingly large.
- It combines BFS with heuristics to prioritize promising paths.

---

# Summary Table

| Aspect | Brute Force (DFS) | BFS (Optimal) |
| --- | --- | --- |
| Approach | Recursive DFS exploring all paths | Level-by-level BFS exploring shortest paths |
| Implementation Complexity | Higher (requires backtracking and pruning) | Simpler, straightforward queue-based traversal |
| Time Complexity | Exponential `O(8^(n^2))` | Polynomial `O(n^2)` |
| Space Complexity | `O(n^2)` (recursion + visited) | `O(n^2)` (visited + queue) |
| Use Case | Conceptual, not efficient for large grids | Efficient and suitable for large grids |

---

# Final Tips for Interview:

- Always check edge cases: start or end blocked.
- Use BFS for shortest path in unweighted grid.
- Mark visited cells to avoid infinite loops.
- Explore all 8 directions for diagonal movement.
- Aim for BFS for optimal performance.

---

Feel free to ask if you'd like a specific implementation or further explanation!
