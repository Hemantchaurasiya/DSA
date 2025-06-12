# Swim in Rising Water

This problem involves finding the minimum time `t` at which you can swim from the top-left to the bottom-right cell in a grid, where each cell's value indicates the water level at which it becomes passable. The goal is to determine the earliest time `t` such that there's a path from `(0,0)` to `(n-1, n-1)` moving only through cells with values ≤ `t`.

---

## Problem Definition:
Given an `n x n` grid where each cell's value represents the time when it becomes passable, find the minimum `t` such that you can reach the bottom-right cell `(n-1, n-1)` from the top-left `(0,0)` moving only through cells with values ≤ `t`.

---

## Approach 1: Brute Force (Check all times)

### **Core Idea:**
Check all possible times from the minimum to the maximum value in the grid, and for each time, verify if there's a path from start to end.

### **Algorithm:**
1. Identify the minimum and maximum values in the grid (`low`, `high`).
2. Use binary search between `low` and `high`:
   - For each mid value `mid`, check if there's a path from `(0,0)` to `(n-1,n-1)` moving only through cells with value ≤ `mid`.
3. Use BFS or DFS to verify the path at each `mid`.
4. Narrow down the search based on whether a path exists.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public int swimInWater(int[][] grid) {
        int n = grid.length;
        int low = Math.min(grid[0][0], grid[n-1][n-1]);
        int high = 0;
        for (int[] row : grid) {
            for (int val : row) {
                high = Math.max(high, val);
            }
        }

        while (low < high) {
            int mid = low + (high - low) / 2;
            if (canReach(grid, mid)) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        return low;
    }

    private boolean canReach(int[][] grid, int maxTime) {
        int n = grid.length;
        boolean[][] visited = new boolean[n][n];
        Queue<int[]> queue = new LinkedList<>();
        if (grid[0][0] > maxTime) return false;
        queue.offer(new int[]{0, 0});
        visited[0][0] = true;

        int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};
        while (!queue.isEmpty()) {
            int[] cell = queue.poll();
            int r = cell[0], c = cell[1];
            if (r == n - 1 && c == n - 1) return true;

            for (int[] dir : directions) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && !visited[nr][nc] && grid[nr][nc] <= maxTime) {
                    visited[nr][nc] = true;
                    queue.offer(new int[]{nr, nc});
                }
            }
        }
        return false;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(N^2 log(maxVal - minVal))`, where `maxVal` and `minVal` are the maximum and minimum grid values. We perform binary search (`log(maxVal - minVal)`) and BFS (`O(N^2)`).
- **Space Complexity:** `O(N^2)` for the visited array and BFS queue.

### **Dry Run:**
Suppose:
```plaintext
grid = [[0,2],
        [1,3]], min=0, max=3
```
- Binary search:
  - mid=1: Check path with max time=1:
    - Path: `(0,0) -> (1,0) -> (1,1)`?  
    - `(0,0)=0`, `(1,0)=1`, `(1,1)=3` -> Not passable at 1, so no.
  - mid=2: Check max time=2:
    - Path possible? Yes, `(0,0)=0`, `(1,0)=1`, `(1,1)=3`? No, `(1,1)=3` > 2, so no.
  - mid=3: Check max time=3:
    - Path: `(0,0)=0`, `(0,1)=2`, `(1,1)=3` -> Yes, path exists.
- Result: 3.

---

## Approach 2: Min-Heap / Dijkstra-like (Most Optimal)

### **Core Idea:**
Use a priority queue to explore cells with the least elevation first, similar to Dijkstra's algorithm. This approach ensures we find the minimum possible `t` to reach `(n-1, n-1)`.

### **Algorithm:**
1. Initialize a min-heap (priority queue) with starting cell `(0,0)` and its height.
2. Maintain a `visited` array.
3. Poll the cell with the lowest height:
   - If it's `(n-1, n-1)`, return the height.
   - Explore neighbors:
     - If not visited, add neighbor with its height to the heap.
4. Continue until destination is reached.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public int swimInWater(int[][] grid) {
        int n = grid.length;
        boolean[][] visited = new boolean[n][n];
        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[2]));
        pq.offer(new int[]{0, 0, grid[0][0]});
        int result = 0;

        int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};

        while (!pq.isEmpty()) {
            int[] cell = pq.poll();
            int r = cell[0], c = cell[1], height = cell[2];
            if (visited[r][c]) continue;
            visited[r][c] = true;
            result = Math.max(result, height);
            if (r == n - 1 && c == n - 1) return result;

            for (int[] dir : directions) {
                int nr = r + dir[0], nc = c + dir[1];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && !visited[nr][nc]) {
                    pq.offer(new int[]{nr, nc, grid[nr][nc]});
                }
            }
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(N^2 log N^2) = O(N^2 log N)` due to the heap operations for all cells.
- **Space Complexity:** `O(N^2)` for the visited array and priority queue.

### **Dry Run:**
Suppose:
```plaintext
grid = [[0,2],
        [1,3]]
```
- Start at `(0,0)` with height=0, max=0.
- Explore neighbors:
  - `(0,1)` with height=2
  - `(1,0)` with height=1
- The heap orders cells by height:
  - `(1,0,1)`, `(0,1,2)`
- Poll `(1,0,1)`:
  - Check neighbors:
    - `(1,1)` with height=3
  - Max height so far: max(0,1)=1
- Poll `(0,1,2)`:
  - Check neighbors:
    - `(1,1)` with height=3
  - Max height: max(1,2)=2
- Poll `(1,1,3)`:
  - Reached destination, return max height=3.

---

## **Summary Table**

| Approach | Best For | Time Complexity | Space Complexity | Notes |
|--------------|------------|------------------|------------------|--------|
| Binary Search + BFS | Small to medium grids | `O(N^2 log(maxVal))` | `O(N^2)` | Checks feasibility at each mid |
| Dijkstra-like Min-Heap | Large grids, optimal | `O(N^2 log N)` | `O(N^2)` | Finds minimal maximum elevation efficiently |

---

## **Final Tips:**
- Use **binary search + BFS** for smaller constraints or when maximum and minimum values are manageable.
- Use **Dijkstra's algorithm** with a min-heap for larger or more complex grids for efficiency.
- Always keep track of visited cells to avoid re-processing.
- Think of the problem as a path with a "cost" (minimum maximum height) rather than sum of weights.

---

**Happy coding!**
