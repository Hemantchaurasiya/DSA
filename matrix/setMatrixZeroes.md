# Set Matrix Zeroes

This problem involves modifying a given matrix so that if an element is zero, its entire row and column are set to zero. It is a classic problem to practice matrix manipulation, in-place algorithms, and space optimization.

---

## 1. Brute Force Approach

### Algorithm:

- Traverse the entire matrix.
- Whenever a zero is encountered at position `(i, j)`:
    - Set all elements in row `i` to zero.
    - Set all elements in column `j` to zero.
- To avoid affecting subsequent operations, use an auxiliary data structure to store the positions of zeros (like a list of `(row, col)` pairs).
- After the first pass, update the matrix based on the stored positions.

---

### Java Code:

```java
public class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        List<int[]> zeroPositions = new ArrayList<>();

        // Find all zero positions
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 0) {
                    zeroPositions.add(new int[]{i, j});
                }
            }
        }

        // Set rows and columns to zero based on stored positions
        for (int[] pos : zeroPositions) {
            int row = pos[0];
            int col = pos[1];
            // Set entire row to zero
            for (int j = 0; j < n; j++) {
                matrix[row][j] = 0;
            }
            // Set entire column to zero
            for (int i = 0; i < m; i++) {
                matrix[i][col] = 0;
            }
        }
    }
}

```

---

### Complexity Analysis:

- **Time Complexity:**
    
    **O(m * n * (m + n))**
    
    - Finding zeros: `O(m * n)`
    - Setting rows and columns: For each zero, potentially `O(m + n)` operations, worst-case if many zeros exist.
- **Space Complexity:**
    
    **O(m * n)** in the worst case (if many zeros), due to storing all zero positions.
    

---

### Dry Run:

**Input:**

```
matrix = [
  [1, 1, 1],
  [1, 0, 1],
  [1, 1, 1]
]

```

**Execution:**

1. Find zero at `(1, 1)` → store `(1, 1)`.
2. Set entire row 1 to zero:

```
[
  [1, 1, 1],
  [0, 0, 0],
  [1, 1, 1]
]

```

1. Set entire column 1 to zero:

```
[
  [1, 0, 1],
  [0, 0, 0],
  [1, 0, 1]
]

```

**Result:**

```
[
  [1, 0, 1],
  [0, 0, 0],
  [1, 0, 1]
]

```

---

## 2. Better Solution (Using Markers)

### Algorithm:

- Use two separate arrays (`rows` and `cols`) to mark whether a particular row or column should be zeroed.
- Traverse the matrix once:
    - When a zero is found, mark the corresponding row and column in `rows` and `cols`.
- In a second pass, set all marked rows and columns to zero.

---

### Java Code:

```java
public class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        boolean[] rows = new boolean[m];
        boolean[] cols = new boolean[n];

        // Mark zero rows and columns
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 0) {
                    rows[i] = true;
                    cols[j] = true;
                }
            }
        }

        // Set marked rows to zero
        for (int i = 0; i < m; i++) {
            if (rows[i]) {
                for (int j = 0; j < n; j++) {
                    matrix[i][j] = 0;
                }
            }
        }

        // Set marked columns to zero
        for (int j = 0; j < n; j++) {
            if (cols[j]) {
                for (int i = 0; i < m; i++) {
                    matrix[i][j] = 0;
                }
            }
        }
    }
}

```

---

### Complexity Analysis:

- **Time Complexity:**
    
    **O(m * n)**
    
    - Two passes over the entire matrix: one to mark, one to set zeros.
- **Space Complexity:**
    
    **O(m + n)** due to `rows` and `cols` arrays.
    

---

### Dry Run:

**Input:**

```
matrix = [
  [1, 2, 3],
  [4, 0, 6],
  [7, 8, 9]
]

```

**Execution:**

1. Mark `rows[1] = true`, `cols[1] = true`.
2. Set row 1 to zero:

