# 🔄 Matrix Rotation (90°, 180°, 270°)

### 🧠 What Does It Mean to Rotate a Matrix?

Given a matrix, rotate it:

- **90° clockwise**
- **90° counterclockwise**
- **180°**

Rotation changes **element positions** while preserving the grid structure.

---

### 🔢 Example:

**Original Matrix** (3×3):

```
1 2 3
4 5 6
7 8 9
```

---

### ✅ 90° Clockwise Rotation:

```
7 4 1
8 5 2
9 6 3
```

### ✅ 90° Counterclockwise Rotation:

```
3 6 9
2 5 8
1 4 7
```

### ✅ 180° Rotation:

```
9 8 7
6 5 4
3 2 1
```

---

## 🔧 90° Clockwise Rotation (In-place for Square Matrix)

### 🔍 Steps:

1. **Transpose** the matrix.
2. **Reverse each row.**

### ✅ Java Code:

```java
public class RotateMatrix {

    public static void rotate90Clockwise(int[][] matrix) {
        int n = matrix.length;

        // Step 1: Transpose
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }

        // Step 2: Reverse each row
        for (int i = 0; i < n; i++) {
            int left = 0, right = n - 1;
            while (left < right) {
                int temp = matrix[i][left];
                matrix[i][left] = matrix[i][right];
                matrix[i][right] = temp;
                left++;
                right--;
            }
        }
    }

    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };

        rotate90Clockwise(matrix);

        System.out.println("90° Clockwise Rotation:");
        for (int[] row : matrix) {
            for (int num : row) {
                System.out.print(num + " ");
            }
            System.out.println();
        }
    }
}
```

---

### 🔄 90° Counterclockwise (In-place)

### 🔍 Steps:

1. **Transpose**
2. **Reverse each column**

```java
public static void rotate90CounterClockwise(int[][] matrix) {
    int n = matrix.length;

    // Step 1: Transpose
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    }

    // Step 2: Reverse each column
    for (int j = 0; j < n; j++) {
        int top = 0, bottom = n - 1;
        while (top < bottom) {
            int temp = matrix[top][j];
            matrix[top][j] = matrix[bottom][j];
            matrix[bottom][j] = temp;
            top++;
            bottom--;
        }
    }
}
```

---

### 🔁 180° Rotation (In-place)

### 🔍 Steps:

1. **Reverse rows**
2. **Reverse columns**

```java
public static void rotate180(int[][] matrix) {
    int n = matrix.length;

    // Reverse each row
    for (int i = 0; i < n; i++) {
        reverseArray(matrix[i]);
    }

    // Reverse all rows
    for (int i = 0; i < n / 2; i++) {
        int[] temp = matrix[i];
        matrix[i] = matrix[n - i - 1];
        matrix[n - i - 1] = temp;
    }
}

private static void reverseArray(int[] arr) {
    int left = 0, right = arr.length - 1;
    while (left < right) {
        int temp = arr[left];
        arr[left] = arr[right];
        arr[right] = temp;
        left++;
        right--;
    }
}
```

---

### 🧠 Use Cases:

- Rotating images or tiles (graphics, games)
- Puzzle solvers (Sudoku, matrix-based boards)
- Algorithms involving 2D transformations
- Tetris-style simulations
