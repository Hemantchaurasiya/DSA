# ✅ Wave Traversal

### 🧠 **Problem Statement:**

Given a 2D matrix, print the elements in a **wave-like pattern**, **column by column**:

- If the column index is even: **top to bottom**
- If the column index is odd: **bottom to top**

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

`1 4 7 8 5 2 3 6 9`

---

### 🎯 Intuition:

You "wave" through the matrix column by column:

- Even-indexed columns: go **down**
- Odd-indexed columns: go **up**

---

### ✅ Java Code:

```java
public class WaveTraversal {

    public static void printWave(int[][] matrix) {
        int rows = matrix.length;
        int cols = matrix[0].length;

        for (int col = 0; col < cols; col++) {
            if (col % 2 == 0) {
                // Top to Bottom
                for (int row = 0; row < rows; row++) {
                    System.out.print(matrix[row][col] + " ");
                }
            } else {
                // Bottom to Top
                for (int row = rows - 1; row >= 0; row--) {
                    System.out.print(matrix[row][col] + " ");
                }
            }
        }
    }

    public static void main(String[] args) {
        int[][] matrix = {
            { 1,  2,  3 },
            { 4,  5,  6 },
            { 7,  8,  9 }
        };
        System.out.print("Wave Traversal: ");
        printWave(matrix);
    }
}
```

---

### 🧾 Output:

```
Wave Traversal: 1 4 7 8 5 2 3 6 9
```

---

### 🧠 Use Cases:

- Pattern printing
- Zigzag scanning in image processing
- Robotic movement simulation in grids
- Spreadsheet-like data parsing
