# ✅ Diagonal Zigzag Traversal

### 🧠 **Problem Statement:**

Given an `m x n` matrix, return all elements in **zigzag diagonal order**:

- Traverse diagonals starting from top-left to bottom-right
- The direction **alternates**: one diagonal goes up-right ↗️, the next goes down-left ↙️

---

### 🔢 Example Input:

```java
int[][] matrix = {
    { 1,  2,  3 },
    { 4,  5,  6 },
    { 7,  8,  9 }
};
```

### ✅ Output:

`1 2 4 7 5 3 6 8 9`

---

### 🎯 Intuition:

Every diagonal starts at:

- Either the **first row** (col increases)
- Or the **last column** (row increases)

To traverse a diagonal:

- If direction is **up-right**, decrease row, increase col
- If direction is **down-left**, increase row, decrease col

Alternate directions for each diagonal.

---

### ✅ Java Code:

```java
public class ZigzagDiagonalTraversal {

    public static int[] findDiagonalOrder(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return new int[0];

        int rows = matrix.length;
        int cols = matrix[0].length;
        int[] result = new int[rows * cols];

        int row = 0, col = 0;
        boolean up = true;
        int index = 0;

        while (index < result.length) {
            result[index++] = matrix[row][col];

            if (up) {
                // Going up-right
                if (col == cols - 1) {
                    row++;
                    up = false;
                } else if (row == 0) {
                    col++;
                    up = false;
                } else {
                    row--;
                    col++;
                }
            } else {
                // Going down-left
                if (row == rows - 1) {
                    col++;
                    up = true;
                } else if (col == 0) {
                    row++;
                    up = true;
                } else {
                    row++;
                    col--;
                }
            }
        }

        return result;
    }

    public static void main(String[] args) {
        int[][] matrix = {
            { 1,  2,  3 },
            { 4,  5,  6 },
            { 7,  8,  9 }
        };

        int[] result = findDiagonalOrder(matrix);
        System.out.print("Zigzag Diagonal Traversal: ");
        for (int num : result) {
            System.out.print(num + " ");
        }
    }
}
```

---

### 🧾 Output:

```
Zigzag Diagonal Traversal: 1 2 4 7 5 3 6 8 9
```

---

### 🧠 Use Cases:

- Text formatting in tables (Zigzag pattern)
- Memory-efficient traversal of sparse matrices
- Image and signal processing (especially for zigzag encoding in JPEG compression)
- Game mechanics (chess, puzzle boards)
