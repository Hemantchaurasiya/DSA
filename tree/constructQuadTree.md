# Construct Quad Tree

This guide provides a comprehensive overview of solving the **Construct Quad Tree** problem, progressing from naive to optimal solutions. It is designed for quick revision and interview preparation.

---

## Problem Overview
Given a 2D grid (matrix) of 0s and 1s, **construct a Quad Tree** that represents the grid. The Quad Tree divides the grid into four quadrants recursively until each region is uniform (all 0s or all 1s).

---

## Approach 1: Brute Force (Naive)

### Core Idea
- Check each cell individually.
- Build the Quad Tree by dividing the grid into quadrants recursively.
- For each quadrant, check if all values are the same; if yes, create a leaf node, otherwise subdivide further.

### Algorithm
1. **Define a recursive function** that takes the current sub-grid boundaries.
2. **Check if the current sub-grid is uniform** (all 0s or all 1s).
3. If uniform, **return a leaf node** with value.
4. Otherwise, **divide the grid into four quadrants**:
   - Top-left
   - Top-right
   - Bottom-left
   - Bottom-right
5. Recursively construct the Quad Tree for each quadrant.
6. **Combine the four quadrants** into a parent node and return.

### Java Code
```java
class Node {
    public boolean val;
    public boolean isLeaf;
    public Node topLeft;
    public Node topRight;
    public Node bottomLeft;
    public Node bottomRight;

    public Node(boolean val, boolean isLeaf) {
        this.val = val;
        this.isLeaf = isLeaf;
    }
}

public class Solution {
    public Node construct(int[][] grid) {
        return build(grid, 0, 0, grid.length);
    }

    private Node build(int[][] grid, int row, int col, int size) {
        if (isUniform(grid, row, col, size)) {
            return new Node(grid[row][col] == 1, true);
        }

        int half = size / 2;
        Node topLeft = build(grid, row, col, half);
        Node topRight = build(grid, row, col + half, half);
        Node bottomLeft = build(grid, row + half, col, half);
        Node bottomRight = build(grid, row + half, col + half, half);

        Node node = new Node(true, false);
        node.topLeft = topLeft;
        node.topRight = topRight;
        node.bottomLeft = bottomLeft;
        node.bottomRight = bottomRight;

        return node;
    }

    private boolean isUniform(int[][] grid, int row, int col, int size) {
        int firstVal = grid[row][col];
        for (int i = row; i < row + size; i++) {
            for (int j = col; j < col + size; j++) {
                if (grid[i][j] != firstVal) {
                    return false;
                }
            }
        }
        return true;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(N^2 * log N)**  
  - Each recursive call checks all cells in the current sub-grid (up to N^2).  
  - The depth of recursion is **O(log N)** because the grid is divided into quadrants at each step.

- **Space Complexity:**  
  **O(N^2)** for the recursive stack and the input storage, **plus** the space for the tree nodes.

---

## Approach 2: Optimized Check with Prefix Sum (Improvement)

### Core Idea
- Use prefix sums to quickly determine if a sub-grid is uniform.
- Reduce the time spent on uniformity checks from O(N^2) to O(1).

### Algorithm
1. **Precompute prefix sums** for the grid.
2. For each recursive subdivision:
   - Use prefix sums to check if the sum of elements in the current sub-grid equals **size * firstVal** (either 0 or size*1).
   - If equal, the sub-grid is uniform.
3. Proceed as in the naive approach, but with **O(1)** uniformity checks.

### Java Code
```java
public class Solution {
    int[][] prefixSum;

    public Node construct(int[][] grid) {
        int n = grid.length;
        prefixSum = new int[n + 1][n + 1];
        buildPrefixSum(grid);
        return build(grid, 0, 0, n);
    }

    private void buildPrefixSum(int[][] grid) {
        int n = grid.length;
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                prefixSum[i][j] = grid[i - 1][j - 1]
                                  + prefixSum[i - 1][j]
                                  + prefixSum[i][j - 1]
                                  - prefixSum[i - 1][j - 1];
            }
        }
    }

    private boolean isUniform(int row, int col, int size) {
        int total = prefixSum[row + size][col + size]
                  - prefixSum[row][col + size]
                  - prefixSum[row + size][col]
                  + prefixSum[row][col];

        if (total == 0) return true; // all zeros
        if (total == size * size) return true; // all ones
        return false;
    }

    private Node build(int[][] grid, int row, int col, int size) {
        if (isUniform(row, col, size))
            return new Node(grid[row][col] == 1, true);

        int half = size / 2;
        Node topLeft = build(grid, row, col, half);
        Node topRight = build(grid, row, col + half, half);
        Node bottomLeft = build(grid, row + half, col, half);
        Node bottomRight = build(grid, row + half, col + half, half);

        Node node = new Node(true, false);
        node.topLeft = topLeft;
        node.topRight = topRight;
        node.bottomLeft = bottomLeft;
        node.bottomRight = bottomRight;

        return node;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(N^2)** for prefix sum computation + **O(N^2 * log N)** for recursion.  
  - Uniformity checks are **O(1)** due to prefix sums, so the recursive part is reduced significantly.

- **Space Complexity:**  
  **O(N^2)** for prefix sums + **O(N^2)** for the tree nodes in worst case.

---

## Approach 3: Most Optimal Solution (Divide and Conquer with Memoization)

### Core Idea
- Use divide and conquer with memoization for repeated sub-grid checks.
- Reuse results of sub-problems to avoid recomputation.
- This approach is more theoretical for large grids but can optimize in certain scenarios.

### Algorithm
1. **Implement divide and conquer** similar to previous approaches.
2. **Memoize** results for sub-grids (by hash or coordinates) to avoid recomputation.
3. Proceed recursively, checking if sub-grids are uniform.
4. Construct the Quad Tree accordingly.

*(Note: Practical benefit is context-dependent; often, prefix sum optimization suffices.)*

---

# Summary
| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | Check each sub-grid naively | O(N^2 * log N) | O(N^2) | Simple but slow for large grids |
| Prefix Sum Optimization | Use prefix sums for quick uniformity check | O(N^2) + O(N^2 * log N) | O(N^2) | Efficient uniformity checks |
| Memoization / Divide & Conquer | Reuse sub-results | Varies | Varies | Advanced; context-dependent |

---

## Final Tips
- Always check for uniformity efficiently.
- Use recursion to divide the grid into quadrants.
- Construct leaf nodes when regions are uniform.
- Combine sub-quadrants into parent nodes.
- Optimize uniformity checks with prefix sums for large inputs.

---

This completes your **Construct Quad Tree** revision note. Use it to understand the progression from naive to optimal solutions, and prepare effectively for interviews.
