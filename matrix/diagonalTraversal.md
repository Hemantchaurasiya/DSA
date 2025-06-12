# ✅ Diagonal Traversal

Diagonal traversals are used in problems like:

- Matrix Zigzag print
- Anti-diagonal sum
- Lower and upper triangle processing
- Dynamic programming (e.g., DP table filling diagonally)
- Diagonal BFS traversal in games or puzzles

---

## 🔹 Type A: **Primary Diagonal (Top-left to Bottom-right)**

Elements where `row == col`

### 📌 Code:

```java
for (int i = 0; i < matrix.length; i++) {
    System.out.print(matrix[i][i] + " ");
}
```

---

## 🔹 Type B: **Secondary Diagonal (Top-right to Bottom-left)**

Elements where `row + col == n - 1` (where `n` is the size of the square matrix)

### 📌 Code:

```java
int n = matrix.length;
for (int i = 0; i < n; i++) {
    System.out.print(matrix[i][n - 1 - i] + " ");
}
```

---

## 🔹 Type C: **All Diagonals – Top-left to Bottom-right (↘️)**

Start from every element in the **first row** and **first column**

### 📌 Code:

```java
int rows = matrix.length;
int cols = matrix[0].length;

// From top row
for (int col = 0; col < cols; col++) {
    int i = 0, j = col;
    while (i < rows && j < cols) {
        System.out.print(matrix[i][j] + " ");
        i++;
        j++;
    }
    System.out.println();
}

// From first column (excluding the [0][0] as it’s already covered)
for (int row = 1; row < rows; row++) {
    int i = row, j = 0;
    while (i < rows && j < cols) {
        System.out.print(matrix[i][j] + " ");
        i++;
        j++;
    }
    System.out.println();
}
```

---

## 🔹 Type D: **All Anti-Diagonals – Top-right to Bottom-left (↙️)**

Start from top row and last column, then first column of remaining rows.

### 📌 Code:

```java
int rows = matrix.length;
int cols = matrix[0].length;

// From top row
for (int col = cols - 1; col >= 0; col--) {
    int i = 0, j = col;
    while (i < rows && j < cols) {
        System.out.print(matrix[i][j] + " ");
        i++;
        j++;
    }
    System.out.println();
}

// From first column (excluding top element already covered)
for (int row = 1; row < rows; row++) {
    int i = row, j = 0;
    while (i < rows && j < cols) {
        System.out.print(matrix[i][j] + " ");
        i++;
        j++;
    }
    System.out.println();
}
```

---

## 🧪 Example Input:

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
```

- 🔹 Primary Diagonal: `1 5 9`
- 🔹 Secondary Diagonal: `3 5 7`
- 🔹 All ↘ Diagonals:
    
    ```
    CopyEdit
    1
    2 4
    3 5 7
    6 8
    9
    
    ```
    
- 🔹 All ↙ Diagonals:
    
    ```
    CopyEdit
    3
    2 6
    1 5 9
    4 8
    7
    
    ```
