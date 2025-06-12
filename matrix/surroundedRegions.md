# Surrounded Regions (DFS/BFS)

This problem involves modifying a 2D grid of `'X'` and `'O'` characters such that all `'O'`s **completely surrounded** by `'X'`s are flipped to `'X'`. The `'O'`s connected to the border are **not** flipped, as they are not surrounded.

---

## Problem Statement

Given an `m x n` grid, capture all regions surrounded by `'X'` by flipping all `'O'`s that are fully enclosed by `'X'`.

---

## Approach 1: Brute Force (Naive)

### **Algorithm:**

- For each `'O'` in the grid:
    - Check if this `'O'` is surrounded by `'X'`s:
        - Use DFS/BFS to explore all `'O'`s connected to it.
        - If during exploration, you reach the border, then this `'O'` is not surrounded.
    - If surrounded, flip all `'O'`s in that region to `'X'`.
- **Issue:** This approach results in repeated exploration for each `'O'`, leading to high time complexity.

### **Java Code:**

```java
public class SurroundedRegions {
    private int rows, cols;

    public void solve(char[][] board) {
        int m = board.length;
        int n = board[0].length;
        boolean[][] visited;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O' && !visited[i][j]) {
                    List<int[]> region = new ArrayList<>();
                    boolean[] isBorderConnected = {false};
                    dfs(board, i, j, region, isBorderConnected);
                    if (!isBorderConnected[0]) {
                        for (int[] cell : region) {
                            board[cell[0]][cell[1]] = 'X';
                        }
                    }
                }
            }
        }
    }

    private void dfs(char[][] board, int i, int j, List<int[]> region, boolean[] isBorderConnected) {
        if (i < 0 || j < 0 || i >= board.length || j >= board[0].length) return;
        if (board[i][j] == 'X') return;
        // Mark visited
        // (Assuming a global visited array or a different approach)
        // For brevity, omitted here

        region.add(new int[]{i, j});
        if (i == 0 || j == 0 || i == board.length - 1 || j == board[0].length - 1) {
            isBorderConnected[0] = true;
        }

        dfs(board, i + 1, j, region, isBorderConnected);
        dfs(board, i - 1, j, region, isBorderConnected);
        dfs(board, i, j + 1, region, isBorderConnected);
        dfs(board, i, j - 1, region, isBorderConnected);
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:** *O((m * n) * (m * n))* in the worst case because each `'O'` might be explored multiple times.
- **Space Complexity:** *O(m * n)* for recursion stack and auxiliary data structures.

---

## Approach 2: Better Solution – Mark Border-Connected `'O'`s

### **Algorithm:**

1. **Identify `'O'`s on the borders** (first row, last row, first column, last column).
2. **Perform DFS/BFS** from each border `'O'` to mark all `'O'`s connected to the border as **safe** (e.g., temporarily mark as `'S'`).
3. **Iterate through the grid**:
    - Flip all remaining `'O'`s (which are not marked safe) to `'X'`.
    - Convert all `'S'` back to `'O'`.

**Intuition:** Only `'O'`s connected to the border are safe; others are surrounded.

### **Java Code:**

```java
public class SurroundedRegions {
    private int rows, cols;

    public void solve(char[][] board) {
        if (board == null || board.length == 0) return;
        int m = board.length, n = board[0].length;

        // Mark border-connected 'O's
        for (int i = 0; i < m; i++) {
            if (board[i][0] == 'O') dfs(board, i, 0);
            if (board[i][n - 1] == 'O') dfs(board, i, n - 1);
        }
        for (int j = 0; j < n; j++) {
            if (board[0][j] == 'O') dfs(board, 0, j);
            if (board[m - 1][j] == 'O') dfs(board, m - 1, j);
        }

        // Flip surrounded 'O's to 'X' and revert safe 'O's
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (board[i][j] == 'O') {
                    board[i][j] = 'X'; // surrounded
                } else if (board[i][j] == 'S') {
                    board[i][j] = 'O'; // safe
                }
            }
        }
    }

    private void dfs(char[][] board, int i, int j) {
        if (i < 0 || j < 0 || i >= board.length || j >= board[0].length) return;
        if (board[i][j] != 'O') return;

        board[i][j] = 'S'; // mark as safe

        dfs(board, i + 1, j);
        dfs(board, i - 1, j);
        dfs(board, i, j + 1);
        dfs(board, i, j - 1);
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:** *O(m * n)* because each cell is visited at most once during DFS.
- **Space Complexity:** *O(m * n)* for recursion stack in the worst case.

---

## Approach 3: Most Optimal – BFS + DFS (Union-Find is not necessary here)

This approach is similar to the second, but uses BFS for border traversal, which can be more iterative and may avoid recursion stack issues.

### **Algorithm:**

- Use BFS from each border `'O'` to mark all connected `'O'`s as safe.
- Flip all other `'O'`s to `'X'`.
- Revert safe `'O'`s back if marked temporarily.

---

## **Dry Run Example**

### Input:

```
[
  ['X', 'X', 'X', 'X'],
  ['X', 'O', 'O', 'X'],
  ['X', 'X', 'O', 'X'],
  ['X', 'O', 'X', 'X']
]

```

### Process:

- Border `'O'`s are at `(3,1)`.
- BFS/DFS from `(3,1)`:
    - Mark `(3,1)` as safe `'S'`.
    - No other `'O'` connected to `(3,1)` on the border.
- Remaining `'O'`s at `(1,1)`, `(1,2)`, `(2,2)` are surrounded.
- Flip these to `'X'`.
- Convert `'S'` back to `'O'`.

### Final grid:

```
[
  ['X', 'X', 'X', 'X'],
  ['X', 'X', 'X', 'X'],
  ['X', 'X', 'X', 'X'],
  ['X', 'O', 'X', 'X']
]

```

---

## **Summary**

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | Explore each `'O'` repeatedly; check if surrounded | O((m * n)^2) | O(m * n) | Inefficient, only for understanding |
| Better Solution | Mark border-connected `'O'`s, flip others | O(m * n) | O(m * n) | Efficient and standard approach |
| Optimized BFS/DFS | Use BFS instead of recursion; same logic | O(m * n) | O(m * n) | Preferred in large grids |

---

## **Final Tips for Interview**

- Recognize the importance of border-connected `'O'`s.
- Use DFS or BFS to mark safe `'O'`s.
- Remember to revert temporary markings after processing.
- Optimize by avoiding repeated exploration of the same `'O'`.

---

This comprehensive guide should help in quick revision and understanding of the **Surrounded Regions** problem using DFS/BFS strategies.
