# Minimum Cost to Make at Least One Valid Path in a Grid

---

## Problem Overview
Given a grid where each cell contains an initial direction (represented as an integer from 1 to 4) indicating a preferred move (right, left, down, up), you can change the direction of any cell at a cost of 1 per change. The goal is to find the **minimum total cost** needed to ensure there's **at least one valid path** from the top-left cell `(0,0)` to the bottom-right cell `(m-1, n-1)`.

---

## Approach 1: Brute Force (DFS with Path Checking)

### **Core Idea:**
Attempt all possible configurations by changing directions and check if a valid path exists; this is highly inefficient due to combinatorial explosion.

### **Algorithm:**
- Enumerate all possible directions in the grid.
- For each configuration:
  - Check if a valid path exists from `(0,0)` to `(m-1,n-1)` following the directions.
  - Keep track of the minimum number of changes needed.
- Since this is exponential, it's not feasible for large grids.

**Note:** Usually, this approach is impractical; it's here for completeness.

---

### **Complexity Analysis:**
- **Time Complexity:** Exponential, due to trying all configurations.
- **Space Complexity:** `O(m*n)` for recursion and state storage.

---

## Approach 2: 0-1 BFS (Optimized Pathfinding with Cost)

### **Core Idea:**
Use a 0-1 BFS to efficiently find the minimal cost path, considering changing directions as edges with cost 0 (if the current direction is correct) or 1 (if a change is needed). This technique is suitable because costs are only 0 or 1.

### **Algorithm:**
1. **Build a graph-like structure**:
   - Treat each cell as a node.
   - For each cell, possible moves are to neighboring cells.
2. **Use a deque (double-ended queue)**:
   - Start from `(0,0)` with cost 0.
   - For each neighbor:
     - If following the cell's current direction, the cost is 0; add to the front.
     - If changing direction, the cost is 1; add to the back.
3. **Process the deque**:
   - Update the minimum cost to reach each cell.
   - Continue until `(m-1, n-1)` is reached.
4. **Result**:
   - The minimum cost to reach `(m-1, n-1)` is the answer.

---

### **Java Code:**
```java
import java.util.*;

public class MinCostPath {
    public int minCost(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] cost = new int[m][n];
        for (int[] row : cost) Arrays.fill(row, Integer.MAX_VALUE);
        cost[0][0] = 0;

        Deque<int[]> deque = new ArrayDeque<>();
        deque.offer(new int[]{0, 0});

        // Directions: 1=Right, 2=Left, 3=Down, 4=Up
        int[][] directions = {{0,1},{0,-1},{1,0},{-1,0}};

        while (!deque.isEmpty()) {
            int[] cell = deque.pollFirst();
            int x = cell[0], y = cell[1];

            for (int dirIdx = 0; dirIdx < 4; dirIdx++) {
                int[] dir = directions[dirIdx];
                int nx = x + dir[0], ny = y + dir[1];
                if (nx >= 0 && ny >= 0 && nx < m && ny < n) {
                    int newCost = cost[x][y] + (grid[x][y] == dirIdx + 1 ? 0 : 1);
                    if (newCost < cost[nx][ny]) {
                        cost[nx][ny] = newCost;
                        if (grid[x][y] == dirIdx + 1) {
                            deque.offerFirst(new int[]{nx, ny});
                        } else {
                            deque.offerLast(new int[]{nx, ny});
                        }
                    }
                }
            }
        }
        return cost[m - 1][n - 1];
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** `O(m * n)` because each cell is processed at most once or twice due to the deque operations, similar to Dijkstra's with 0-1 weights.
- **Space Complexity:** `O(m * n)` for `cost` array and the queue.

---

## **Summary Table**

| Approach | Data Structure | Time Complexity | Space Complexity | Notes |
|------------|-----------------|---------------------|-----------------------|--------|
| Brute Force | DFS + Path Check | Exponential | `O(m*n)` | Impractical for large grids |
| 0-1 BFS | Deque + Cost Array | `O(m*n)` | `O(m*n)` | Efficient for grid pathfinding with costs 0/1 |

---

## **Final Tips:**
- Use **0-1 BFS** for minimal cost path problems involving edges with costs 0 or 1.
- Carefully model directions and how changing them impacts cost.
- For large grids, avoid brute force; prefer optimized pathfinding algorithms like 0-1 BFS or Dijkstra with small edge weights.

This guide provides a comprehensive understanding of solving **Minimum Cost to Make at Least One Valid Path in a Grid** efficiently for interview prep.
