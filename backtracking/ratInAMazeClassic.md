# Rat in a Maze

---

## Problem Statement
Given a maze represented by a 2D grid, where `1` indicates open paths and `0` indicates blocked cells, find all possible paths for a rat starting from the top-left cell `(0,0)` to the bottom-right cell `(n-1, n-1)`. The rat can move **up**, **down**, **left**, and **right**.

---

## Approach 1: Brute Force (Backtracking Without Pruning)

### Core Idea
Explore all possible paths from the start to the destination, trying all directions at each step, and backtrack if a path leads to dead ends.

### Algorithm
1. **Initialize** a visited matrix to keep track of visited cells.
2. **Start** from `(0,0)`:
   - Mark current cell as visited.
   - If current cell is destination `(n-1, n-1)`, record the path.
   - Recursively explore all four directions:
     - Up `(row-1, col)`
     - Down `(row+1, col)`
     - Left `(row, col-1)`
     - Right `(row, col+1)`
   - **Backtrack** by unmarking the current cell before returning.
3. **Collect** all valid paths.

### Recursive Tree Diagram (Conceptual)
```
Start (0,0)
|- Explore all directions
   |- If valid, recurse further
      |- Reach destination -> save path
      |- Backtrack
```

### Java Code
```java
import java.util.*;

public class RatInMaze {
    static int n;
    static List<String> paths = new ArrayList<>();
    static int[][] maze;
    static boolean[][] visited;

    static void solveMaze(int row, int col, String path) {
        // Base conditions
        if (row < 0 || col < 0 || row >= n || col >= n || maze[row][col] == 0 || visited[row][col]) {
            return;
        }

        // If destination is reached
        if (row == n - 1 && col == n - 1) {
            paths.add(path);
            return;
        }

        // Mark current cell as visited
        visited[row][col] = true;

        // Explore all four directions
        // Down
        solveMaze(row + 1, col, path + "D");
        // Left
        solveMaze(row, col - 1, path + "L");
        // Right
        solveMaze(row, col + 1, path + "R");
        // Up
        solveMaze(row - 1, col, path + "U");

        // Backtrack
        visited[row][col] = false;
    }

    public static void main(String[] args) {
        maze = new int[][]{
            {1, 0, 0, 0},
            {1, 1, 0, 1},
            {0, 1, 0, 0},
            {1, 1, 1, 1}
        };
        n = maze.length;
        visited = new boolean[n][n];
        solveMaze(0, 0, "");
        System.out.println("Paths: " + paths);
    }
}
```

### Time Complexity
- **O(4^(n^2))** in the worst case, since at each cell, there are 4 possible directions, and the exploration can go as deep as all cells.
- **Note:** Actual practical complexity is less due to pruning and maze constraints.

### Space Complexity
- **O(n^2)** for the recursion stack and visited matrix.

---

## Approach 2: Improved Backtracking with Pruning

### Core Idea
Reduce unnecessary exploration by:
- Checking boundaries and visited cells before proceeding.
- Pruning paths that hit dead ends early.

### Algorithm
Same as above but with added checks to immediately discard invalid moves, which is already incorporated in the brute-force approach.

### Complexity
Same as brute-force, but pruning can significantly reduce the search space in practice.

---

## Approach 3: Dynamic Programming / Memoization (Not typically used here)
While memoization isn't straightforward due to path tracking, some variants attempt to cache sub-solutions. However, for path enumeration, backtracking remains standard.

---

## Approach 4: BFS / Iterative Solutions (Not optimal here)
Since we need all possible paths, BFS isn't suitable for finding *all* paths efficiently; DFS/backtracking remains the best.

---

## **Summary of Approaches**

| Approach                 | Idea                                    | Efficiency                                              |
|--------------------------|-----------------------------------------|---------------------------------------------------------|
| Brute Force (Backtracking) | Explore all possible paths via recursion | Exponential, **O(4^(n^2))** in worst case             |
| Pruned Backtracking     | Same as above with early pruning        | Same worst-case complexity but faster in practice     |
| DP / Memoization        | Cache sub-solutions (not straightforward for all paths) | Not typically used for path enumeration               |
| BFS / Iterative       | Level-by-level exploration (not suitable for all paths) | Not ideal for enumerating all paths                  |

---

## **Dry Run Example**

### Input Maze:
```
1 0 0 0
1 1 0 1
0 1 0 0
1 1 1 1
```

### Step-by-step:
| Step | Current Position | Path so Far | Visited Matrix | Action                                   |
|-------|-------------------|--------------|----------------|------------------------------------------|
| 1     | (0,0)            | ""           | (0,0) visited | Explore neighbors: Down, Left, Right, Up |
| 2     | (1,0)            | "D"          | (0,0), (1,0) | Explore down, right, etc.               |
| 3     | (1,1)            | "DR"         | ...            | Continue exploring paths                |
| ...   | ...               | ...          | ...            | Continue until reaching (3,3) or dead end |

*(This illustrates recursive exploration with backtracking, updating the path string and visited matrix at each step.)*

---

## Final Notes
- Backtracking is the standard approach for solving the classic "Rat in a Maze" problem.
- Always mark visited cells to prevent cycles.
- Use pruning to optimize the search.
- Path collection can be stored as strings or lists of moves.
- For large mazes, optimize by pruning or using iterative methods.

---

**Happy coding!**
