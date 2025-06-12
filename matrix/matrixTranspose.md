# 🔁 Matrix Transpose

### 🧠 **What is Transposing a Matrix?**

**Transpose** of a matrix flips it over its **main diagonal**, i.e.,

convert all `matrix[i][j]` into `matrix[j][i]`.

- The **rows become columns**
- The **columns become rows**

---

### 🔢 Example:

**Original Matrix:**

```
1 2 3
4 5 6
```

**Transpose:**

```
1 4
2 5
3 6
```

---

### 📌 Types of Transpose:

1. **Square Matrix Transpose (in-place)**
2. **Rectangular Matrix Transpose (new matrix)**

---

### ✅ Java Code

### 🔷 1. Transpose of a **Rectangular Matrix** (using new matrix):

```java
public class MatrixTranspose {

    public static int[][] transpose(int[][] matrix) {
        int rows = matrix.length;
        int cols = matrix[0].length;
        int[][] transposed = new int[cols][rows];

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                transposed[j][i] = matrix[i][j];
            }
        }

        return transposed;
    }

    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6}
        };

        int[][] result = transpose(matrix);

        System.out.println("Transposed Matrix:");
        for (int[] row : result) {
            for (int num : row) {
                System.out.print(num + " ");
            }
            System.out.println();
        }
    }
}
```

### 🧾 Output:

```
Transposed Matrix:
1 4
2 5
3 6
```

---

### 🔷 2. Transpose of a **Square Matrix** (in-place):

```java
public static void transposeInPlace(int[][] matrix) {
    int n = matrix.length;

    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            // Swap symmetric elements
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }
}
```

---

### 🧠 Use Cases:

- Rotate matrix (transpose + reverse rows/columns)
- Data transformation in spreadsheets
- Matrix algebra
- Graph representations (adjacency matrix transpose = reverse edges)

---

### 🧪 Practice Ideas:

- Transpose + reverse rows to rotate matrix 90° clockwise
- Transpose + reverse columns to rotate 90° counterclockwise
- Use transpose in matrix multiplication
