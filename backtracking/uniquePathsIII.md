# Unique Paths III

---

## Problem Overview
Given a grid with obstacles (cells with value `-1`), empty cells (`0`), a starting cell (`1`), and a destination cell (`2`), find **the number of unique paths** from start to end such that **all non-obstacle cells** are visited exactly once.

---

## Approaches to Solve the Problem

### 1. Brute Force (Backtracking with Visited Set)
---

#### **Core Idea**
Explore all possible paths from the start cell to the end cell, ensuring each cell is visited exactly once. Count only those paths covering all non-obstacle cells.

#### **Algorithm**
- Find the starting position and count total non-obstacle cells.
- Use DFS to explore all four directions.
- Maintain a visited matrix or set to avoid revisiting cells.
- When reaching the destination, check if all non-obstacle cells are visited.
- Backtrack after exploring each path.

#### **Recursion Tree Diagram**

```
Start
 ├─ Explore direction 1
 │    ├─ Explore further
 │    └─ Backtrack
 ├─ Explore direction 2
 │    └─ ...
 └─ Explore direction 3/4
```

*(Due to the recursive nature, the tree expands exponentially with the number of reachable cells)*

#### **Java Code**

```java
public class UniquePathsIII {
    int count = 0;
    int totalCells = 0;
    int rows, cols;
    int[][] grid;

    public int uniquePathsIII(int[][] grid) {
        this.grid = grid;
        rows = grid.length;
        cols = grid[0].length;
        int startX = 0, startY = 0;

        // Count total non-obstacle cells and locate start
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] != -1) {
                    totalCells++;
                }
                if (grid[i][j] == 1) {
                    startX = i;
                    startY = j;
                }
            }
        }

        boolean[][] visited = new boolean[rows][cols];
        dfs(startX, startY, 1, visited);
        return count;
    }

    private void dfs(int x, int y, int visitedCells, boolean[][] visited) {
        // Boundary and obstacle checks
        if (x < 0 || x >= rows || y < 0 || y >= cols || grid[x][y] == -1 || visited[x][y]) {
            return;
        }

        // If it's the end cell
        if (grid[x][y] == 2) {
            if (visitedCells == totalCells) {
                count++;
            }
            return;
        }

        // Mark current cell as visited
        visited[x][y] = true;

        // Explore four directions
        dfs(x + 1, y, visitedCells + 1, visited);
        dfs(x - 1, y, visitedCells + 1, visited);
        dfs(x, y + 1, visitedCells + 1, visited);
        dfs(x, y - 1, visitedCells + 1, visited);

        // Backtrack
        visited[x][y] = false;
    }
}
```

#### **Complexity Analysis**
- **Time Complexity:** *O(4^{N})*, where N is the number of non-obstacle cells, since each cell can explore up to 4 directions recursively.
- **Space Complexity:** *O(N)* for the recursion stack and the visited array.

#### **Dry Run Example**

Suppose input:

```
grid = [
  [1, 0, 0, 0],
  [0, -1, -1, 0],
  [0, 0, 2, 0]
]
```

- Count non-obstacle cells: 10
- Start at (0,0). Explore all paths recursively.
- Path exploration continues until reaching (2,2) with all cells visited.

---

### 2. Dynamic Programming with State Compression (Bitmasking)
---

#### **Core Idea**
Use bitmasking to represent visited cells, and memoization to avoid recomputation. This approach is more efficient for smaller grids.

#### **Algorithm**
- Assign each cell an index.
- Use a bitmask to represent visited cells.
- Use DFS + memoization: for each position and visited mask, store the number of paths.
- When reaching the destination with all cells visited, count paths.

*(Note: Due to the complexity of implementing in Java and grid size constraints, this approach is less common for larger grids but efficient for small ones.)*

---

### 3. Most Optimal Solution: Backtracking with Pruning (Straightforward)
*(This is similar to Approach 1 but emphasizes pruning and efficiency)*

---

## Final Notes
- **Backtracking** is the most intuitive and straightforward approach.
- **State compression with bitmasking** is more efficient but complex.
- Always pre-count total non-obstacle cells and ensure paths cover all.
- Use recursive DFS with backtracking to explore all paths.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Suitable for |
|------------|--------------|-------------------|------------------|--------------|
| Brute Force (Backtracking) | Explore all paths with visited set | O(4^N) | O(N) | Small grids | 
| DP + Bitmasking | Use state compression to memoize subproblems | O(2^N * N) | O(N * 2^N) | Very small grids | 

---

## Final Tips
- Always handle boundary conditions carefully.
- Count total cells before starting DFS.
- Mark and unmark cells during backtracking.
- Use early pruning when possible to reduce the search space.

---

This revision note provides a structured, detailed overview suitable for quick review and interview prep for **Unique Paths III**.
