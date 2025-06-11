# Sudoku Solver

This guide covers multiple approaches to solving the **Sudoku Solver** problem, from brute-force to optimized solutions, with detailed explanations, algorithms, code, and complexity analysis for interview preparation and quick revision.

---

## Problem Statement
Given a 9x9 Sudoku board, fill the empty cells (denoted by '.') such that each row, each column, and each of the nine 3x3 sub-boxes contain all digits from 1 to 9 exactly once.

---

## Approach 1: Brute Force (Backtracking)

### Core Idea
Try all possible digits in each empty cell, backtracking when a digit violates Sudoku rules. This naive approach explores all configurations until a solution is found.

### Algorithm
1. Traverse the board to find an empty cell.
2. For each empty cell, attempt to fill it with digits 1 through 9.
3. For each candidate digit:
   - Check if placing it violates Sudoku rules.
   - If valid, assign the digit and recurse to the next empty cell.
   - If recursion leads to a solution, return true.
   - Else, backtrack (reset cell to '.'), try next digit.
4. If no digit fits, backtrack to previous step.

### Recursion Tree
```
Level 0: Empty cell 1
    - Try digit 1: invalid? No -> recurse
    - Try digit 2: invalid? No -> recurse
    ...
    - Try digit 9: invalid? No -> recurse
Level 1: Empty cell 2
    ...
```

### Java Code
```java
public class SudokuSolver {
    public void solveSudoku(char[][] board) {
        backtrack(board);
    }

    private boolean backtrack(char[][] board) {
        for (int row = 0; row < 9; row++) {
            for (int col = 0; col < 9; col++) {
                if (board[row][col] == '.') {
                    for (char c = '1'; c <= '9'; c++) {
                        if (isValid(board, row, col, c)) {
                            board[row][col] = c;
                            if (backtrack(board))
                                return true;
                            board[row][col] = '.'; // backtrack
                        }
                    }
                    return false; // no valid digit found
                }
            }
        }
        return true; // all cells filled
    }

    private boolean isValid(char[][] board, int row, int col, char c) {
        for (int i = 0; i < 9; i++) {
            if (board[row][i] == c || board[i][col] == c)
                return false;
            if (board[3*(row/3) + i/3][3*(col/3) + i%3] == c)
                return false;
        }
        return true;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(9^(N))*, where N is the number of empty cells (~81). In worst case, each cell tries 9 options, leading to exponential complexity.
- **Space Complexity:** *O(N)* due to recursion stack (max depth = number of empty cells).

### Dry Run Example
Suppose the initial board has only one empty cell at (0,0), and it is '.'.
- Try '1' → Check validity → valid → assign '1' → recurse.
- If subsequent cells are also empty, repeat the process.
- Backtracking occurs if no valid options exist at some point.

---

## Approach 2: Constraint Propagation with Hash Sets (Better Solution)

### Core Idea
Use additional data structures to keep track of used digits in rows, columns, and boxes, reducing validation time. This prevents rechecking the entire row, column, and box every time.

### Algorithm
1. Initialize hash sets for each row, column, and box to track used digits.
2. Populate these sets based on the initial board.
3. Use backtracking:
   - Find the next empty cell.
   - For each digit '1' to '9':
     - Check if the digit is not in the corresponding row, column, and box sets.
     - If valid, place the digit, update sets, and recurse.
     - If recursion fails, remove the digit and revert sets.
4. Continue until the board is complete.

### Java Code
```java
import java.util.HashSet;

public class SudokuSolver {
    private HashSet<Character>[] rows = new HashSet[9];
    private HashSet<Character>[] cols = new HashSet[9];
    private HashSet<Character>[] boxes = new HashSet[9];

