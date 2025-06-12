# Game of Life

The **Game of Life** problem involves updating a 2D grid representing cells as either live (1) or dead (0), based on specific rules depending on their neighbors. The challenge is to perform the update **in-place** with minimal extra space.

---

## Problem Statement

Given a `m x n` grid `board` representing the current state of cells, update the board to the next state according to these rules:

- **Any live cell (1)** with fewer than two live neighbors dies (underpopulation).
- **Any live cell (1)** with two or three live neighbors lives on.
- **Any live cell (1)** with more than three live neighbors dies (overpopulation).
- **Any dead cell (0)** with exactly three live neighbors becomes alive (reproduction).

---

## Approaches to Solve the Problem

---

### 1. **Brute Force Approach**

### **Algorithm**

- Create a copy of the original grid to store the next state.
- For each cell:
    - Count the number of live neighbors using the original grid.
    - Apply the rules to determine the next state in the copy.
- Copy the updated states back to the original grid.

### **Java Code**

```java
public void gameOfLife(int[][] board) {
    int m = board.length;
    int n = board[0].length;
    int[][] copy = new int[m][n];

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            copy[i][j] = board[i][j];
        }
    }

    int[] directions = {-1, 0, 1, 0, -1};

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            int liveNeighbors = 0;
            for (int d = 0; d < 4; d++) {
                int ni = i + directions[d];
                int nj = j + directions[d + 1];
                if (ni >= 0 && ni < m && nj >= 0 && nj < n && copy[ni][nj] == 1) {
                    liveNeighbors++;
                }
            }
            if (copy[i][j] == 1) {
                if (liveNeighbors < 2 || liveNeighbors > 3) {
                    board[i][j] = 0;
                }
            } else {
                if (liveNeighbors == 3) {
                    board[i][j] = 1;
                }
            }
        }
    }
}

```

### **Complexity Analysis**

- **Time Complexity:**`O(m * n)` because each cell is processed once, and counting neighbors is constant time.
- **Space Complexity:**`O(m * n)` for the copy of the grid.

### **Dry Run**

Suppose initial grid:

| 0 | 1 | 0 |
| --- | --- | --- |
| 0 | 0 | 1 |
| 1 | 1 | 1 |
- For cell `(2, 2)`:
    - Count neighbors in original grid.
    - Apply rules based on neighbor count.
- Final updated grid after processing all cells.

---

### 2. **In-Place Solution Using State Encoding (Most Efficient)**

### **Algorithm**

- Use **bit manipulation** or **state encoding** to store both the current and next state within the same grid.
- For each cell:
    - Count neighbors considering only the **current state**.
    - Encode the **next state** temporarily in the same cell without losing the current state information.
- After processing all cells, shift bits to update the current state.

**Key idea:**

- 0 or 1 in the grid can be used to represent current state.
- Use additional bits to encode the next state:
    - For example, 2 (binary `10`) can represent "dead -> alive".
    - 3 (binary `11`) can represent "alive -> alive".

### **Java Code**

```java
public void gameOfLife(int[][] board) {
    int m = board.length;
    int n = board[0].length;

    // Directions for the 8 neighbors
    int[] directions = {-1, -1, -1, 0, 0, 1, 1, 1, -1};

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            int liveNeighbors = 0;
            for (int d = 0; d < 8; d++) {
                int ni = i + directions[d];
                int nj = j + directions[d + 1];
                if (ni >= 0 && ni < m && nj >= 0 && nj < n) {
                    // Check the current state only (bitwise AND with 1)
                    liveNeighbors += board[ni][nj] & 1;
                }
            }
            // Apply rules and encode next state
            if ((board[i][j] & 1) == 1) {
                if (liveNeighbors == 2 || liveNeighbors == 3) {
                    // Cell stays alive
                    board[i][j] |= 2; // Set second bit to 1 (next state = alive)
                }
            } else {
                if (liveNeighbors == 3) {
                    // Cell becomes alive
                    board[i][j] |= 2; // Set second bit to 1 (next state = alive)
                }
            }
        }
    }

    // Finalize the update by shifting bits
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            // Shift right to update current state
            board[i][j] >>= 1;
        }
    }
}

```

### **Complexity Analysis**

- **Time Complexity:**`O(m * n)` — each cell is processed once, counting neighbors in constant time.
- **Space Complexity:**`O(1)` — in-place updates without extra space, aside from fixed variables.

### **Dry Run**

Initial grid:

| 0 | 1 | 0 |
| --- | --- | --- |
| 0 | 0 | 1 |
| 1 | 1 | 1 |
- For cell `(1,1)`:
    - Count neighbors considering only current state.
    - Decide next state and encode it in the second bit.
- After processing all cells, shift bits right to finalize next state.

---

## Summary

| Approach | Time Complexity | Space Complexity | Pros | Cons |
| --- | --- | --- | --- | --- |
| Brute Force | O(m * n) | O(m * n) | Simple, easy to implement | Uses extra space, not optimal |
| In-Place Encoding | O(m * n) | O(1) | Space-efficient, optimal | Slightly complex implementation |

---

## Final Tips

- Always consider in-place solutions to optimize space.
- Use bit manipulation for encoding multiple states within a single variable.
- Validate neighbor counts carefully, especially at the edges.
- Practice dry runs with small matrices to understand how states evolve.

---

This guide provides a structured approach for understanding and revising the **Game of Life** problem, suitable for interviews and quick revision.
