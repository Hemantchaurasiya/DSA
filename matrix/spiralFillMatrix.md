# Spiral Fill Matrix (Construction)

This problem involves filling an `m x n` matrix in a spiral order with consecutive numbers starting from 1. The goal is to generate the matrix in a spiral pattern, moving right, down, left, and up repeatedly until the matrix is completely filled.

---

## 1. Approach Name: **Brute Force (Simulation with Visited Matrix)**

### Algorithm:

- Initialize an empty `m x n` matrix filled with zeros.
- Maintain four boundary variables:
    - `top` (initially 0)
    - `bottom` (initially m-1)
    - `left` (initially 0)
    - `right` (initially n-1)
- Maintain a counter `num` starting at 1.
- Loop until `top > bottom` or `left > right`:
    - Fill the top row from `left` to `right`, then increment `top`.
    - Fill the right column from `top` to `bottom`, then decrement `right`.
    - Fill the bottom row from `right` to `left`, then decrement `bottom`.
    - Fill the left column from `bottom` to `top`, then increment `left`.
- Continue until all cells are filled.

### Java Code:

```java
public int[][] generateMatrix(int m, int n) {
    int[][] matrix = new int[m][n];
    int top = 0, bottom = m - 1;
    int left = 0, right = n - 1;
    int num = 1;

    while (top <= bottom && left <= right) {
        // Fill top row
        for (int i = left; i <= right; i++) {
            matrix[top][i] = num++;
        }
        top++;

        // Fill right column
        for (int i = top; i <= bottom; i++) {
            matrix[i][right] = num++;
        }
        right--;

        if (top <= bottom) {
            // Fill bottom row
            for (int i = right; i >= left; i--) {
                matrix[bottom][i] = num++;
            }
            bottom--;
        }

        if (left <= right) {
            // Fill left column
            for (int i = bottom; i >= top; i--) {
                matrix[i][left] = num++;
            }
            left++;
        }
    }

    return matrix;
}

```

### Complexity Analysis:

- **Time Complexity:** **O(m * n)**
    
    Each cell is visited exactly once during the filling process.
    
- **Space Complexity:** **O(1)** (excluding the output matrix)
    
    Only a few variables are used for boundaries and counters.
    

### Dry Run:

Suppose `m = 3`, `n = 3`.

Initial state:

```
matrix = [[0,0,0],
          [0,0,0],
          [0,0,0]]

top=0, bottom=2, left=0, right=2, num=1

```

Iteration 1:

- Fill top row (row 0, cols 0 to 2): 1, 2, 3
- top=1
- Fill right column (col 2, rows 1 to 2): 4, 5
- right=1
- Fill bottom row (row 2, cols 1 to 0): 6, 7
- bottom=1
- Fill left column (col 0, rows 1 to 1): 8
- left=1

Iteration 2:

- Fill top row (row 1, cols 1 to 1): 9
- top=2
- Loop ends as `top=2 > bottom=1` and `left=1 > right=1`.

Final matrix:

```
[[1, 2, 3],
 [8, 9, 4],
 [7, 6, 5]]

```

---

## 2. Approach Name: **Optimized Solution (Direct Filling Using Direction Vectors)**

### Algorithm:

- Use direction vectors to navigate through the matrix:
    - Right: (0, 1)
    - Down: (1, 0)
    - Left: (0, -1)
    - Up: (-1, 0)
- Start from position `(0, 0)` with direction index 0 (right).
- For each number from 1 to `m*n`:
    - Fill the current cell.
    - Calculate the next position based on the current direction.
    - If the next position is out of bounds or already filled, change direction clockwise.
    - Move to the next position and continue.

### Java Code:

```java
public int[][] generateMatrix(int m, int n) {
    int[][] matrix = new int[m][n];
    int[] dirX = {0, 1, 0, -1};
    int[] dirY = {1, 0, -1, 0};
    int total = m * n;
    int row = 0, col = 0, dirIdx = 0;

    for (int num = 1; num <= total; num++) {
        matrix[row][col] = num;
        int nextRow = row + dirX[dirIdx];
        int nextCol = col + dirY[dirIdx];

        if (nextRow < 0 || nextRow >= m || nextCol < 0 || nextCol >= n || matrix[nextRow][nextCol] != 0) {
            // Change direction clockwise
            dirIdx = (dirIdx + 1) % 4;
            nextRow = row + dirX[dirIdx];
            nextCol = col + dirY[dirIdx];
        }

        row = nextRow;
        col = nextCol;
    }
    return matrix;
}

```

### Complexity Analysis:

- **Time Complexity:** **O(m * n)**
    
    Each cell is visited exactly once, with constant time for direction change checks.
    
- **Space Complexity:** **O(1)** (excluding output matrix).

### Dry Run:

Suppose `m=3`, `n=3`.

Start:

- `(row, col) = (0, 0)`, direction index 0 (right)
- Fill `(0, 0)`: 1
- Next position: `(0, 1)` → valid, move
- Fill `(0, 1)`: 2
- Next: `(0, 2)` → valid, move
- Fill `(0, 2)`: 3
- Next: `(0, 3)` → invalid, change direction to down
- Next: `(1, 2)` → valid, move
- Fill `(1, 2)`: 4
- Next: `(2, 2)` → valid, move
- Fill `(2, 2)`: 5
- Next: `(3, 2)` → invalid, change direction to left
- Next: `(2, 1)` → valid, move
- Fill `(2, 1)`: 6
- Next: `(2, 0)` → valid, move
- Fill `(2, 0)`: 7
- Next: `(2, -1)` → invalid, change direction to up
- Next: `(1, 0)` → valid, move
- Fill `(1,0)`: 8
- Next: `(0, 0)` → already filled, change direction to right
- Next: `(1, 1)` → valid, move
- Fill `(1,1)`: 9
- Next: `(1, 2)` → already filled, change direction to down, but next position is invalid; process ends.

Final matrix:

```
[[1, 2, 3],
 [8, 9, 4],
 [7, 6, 5]]

```

---

## Summary:

| Aspect | Brute Force | Optimized (Direction Vectors) |
| --- | --- | --- |
| Approach | Boundary-controlled layer-by-layer fill | Direction vector traversal with boundary checks |
| Ease of implementation | Moderate | Slightly more elegant and scalable |
| Time Complexity | O(m*n) | O(m*n) |
| Space Complexity | O(1) (excluding output) | O(1) |

---

This comprehensive revision should prepare you well for solving and understanding the "Spiral Fill Matrix" problem efficiently during interviews!
