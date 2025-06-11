# N Queens II

The **N Queens II** problem asks: *Given an integer `n`, return the total number of distinct solutions to the n-queens puzzle.* The goal is to place `n` queens on an `n x n` chessboard such that no two queens threaten each other.

---

## Approach 1: Brute Force (Backtracking with Conflict Checking)

### 1. Core Idea:
- Generate all possible arrangements of placing `n` queens on the board.
- Check for conflicts (same row, column, or diagonals).
- Count solutions that are valid.

### 2. Algorithm:
- Use recursion to place queens row by row.
- For each row, try placing a queen in each column.
- Before placing, check if the position is safe:
  - No other queens in the same column.
  - No other queens on the same diagonals.
- If safe, recurse to the next row.
- Backtrack if no valid position is found in the current row.

### 3. Recursion Tree:
```
Level 0: Place queen in row 0 (try all columns)
Level 1: Place queen in row 1 (try all columns safe from previous)
...
Level n: All queens placed -> count solution
```

### 4. Java Code:
```java
public class NQueensII {
    int count = 0;

    public int totalNQueens(int n) {
        // Arrays to track columns and diagonals
        boolean[] cols = new boolean[n];
        boolean[] diag1 = new boolean[2 * n - 1]; // (row + col)
        boolean[] diag2 = new boolean[2 * n - 1]; // (row - col + n - 1)
        backtrack(0, n, cols, diag1, diag2);
        return count;
    }

    private void backtrack(int row, int n, boolean[] cols, boolean[] diag1, boolean[] diag2) {
        if (row == n) {
            count++;
            return;
        }
        for (int col = 0; col < n; col++) {
            if (cols[col] || diag1[row + col] || diag2[row - col + n - 1]) continue;

            // Place queen
            cols[col] = true;
            diag1[row + col] = true;
            diag2[row - col + n - 1] = true;

            backtrack(row + 1, n, cols, diag1, diag2);

            // Remove queen (backtrack)
            cols[col] = false;
            diag1[row + col] = false;
            diag2[row - col + n - 1] = false;
        }
    }
}
```

### 5. Complexity Analysis:
- **Time Complexity:** O(n!), because in the worst case, each row tries to place a queen in any column, pruning as it goes.
- **Space Complexity:** O(n) for the recursion stack and additional arrays.

### 6. Dry Run (for n=4):
| Step | Row | Current Board State | Conflict Check | Action |
|-------|-------|---------------------|----------------|---------|
| 1     | 0     | Place at (0,0)      | Safe           | Place queen, recurse |
| 2     | 1     | Try columns 0..3    | Column 0 blocked, diagonals blocked | Place at (1,1) | ... (continue recursively) |
| ...   | ...   | ...                 | ...            | ...     |
| Final | n     | All queens placed   | Valid solutions | Count incremented |

---

## Approach 2: Optimized Solution Using Hashing (Bit Manipulation)

### 1. Core Idea:
- Use bitmasks to efficiently track columns and diagonals.
- Bitwise operations speed up conflict checks.
- Each bit in an integer represents a column/diagonal's occupation status.

### 2. Algorithm:
- Maintain three integers:
  - `columns`: bits set for occupied columns.
  - `diag1`: bits for occupied "main" diagonals.
  - `diag2`: bits for occupied "anti" diagonals.
- At each row, compute available positions as:
  ``` 
  available_positions = (~(columns | diag1 | diag2)) & ((1 << n) - 1)
  ```
- Iterate over all available positions:
  - Pick the rightmost set bit (`pos = available_positions & -available_positions`).
  - Place the queen, update masks, recurse.
  - Remove the placed position and continue.

### 3. Java Code:
```java
public class NQueensII {
    private int count = 0;

    public int totalNQueens(int n) {
        solve(n, 0, 0, 0, 0);
        return count;
    }

    private void solve(int n, int row, int columns, int diag1, int diag2) {
        if (row == n) {
            count++;
            return;
        }
        int availablePositions = ((1 << n) - 1) & ~(columns | diag1 | diag2);
        while (availablePositions != 0) {
            int position = availablePositions & -availablePositions; // rightmost set bit
            availablePositions -= position; // remove the position
            solve(n, row + 1,
                  columns | position,
                  (diag1 | position) << 1,
                  (diag2 | position) >> 1);
        }
    }
}
```

### 4. Complexity Analysis:
- **Time Complexity:** O(n!), similar to previous, but faster in practice due to bitwise operations.
- **Space Complexity:** O(n) for recursion and constant extra space for bitmasks.

### 5. Dry Run (for n=4):
| Step | Row | Available Positions (binary) | Chosen Position | Updated Masks | Recurse |
|-------|-------|------------------------------|-----------------|----------------|---------|
| 1     | 0     | 1111                         | 0001            | columns=0001, diag1=0010, diag2=0100 | recurse |
| 2     | 1     | Compute available positions | 0010            | ...            | recurse |
| ...   | ...   | ...                          | ...             | ...            | ...     |
| Final | n     | All queens placed            | Solution found  | count++        |         |

---

## Summary Table: Approaches Comparison

| Approach | Core Idea                                | Data Structures                   | Time Complexity | Space Complexity |
|------------|------------------------------------------|-----------------------------------|-------------------|------------------|
| Brute Force | Generate all arrangements, check conflicts | Arrays for columns/diagonals     | O(n!)             | O(n)             |
| Bitmasking | Use bits for conflict detection, backtracking | Bitmasks for columns/diagonals   | O(n!) in practice | O(n)             |

---

## Final Tips:
- Use **bit manipulation** for faster solutions, especially suitable for larger `n`.
- Always prune conflicts early to reduce the search space.
- Understand the problem's symmetry and constraints to optimize further if needed.

---

This guide provides a comprehensive overview for understanding, implementing, and optimizing solutions to the **N Queens II** problem. Happy coding!
