# Boundary Traversal of a Matrix

### 🧠 **Problem Statement:**

Given a 2D matrix, print only its **outer boundary** in clockwise order.

---

### 🧾 Example:

```java
int[][] matrix = {
    { 1, 2, 3, 4 },
    { 5, 6, 7, 8 },
    { 9, 10, 11, 12 },
    { 13, 14, 15, 16 }
};
```

**Boundary Output:**

`1 2 3 4 8 12 16 15 14 13 9 5`

---

### ✅ Steps:

1. Traverse **top row** → left to right
2. Traverse **right column** → top to bottom (excluding top cell already printed)
3. Traverse **bottom row** → right to left (excluding rightmost cell)
4. Traverse **left column** → bottom to top (excluding top and bottom)

⚠️ Take care when there's only **1 row** or **1 column** to avoid printing elements multiple times.

---

### ✅ Java Code:

```java
public class BoundaryTraversal {

    public static void printBoundary(int[][] matrix) {
        int rows = matrix.length;
        int cols = matrix[0].length;

        // Top row
        for (int col = 0; col < cols; col++) {
            System.out.print(matrix[0][col] + " ");
        }

        // Right column
        for (int row = 1; row < rows; row++) {
            System.out.print(matrix[row][cols - 1] + " ");
        }

        // Bottom row (only if more than 1 row)
        if (rows > 1) {
            for (int col = cols - 2; col >= 0; col--) {
                System.out.print(matrix[rows - 1][col] + " ");
            }
        }

        // Left column (only if more than 1 column)
        if (cols > 1) {
            for (int row = rows - 2; row > 0; row--) {
                System.out.print(matrix[row][0] + " ");
            }
        }
    }

    public static void main(String[] args) {
        int[][] matrix = {
            { 1, 2, 3, 4 },
            { 5, 6, 7, 8 },
            { 9, 10, 11, 12 },
            { 13, 14, 15, 16 }
        };
        System.out.print("Boundary Traversal: ");
        printBoundary(matrix);
    }
}
```

---

### 🧾 Output:

```
Boundary Traversal: 1 2 3 4 8 12 16 15 14 13 9 5
```

---

### 🧠 Use Cases:

- When we need only outer elements (e.g., frame of an image)
- Pattern printing problems
- Simulation of "circular scanning" robots or drones
- Detecting wall elements in grid-based games
