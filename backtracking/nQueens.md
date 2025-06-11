# N Queens

The N Queens problem involves placing **N** chess queens on an **N x N** chessboard so that no two queens threaten each other. The goal is to find all valid arrangements.

---

## 1. Brute Force Approach

### **Core Idea:**  
Generate all possible arrangements of N queens on the board and check for each if they are safe.

### **Algorithm:**

1. Generate all permutations of placing queens in different columns (since only one queen per column is allowed).
2. For each permutation, check if any two queens threaten each other diagonally or vertically.
3. Collect all arrangements that are valid.

### **Recursion Tree Diagram:**  
This approach tries **all permutations** of positions, leading to **N!** possibilities.

```
Level 0: Place queen in column 0 (row 0..N-1)
Level 1: Place queen in column 1 (row 0..N-1)
...
Level N-1: Place queen in column N-1 (row 0..N-1)
```

At each level, check if the placement is safe; if not, backtrack.

---

### **Java Code:**

```java
import java.util.ArrayList;
import java.util.List;

public class NQueensBruteForce {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> solutions = new ArrayList<>();
        int[] positions = new int[n]; // positions[col] = row where queen is placed
        backtrack(0, positions, solutions, n);
        return solutions;
    }

    private void backtrack(int col, int[] positions, List<List<String>> solutions, int n) {
        if (col == n) {
            solutions.add(createBoard(positions, n));
            return;
        }
        for (int row = 0; row < n; row++) {
            if (isSafe(row, col, positions)) {
                positions[col] = row;
                backtrack(col + 1, positions, solutions, n);
            }
        }
    }

    private boolean isSafe(int row, int col, int[] positions) {
        for (int prevCol = 0; prevCol < col; prevCol++) {
            int prevRow = positions[prevCol];
            // Check same row or diagonals
            if (prevRow == row || Math.abs(prevRow - row) == Math.abs(prevCol - col))
                return false;
        }
        return true;
    }

    private List<String> createBoard(int[] positions, int n) {
        List<String> board = new ArrayList<>();
        for (int row = 0; row < n; row++) {
            StringBuilder sb = new StringBuilder();
            for (int col = 0; col < n; col++) {
                if (positions[col] == row) sb.append('Q');
                else sb.append('.');
            }
            board.add(sb.toString());
        }
        return board;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
O(N!) — The algorithm generates all permutations of placing queens and checks each for conflicts.

- **Space Complexity:**  
O(N) — For the recursion stack and positions array.

---

### **Dry Run (Sample Input: N=4):**

| Step | Current Column | Positions Array | Validity Check | Action |
|-------|------------------|-------------------|----------------|---------|
| 0     | 0                | [0, _, _, _]     | Safe           | Place queen at row 0 in col 0 |
| 1     | 1                | [0, 0, _, _]     | Not safe (same row) | Backtrack, try next row |
| 1     | 1                | [0, 1, _, _]     | Safe           | Place queen at row 1 in col 1 |
| 2     | 2                | [0, 1, 0, _]     | Not safe (diagonal) | Backtrack |
| 2     | 2                | [0, 1, 2, _]     | Not safe (diagonal) | Backtrack |
| 2     | 2                | [0, 1, 3, _]     | Safe           | Continue...
| ...   | ...              | ...               | ...            | ...

This process continues until all solutions are generated.

---

## 2. Better Solution (Backtracking with Safety Checks)

### **Core Idea:**  
Use backtracking with **efficient safety checks** to prune invalid placements early, avoiding exploring invalid states.

### **Algorithm:**

1. Use three boolean arrays to track **columns**, **diagonals**, and **anti-diagonals** that are under attack:
   - `cols[n]` — whether a column is occupied.
   - `diag[2*n - 1]` — for diagonals.
   - `antiDiag[2*n - 1]` — for anti-diagonals.
2. Place a queen if the column, diagonal, and anti-diagonal are free.
3. Mark the attacked paths and recurse.
4. Backtrack by unmarking.

### **Recursion Tree Diagram:**

```
At each row, try placing a queen in safe columns.
Prune paths where columns/diagonals are occupied.
```

---

### **Java Code:**

```java
public class NQueensOptimized {
    public List<List<String>> solveNQueens(int n) {
        List<List<String>> solutions = new ArrayList<>();
        boolean[] cols = new boolean[n];
        boolean[] diag = new boolean[2 * n - 1];       // "/" diagonals
        boolean[] antiDiag = new boolean[2 * n - 1];   // "\" diagonals
        int[] positions = new int[n];

        backtrack(0, n, positions, solutions, cols, diag, antiDiag);
        return solutions;
    }

