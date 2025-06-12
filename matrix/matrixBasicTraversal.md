# Matrix Basic Traversals

### 🔹 A. Row-wise Traversal

Go through each **row** one by one, and within each row, go through each **column**.

```java
for (int i = 0; i < matrix.length; i++) {
    for (int j = 0; j < matrix[0].length; j++) {
        System.out.print(matrix[i][j] + " ");
    }
    System.out.println();
}
```

---

### 🔹 B. Column-wise Traversal

Go through each **column**, and for each column, go through each **row**.

```java
for (int j = 0; j < matrix[0].length; j++) {
    for (int i = 0; i < matrix.length; i++) {
        System.out.print(matrix[i][j] + " ");
    }
    System.out.println();
}
```

---

### 🔹 C. Reverse Row-wise Traversal

Start from the **last row** and go upwards.

```java
for (int i = matrix.length - 1; i >= 0; i--) {
    for (int j = 0; j < matrix[0].length; j++) {
        System.out.print(matrix[i][j] + " ");
    }
    System.out.println();
}
```

---

### 🔹 D. Reverse Column-wise Traversal

Start from the **last column** and move to the first.

```java
for (int j = matrix[0].length - 1; j >= 0; j--) {
    for (int i = 0; i < matrix.length; i++) {
        System.out.print(matrix[i][j] + " ");
    }
    System.out.println();
}
```

---

### 🔹 E. Traversing Only First Row or Last Column, etc.

```java
// First row
for (int j = 0; j < matrix[0].length; j++) {
    System.out.print(matrix[0][j] + " ");
}

// Last column
for (int i = 0; i < matrix.length; i++) {
    System.out.print(matrix[i][matrix[0].length - 1] + " ");
}
```

---

### 🧠 Use Cases:

- Preprocessing
- Counting values
- Printing matrix
- Applying constraints row-wise or column-wise
- Foundation for more advanced problems like Spiral traversal or DFS/BFS

---

### 🧪 Example Input:

```java
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
```

**Row-wise Output:**

`1 2 3`

`4 5 6`

`7 8 9`

**Column-wise Output:**

`1 4 7`

`2 5 8`

`3 6 9`

Pattern 2: **Diagonal Traversal**

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
    1
    2 4
    3 5 7
    6 8
    9
    ```
    
- 🔹 All ↙ Diagonals:
    
    ```
    3
    2 6
    1 5 9
    4 8
    7
    ```
