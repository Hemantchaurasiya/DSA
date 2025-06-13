# Shortest Path in Binary Matrix

---

## Problem Overview
Given an `n x n` binary matrix, where `0` represents an open cell and `1` represents a blocked cell, find the length of the shortest clear path from the top-left cell `(0,0)` to the bottom-right cell `(n-1, n-1)`.

- You can move to any of the 8 neighboring cells (including diagonals).
- You can only move through cells with value `0`.
- If no such path exists, return `-1`.

---

## Approach 1: Brute Force (DFS with Path Enumeration)

### **Core Idea:**
Explore all possible paths from `(0,0)` to `(n-1,n-1)` using DFS, tracking the shortest path. This approach is inefficient because it explores all paths, leading to exponential complexity.

### **Algorithm:**
1. Check if the start `(0,0)` or end `(n-1,n-1)` cells are blocked (`1`), return `-1` immediately.
2. Use DFS from `(0,0)`:
   - Keep track of visited cells.
   - Recursively explore all 8 directions.
   - For each valid, unvisited cell (`0`), recurse and update the minimum path length.
3. Return the minimum path length found, or `-1` if none.

**Note:** This approach is theoretically possible but computationally infeasible for large matrices.

---

### **Complexity Analysis:**
- **Time Complexity:** Exponential, due to exploring all paths.
- **Space Complexity:** `O(n^2)` for recursion stack and visited array.

---

## Approach 2: Breadth-First Search (BFS) — **Optimal and Efficient**

### **Core Idea:**
Use BFS to find the shortest path in an unweighted grid because BFS naturally finds the shortest path in graphs with equal weights. Moving in 8 directions, starting from `(0,0)`.

### **Algorithm:**
1. **Edge Cases:**
   - If `(0,0)` or `(n-1,n-1)` is blocked (`1`), return `-1`.
2. Initialize a queue and visited array.
3. Enqueue `(0,0)` with distance `1`.
4. While the queue is not empty:
   - Dequeue the current cell `(x, y)` and current distance.
   - If `(x, y)` is `(n-1, n-1)`, return the current distance.
   - Explore all 8 valid neighbors:
     - Valid if within bounds, not visited, and cell value `0`.
     - Mark visited and enqueue with distance + 1.
5. If no path is found, return `-1`.

---

### **Java Code:**
```java
import java.util.*;

public class ShortestPathBinaryMatrix {
    public int shortestPathBinaryMatrix(int[][] grid) {
        int n = grid.length;
        if (grid[0][0] == 1 || grid[n - 1][n - 1] == 1) return -1;
        int[][] directions = {{1,0},{-1,0},{0,1},{0,-1},{1,1},{1,-1},{-1,1},{-1,-1}};
        boolean[][] visited = new boolean[n][n];

        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{0, 0, 1}); // {x, y, distance}
        visited[0][0] = true;

        while (!queue.isEmpty()) {
            int[] cell = queue.poll();
            int x = cell[0], y = cell[1], dist = cell[2];

            if (x == n - 1 && y == n - 1) return dist;

            for (int[] dir : directions) {
                int nx = x + dir[0], ny = y + dir[1];
                if (nx >= 0 && ny >= 0 && nx < n && ny < n && !visited[nx][ny] && grid[nx][ny] == 0) {
                    visited[nx][ny] = true;
                    queue.offer(new int[]{nx, ny, dist + 1});
                }
            }
        }
        return -1; // No path found
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** `O(n^2)` because each cell is processed at most once.
- **Space Complexity:** `O(n^2)` for the visited array and queue.

---

## **Summary Table**

| Approach | Data Structure | Time Complexity | Space Complexity | Notes |
|------------|-----------------|---------------------|-----------------------|--------|
| DFS (Brute Force) | Recursion + Visited | Exponential | `O(n^2)` | Not feasible for large grids |
| BFS | Queue + Visited Array | `O(n^2)` | `O(n^2)` | Most efficient for shortest path in grid |

---

## **Final Tips:**
- Use **BFS** for shortest path in unweighted grids, especially with uniform costs.
- Remember to check for blocked start/end cells early.
- Explore all 8 directions to cover diagonal moves.
- BFS ensures the shortest path due to level-order traversal.

This guide provides a clear and comprehensive overview of solving **Shortest Path in Binary Matrix**, suitable for interview prep and quick revision.