    private void backtrack(int row, int n, int[] positions, List<List<String>> solutions,
                           boolean[] cols, boolean[] diag, boolean[] antiDiag) {
        if (row == n) {
            solutions.add(createBoard(positions, n));
            return;
        }

        for (int col = 0; col < n; col++) {
            int d = row - col + n - 1;
            int ad = row + col;
            if (!cols[col] && !diag[d] && !antiDiag[ad]) {
                positions[row] = col;
                cols[col] = true;
                diag[d] = true;
                antiDiag[ad] = true;

                backtrack(row + 1, n, positions, solutions, cols, diag, antiDiag);

                // Backtrack
                cols[col] = false;
                diag[d] = false;
                antiDiag[ad] = false;
            }
        }
    }

    private List<String> createBoard(int[] positions, int n) {
        List<String> board = new ArrayList<>();
        for (int row = 0; row < n; row++) {
            StringBuilder sb = new StringBuilder();
            for (int col = 0; col < n; col++) {
                if (positions[row] == col)
                    sb.append('Q');
                else
                    sb.append('.');
            }
            board.add(sb.toString());
        }
        return board;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
O(N!), but significantly optimized due to constraints checking, pruning invalid paths early.

- **Space Complexity:**  
O(N) for recursion stack and auxiliary arrays.

---

### **Dry Run (Sample Input: N=4):**

| Step | Row | Cols[] | Diag[] | AntiDiag[] | Actions | Resulting Positions |
|-------|--------|---------|---------|------------|---------|---------------------|
| 0     | 0      | [true, false, false, false] | [true, false, ...] | [true, false, ...] | Place Q at (0,0) | [0, _, _, _] |
| 1     | 1      | [true, false, false, false] | ... | ... | Try col 0: blocked | |
|       |        | [true, true, false, false] | ... | ... | Try col 1: safe | [0, 1, _, _] |
| 2     | 2      | ... | ... | ... | Place Q at (2, 2) | [0, 1, 2, _] |
| ...   | ...    | ... | ... | ... | Continue until solution |

---

## 3. Most Optimal Solution: **Bit Manipulation (Advanced)**

### **Core Idea:**  
Use **bitmasks** for columns, diagonals, and anti-diagonals to achieve **O(1)** checks** and faster execution.

### **Algorithm:**

- Represent columns, diagonals, and anti-diagonals as integers.
- Use bitwise operations (`&`, `|`, `~`) to check and set bits.
- Recursively place queens in safe positions efficiently.

---

### **Java Code Snippet (Conceptual):**

```java
public class NQueensBitMask {
    private List<List<String>> solutions = new ArrayList<>();

    public List<List<String>> solveNQueens(int n) {
        backtrack(n, 0, 0, 0, 0, new ArrayList<>());
        return solutions;
    }

    private void backtrack(int n, int row, int cols, int diag, int antiDiag, List<Integer> positions) {
        if (row == n) {
            solutions.add(createBoard(positions, n));
            return;
        }

        int availablePositions = ((1 << n) - 1) & ~(cols | diag | antiDiag);

        while (availablePositions != 0) {
            int position = availablePositions & -availablePositions; // rightmost set bit
            availablePositions -= position;

            int col = Integer.numberOfTrailingZeros(position);
            positions.add(col);
            backtrack(n, row + 1,
                      cols | position,
                      (diag | position) << 1,
                      (antiDiag | position) >> 1,
                      positions);
            positions.remove(positions.size() - 1);
        }
    }

    private List<String> createBoard(List<Integer> positions, int n) {
        List<String> board = new ArrayList<>();
        for (int row = 0; row < n; row++) {
            StringBuilder sb = new StringBuilder();
            for (int col = 0; col < n; col++) {
                if (positions.get(row) == col)
                    sb.append('Q');
                else
                    sb.append('.');
            }
            board.add(sb.toString());
        }
        return board;
    }
}
```

### **Complexity:**

- **Time Complexity:**  
O(N!), with very efficient pruning due to bitwise operations.

- **Space Complexity:**  
O(N) for recursion stack and bitmasks.

---

## **Summary:**

| Approach | Key Idea | Time Complexity | Space Complexity | Remarks |
|------------|------------|-------------------|-------------------|---------|
| Brute Force | Generate all permutations, check safety | O(N!) | O(N) | Naive, slow for large N |
| Backtracking with Arrays | Use arrays to mark attacked columns/diagonals | O(N!) (pruned) | O(N) | Common approach |
| Bit Manipulation | Use bitmasks for O(1) checks | O(N!) | O(N) | Most optimized for large N |

---

## **Quick Tips for Interviews:**

- Always think about **pruning** to reduce the search space.
- Use **arrays** or **bitmasks** to efficiently check conflicts.
- Understand the **recursion tree** and how backtracking explores solutions.
- Write **clean, modular code** with helper functions for safety checks and board creation.
- For large N, **bit manipulation** offers the best performance.

---

**Happy Coding!**