```
[
  [1, 2, 3],
  [0, 0, 0],
  [7, 8, 9]
]

```

1. Set column 1 to zero:

```
[
  [1, 0, 3],
  [0, 0, 0],
  [7, 0, 9]
]

```

**Result:**

```
[
  [1, 0, 3],
  [0, 0, 0],
  [7, 0, 9]
]

```

---

## 3. Most Optimal Solution (In-Place, Constant Space)

### Algorithm:

- Use the first row and first column as markers to indicate whether a row or column should be zeroed.
- Also, keep track of whether the first row and first column themselves need to be zeroed separately.
- Steps:
    1. Scan the first row and column to check if they contain zeros.
    2. Use the rest of the matrix to mark zeros in the first row and first column.
    3. Zero out cells based on these markers.
    4. Zero the first row and first column if needed.

---

### Java Code:

```java
public class Solution {
    public void setZeroes(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].length;
        boolean firstRowHasZero = false;
        boolean firstColHasZero = false;

        // Check if first row has zero
        for (int j = 0; j < n; j++) {
            if (matrix[0][j] == 0) {
                firstRowHasZero = true;
                break;
            }
        }

        // Check if first column has zero
        for (int i = 0; i < m; i++) {
            if (matrix[i][0] == 0) {
                firstColHasZero = true;
                break;
            }
        }

        // Use first row and column as markers
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][j] == 0) {
                    matrix[i][0] = 0; // Mark row
                    matrix[0][j] = 0; // Mark column
                }
            }
        }

        // Zero out cells based on markers
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (matrix[i][0] == 0 || matrix[0][j] == 0) {
                    matrix[i][j] = 0;
                }
            }
        }

        // Zero out first row if needed
        if (firstRowHasZero) {
            for (int j = 0; j < n; j++) {
                matrix[0][j] = 0;
            }
        }

        // Zero out first column if needed
        if (firstColHasZero) {
            for (int i = 0; i < m; i++) {
                matrix[i][0] = 0;
            }
        }
    }
}

```

---

### Complexity Analysis:

- **Time Complexity:**
    
    **O(m * n)**
    
    - Multiple passes over the matrix, but all are linear.
- **Space Complexity:**
    
    **O(1)** (constant space)
    
    - Uses only a few boolean variables and in-place markers.

---

### Dry Run:

**Input:**

```
matrix = [
  [1, 2, 0],
  [4, 5, 6],
  [7, 8, 9]
]

```

**Execution:**

1. `firstRowHasZero = true` (since `matrix[0][2] == 0`)
2. `firstColHasZero = false`
3. Mark zeros in other cells:
    - `matrix[0][2]` is zero, so mark row 0 and column 2:
        - `matrix[0][2]` already zero.
    - No other zeros found.
4. Zero cells based on markers:
    - For example, `matrix[1][2]` should be zero because column 2 is marked.
    - `matrix[2][2]` should be zero.
5. Zero out first row and column:
    - Since `firstRowHasZero == true`, zero out row 0:

```
[
  [0, 0, 0],
  [4, 5, 6],
  [7, 8, 9]
]

```

1. Final matrix:

```
[
  [0, 0, 0],
  [4, 5, 0],
  [7, 8, 0]
]

```

---

## Summary

| Approach | Space | Time | Key Idea | When to Use |
| --- | --- | --- | --- | --- |
| Brute Force | O(m*n) | O(m*n) | Store zero positions and update | Small matrices or conceptual clarity |
| Better Solution | O(m + n) | O(m * n) | Use auxiliary arrays for rows and columns | Medium-sized matrices with acceptable extra space |
| Optimal In-Place | O(1) | O(m * n) | Use first row and column as markers | Large matrices, space-constrained environments |

---

This comprehensive guide covers all major approaches to solving **Set Matrix Zeroes**, from naive to optimal, with explanations, code, complexity, and dry runs for quick revision and interview preparation.