    public void solveSudoku(char[][] board) {
        for (int i = 0; i < 9; i++) {
            rows[i] = new HashSet<>();
            cols[i] = new HashSet<>();
            boxes[i] = new HashSet<>();
        }

        // Initialize sets
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] != '.') {
                    char num = board[r][c];
                    int boxIndex = (r / 3) * 3 + c / 3;
                    rows[r].add(num);
                    cols[c].add(num);
                    boxes[boxIndex].add(num);
                }
            }
        }
        backtrack(board, 0, 0);
    }

    private boolean backtrack(char[][] board, int row, int col) {
        if (row == 9) return true; // Completed all rows
        if (col == 9) return backtrack(board, row + 1, 0);
        if (board[row][col] != '.') {
            return backtrack(board, row, col + 1);
        }

        int boxIndex = (row / 3) * 3 + col / 3;
        for (char num = '1'; num <= '9'; num++) {
            if (!rows[row].contains(num) && !cols[col].contains(num) && !boxes[boxIndex].contains(num)) {
                board[row][col] = num;
                rows[row].add(num);
                cols[col].add(num);
                boxes[boxIndex].add(num);

                if (backtrack(board, row, col + 1))
                    return true;

                // Backtrack
                board[row][col] = '.';
                rows[row].remove(num);
                cols[col].remove(num);
                boxes[boxIndex].remove(num);
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(9^(N))*, but practically much faster due to constraint propagation.
- **Space Complexity:** *O(N)* for hash sets storing used digits, where N is the number of filled cells.

---

## Approach 3: Optimized with Bitmasking (Most Efficient)

### Core Idea
Use bit masks to represent used digits for each row, column, and box, enabling constant-time checks and updates.

### Algorithm
1. Initialize three arrays of integers (`int[]`) for rows, columns, and boxes:
   - Each integer uses 9 bits to represent digits 1-9.
2. Populate masks based on initial board.
3. Recursively try digits:
   - For each empty cell, determine possible candidates by bitwise AND of inverse masks.
   - Iterate over candidates using bit operations.
   - Update masks accordingly and recurse.
   - Backtrack if needed.

### Java Code
```java
public class SudokuSolver {
    private int[] rows = new int[9];
    private int[] cols = new int[9];
    private int[] boxes = new int[9];

    public void solveSudoku(char[][] board) {
        // Initialize masks
        for (int r = 0; r < 9; r++) {
            for (int c = 0; c < 9; c++) {
                if (board[r][c] != '.') {
                    int num = board[r][c] - '1';
                    int mask = 1 << num;
                    rows[r] |= mask;
                    cols[c] |= mask;
                    boxes[(r / 3) * 3 + c / 3] |= mask;
                }
            }
        }
        backtrack(board, 0, 0);
    }

    private boolean backtrack(char[][] board, int r, int c) {
        if (r == 9) return true;
        if (c == 9) return backtrack(board, r + 1, 0);
        if (board[r][c] != '.') return backtrack(board, r, c + 1);

        int boxIdx = (r / 3) * 3 + c / 3;
        int used = rows[r] | cols[c] | boxes[boxIdx];
        int mask = ((1 << 9) - 1) & (~used);
        while (mask != 0) {
            int pick = mask & (-mask); // least significant bit
            int digit = Integer.numberOfTrailingZeros(pick);
            board[r][c] = (char) ('1' + digit);
            rows[r] |= pick;
            cols[c] |= pick;
            boxes[boxIdx] |= pick;

            if (backtrack(board, r, c + 1))
                return true;

            // Backtrack
            rows[r] &= ~pick;
            cols[c] &= ~pick;
            boxes[boxIdx] &= ~pick;
            board[r][c] = '.';

            mask &= (mask - 1); // remove least significant bit
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(9^(N))*, but with bitmasking, practical speed is improved due to O(1) checks.
- **Space Complexity:** *O(N)* for masks.

---

## Summary of Approaches

| Approach | Core Idea | Validation | Use of Data Structures | Time Complexity | Space Complexity | Notes |
|------------|--------------|--------------|------------------------|-------------------|------------------|--------|
| Brute Force | Try all options with backtracking | Check rules each time | No additional data structures | O(9^(N)) | O(N) recursion stack | Simple but slow |
| Hash Sets | Track used digits with sets | Fast validation | HashSet for rows, columns, boxes | Faster in practice | O(N) | More efficient |
| Bitmasking | Use bits for used digits | Constant-time check | Arrays of integers as masks | Very fast | O(N) | Most optimal |

---

## Final Tips
- Always validate the input board.
- Use constraint propagation to prune search space.
- When implementing backtracking, consider early pruning strategies.
- Bitmasking offers the best performance in practice.

---

This comprehensive guide should prepare you well for solving the Sudoku Solver problem efficiently during interviews!
