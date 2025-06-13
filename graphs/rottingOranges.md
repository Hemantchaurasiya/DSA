# Rotting Oranges

---

## Problem Overview
Given a 2D grid representing oranges:
- `0` represents an empty cell,
- `1` represents a fresh orange,
- `2` represents a rotten orange.

Every minute, any fresh orange that is adjacent (4-directionally: up, down, left, right) to a rotten orange becomes rotten. The goal is to determine the minimum number of minutes that must elapse until no fresh oranges remain. If impossible, return `-1`.

---

## Approach 1: Brute Force (Repeated Scanning)

### Core Idea
Repeatedly scan the entire grid to find rotten oranges, and then rot adjacent fresh oranges. Repeat until no fresh oranges are left or no new oranges rot in a pass.

### Algorithm
1. Initialize a counter for minutes (`time = 0`).
2. Loop until no fresh oranges remain:
   - For each cell, if it is rotten, check its neighbors.
   - If a neighbor is fresh, mark it to rot in this iteration.
   - After scanning the entire grid, update all marked fresh oranges to rotten.
   - If no fresh orange turned rotten in this iteration, break out (no more progress).
   - Increment `time`.
3. After the loop, check if any fresh oranges remain:
   - If yes, return `-1`.
   - Else, return `time`.

### Java Code
```java
public int orangesRotting(int[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    int time = 0;
    boolean changed;

    do {
        changed = false;
        int[][] temp = new int[rows][cols];

        // Copy current grid to temp
        for (int i = 0; i < rows; i++) {
            temp[i] = grid[i].clone();
        }

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    // Check neighbors
                    if (i > 0 && grid[i - 1][j] == 1) {
                        temp[i - 1][j] = 2;
                        changed = true;
                    }
                    if (i < rows - 1 && grid[i + 1][j] == 1) {
                        temp[i + 1][j] = 2;
                        changed = true;
                    }
                    if (j > 0 && grid[i][j - 1] == 1) {
                        temp[i][j - 1] = 2;
                        changed = true;
                    }
                    if (j < cols - 1 && grid[i][j + 1] == 1) {
                        temp[i][j + 1] = 2;
                        changed = true;
                    }
                }
            }
        }

        grid = temp;
        if (changed) time++;
    } while (changed && anyFresh(grid));

    return anyFresh(grid) ? -1 : time;
}

private boolean anyFresh(int[][] grid) {
    for (int[] row : grid)
        for (int cell : row)
            if (cell == 1)
                return true;
    return false;
}
```

### Complexity Analysis
- **Time Complexity:**  
  - Worst case: O((N*M)^2) due to repeated full scans until no change.  
  - Each iteration involves scanning the entire grid (O(N*M)), and potentially up to O(N*M) iterations in the worst case (all oranges rot sequentially).

- **Space Complexity:**  
  - O(N*M) for the temporary grid used in each iteration.

### Dry Run
**Input:**
```
grid = [
  [2,1,1],
  [1,1,0],
  [0,1,1]
]
```

| Step | Rotten Oranges | Fresh Oranges | Changes | Minutes |
|-------|----------------|----------------|---------|---------|
| 0     | (0,0) rotten | (0,1), (0,2), (1,0), (1,1), (2,1), (2,2) | Rotting neighbors of initial rotten | 0 |
| 1     | Rotting neighbors of (0,0): (0,1), (1,0) | Remaining fresh (0,2), (1,1), (2,1), (2,2) | 2 oranges rot | 1 |
| 2     | Rotting neighbors of newly rotten | Remaining fresh (0,2), (2,1), (2,2) | 2 oranges rot | 2 |
| 3     | No new oranges rot | All oranges rotten | Stop | 2 |

---

## Approach 2: BFS (Breadth-First Search) - Optimal Solution

### Core Idea
Use a **multi-source BFS**:
- Initialize a queue with all initially rotten oranges.
- Each iteration (minute), process all rotten oranges in the queue, rotting their neighbors.
- Enqueue newly rotten oranges for the next minute.
- Continue until no fresh oranges remain or no new oranges rot in an iteration.

### Algorithm
1. Traverse the grid, enqueue all initially rotten oranges, and count total fresh oranges.
2. Initialize `minutes = 0`.
3. While the queue is not empty:
   - Process all nodes at the current level (all rotten oranges in queue).
   - For each rotten orange, rot its adjacent fresh oranges.
   - Enqueue newly rotten oranges.
   - Increment `minutes`.
4. After BFS completes:
   - If any fresh oranges remain, return `-1`.
   - Else, return `minutes`.

### Java Code
```java
import java.util.LinkedList;
import java.util.Queue;

public int orangesRotting(int[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    Queue<int[]> queue = new LinkedList<>();
    int freshCount = 0;

    // Initialize queue with all rotten oranges and count fresh oranges
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 2) {
                queue.offer(new int[]{i, j});
            } else if (grid[i][j] == 1) {
                freshCount++;
            }
        }
    }

    if (freshCount == 0) return 0; // No fresh oranges

    int minutes = 0;
    int[][] directions = {{-1,0},{1,0},{0,-1},{0,1}};

    // BFS traversal
    while (!queue.isEmpty() && freshCount > 0) {
        int size = queue.size();
        boolean rottedThisMinute = false;

        for (int i = 0; i < size; i++) {
            int[] point = queue.poll();
            for (int[] dir : directions) {
                int r = point[0] + dir[0];
                int c = point[1] + dir[1];

                if (r >= 0 && r < rows && c >= 0 && c < cols && grid[r][c] == 1) {
                    grid[r][c] = 2; // Rot the fresh orange
                    freshCount--;
                    queue.offer(new int[]{r, c});
                    rottedThisMinute = true;
                }
            }
        }

        if (rottedThisMinute) minutes++;
    }

    return freshCount == 0 ? minutes : -1;
}
```

### Complexity Analysis
- **Time Complexity:**  
  - O(N*M), where each cell is processed at most once.
- **Space Complexity:**  
  - O(N*M) for the queue in the worst case (all oranges are rotten initially).

### Dry Run
**Input:**
```
grid = [
  [2,1,1],
  [1,1,0],
  [0,1,1]
]
```

| Step | Queue (rotten oranges) | Fresh Count | Processed Oranges | Newly Rotten Oranges | Minutes |
|-------|------------------------|--------------|---------------------|----------------------|---------|
| Initial | (0,0) rotten | 5 | Enqueue (0,0) | - | 0 |
| 1     | Process (0,0): rot (0,1), (1,0) | 3 | Enqueue (0,1), (1,0) | 2 oranges rot | 1 |
| 2     | Process (0,1), (1,0): rot (0,2), (2,1) | 1 | Enqueue (0,2), (2,1) | 2 oranges rot | 2 |
| 3     | Process (0,2), (2,1): rot (2,2) | 0 | Enqueue (2,2) | 1 orange rot | 3 |
| 4     | Process (2,2): no new oranges | 0 | - | - | 3 |

**Result:** 3 minutes until all oranges rot.

---

## Summary
| Approach | Key Idea | Pros | Cons | Best Use Case |
|------------|--------------|-------|-------|--------------|
| Brute Force | Repeated full scan until no change | Simple to implement | Inefficient for large grids | Small input sizes, debugging |
| BFS (Optimal) | Multi-source BFS from rotten oranges | Efficient, optimal | Slightly more complex | Large grids, performance-critical |

---

## Final Tips
- Use BFS for optimal time complexity.
- Always count fresh oranges initially to determine if the process is necessary.
- Track the number of minutes via levels in BFS.
- Check for remaining fresh oranges after BFS completes to decide the answer.

---

Happy Revising!
