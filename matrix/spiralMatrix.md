# 🌀 Spiral Traversal of a Matrix

### 🧠 **Problem Statement:**

Given a 2D matrix, return the elements in **spiral order**, starting from the top-left corner and moving clockwise.

---

### 📌 Spiral Traversal Order:

Right ➡️

Down ⬇️

Left ⬅️

Up ⬆️

(Repeat)

---

### 🔢 Example:

```java
int[][] matrix = {
    { 1, 2, 3 },
    { 4, 5, 6 },
    { 7, 8, 9 }
};
```

**Spiral Output:**

`1 2 3 6 9 8 7 4 5`

---

### 🔍 Intuition:

Maintain **4 boundaries**:

- `top`, `bottom`, `left`, `right`

As you spiral:

1. Traverse **top** row left to right → `top++`
2. Traverse **right** column top to bottom → `right--`
3. Traverse **bottom** row right to left → `bottom--`
4. Traverse **left** column bottom to top → `left++`

Repeat this until `top > bottom` or `left > right`

---

### ✅ Java Code:

```java
public class SpiralTraversal {

    public static void printSpiral(int[][] matrix) {
        int top = 0;
        int bottom = matrix.length - 1;
        int left = 0;
        int right = matrix[0].length - 1;

        while (top <= bottom && left <= right) {

            // Traverse from Left to Right
            for (int col = left; col <= right; col++) {
                System.out.print(matrix[top][col] + " ");
            }
            top++;

            // Traverse from Top to Bottom
            for (int row = top; row <= bottom; row++) {
                System.out.print(matrix[row][right] + " ");
            }
            right--;

            // Traverse from Right to Left
            if (top <= bottom) {
                for (int col = right; col >= left; col--) {
                    System.out.print(matrix[bottom][col] + " ");
                }
                bottom--;
            }

            // Traverse from Bottom to Top
            if (left <= right) {
                for (int row = bottom; row >= top; row--) {
                    System.out.print(matrix[row][left] + " ");
                }
                left++;
            }
        }
    }

    public static void main(String[] args) {
        int[][] matrix = {
            { 1, 2, 3 },
            { 4, 5, 6 },
            { 7, 8, 9 }
        };
        System.out.print("Spiral Order: ");
        printSpiral(matrix);
    }
}
```

---

### 🧾 Output:

```
Spiral Order: 1 2 3 6 9 8 7 4 5
```

---

### 🧠 Where is this pattern useful?

- UI layout rendering (circular scrolling)
- Image processing
- Snake games/grid-based games
- Matrix-based simulation problems
