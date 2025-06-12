# Search in Sorted Matrix II

This problem involves searching for a target value in a 2D matrix where each row and each column is sorted in ascending order. The goal is to determine if the target exists in the matrix efficiently.

---

## Problem Statement

Given an `m x n` matrix where each row and column is sorted in ascending order, and a target value, determine if the target exists in the matrix.

---

## Approaches to Solve the Problem

### 1. Brute Force

---

### **Algorithm:**

- Traverse every element in the matrix.
- Check if any element matches the target.
- If found, return `true`.
- If traversal completes without finding the target, return `false`.

---

### **Java Code:**

```java
public boolean searchMatrix(int[][] matrix, int target) {
    int m = matrix.length;
    int n = matrix[0].length;
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == target) {
                return true;
            }
        }
    }
    return false;
}

```

---

### **Complexity Analysis:**

- **Time Complexity:O(m * n)**, since every element is checked in the worst case.
- **Space Complexity:O(1)**, no extra space is used aside from variables.

---

### **Dry Run:**

**Input:**

```
matrix = [
  [1, 4, 7],
  [2, 5, 8],
  [3, 6, 9]
]
target = 5

```

**Execution:**

- Check `matrix[0][0] = 1` → not match.
- Check `matrix[0][1] = 4` → not match.
- Check `matrix[0][2] = 7` → not match.
- Check `matrix[1][0] = 2` → not match.
- Check `matrix[1][1] = 5` → **match!**, return `true`.

---

---

### 2. Better Solution: Binary Search on Each Row

---

### **Algorithm:**

- For each row:
    - Perform binary search to find the target.
    - If found, return `true`.
- Continue until all rows are checked or target is found.

---

### **Java Code:**

```java
public boolean searchMatrix(int[][] matrix, int target) {
    for (int[] row : matrix) {
        int left = 0, right = row.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (row[mid] == target) {
                return true;
            } else if (row[mid] < target) {
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
    }
    return false;
}

```

---

### **Complexity Analysis:**

- **Time Complexity:O(m * log n)**, where `m` is number of rows, `n` is number of columns.
Each row performs a binary search, which is `O(log n)`.
- **Space Complexity:O(1)**, no extra space apart from variables.

---

### **Dry Run:**

**Input:**

```
matrix = [
  [1, 4, 7],
  [2, 5, 8],
  [3, 6, 9]
]
target = 6

```

**Execution:**

- Row 0: `[1, 4, 7]` binary search:
    - mid = 1 (value 4), target 6 > 4 → search right.
    - mid = 2 (value 7), target 6 < 7 → search left.
    - No match in row 0.
- Row 1: `[2, 5, 8]` binary search:
    - mid = 1 (value 5), target 6 > 5 → search right.
    - mid = 2 (value 8), target 6 < 8 → search left.
    - No match in row 1.
- Row 2: `[3, 6, 9]` binary search:
    - mid = 1 (value 6), **match!**, return `true`.

---

---

### 3. **Optimal Solution: Search from Top-Right Corner**

---

### **Algorithm:**

- Initialize `row = 0` and `col = n - 1` (top-right element).
- Loop while `row < m` and `col >= 0`:
    - If `matrix[row][col] == target` → return `true`.
    - If `matrix[row][col] > target` → move left (`col--`).
    - Else (`matrix[row][col] < target`) → move down (`row++`).
- If the loop ends without finding the target, return `false`.

**Intuition:**

- The element at top-right is the largest in its row and smallest in its column.
- Moving left reduces the value, moving down increases the value, guiding us towards the target efficiently.

---

### **Java Code:**

```java
public boolean searchMatrix(int[][] matrix, int target) {
    int m = matrix.length;
    int n = matrix[0].length;
    int row = 0, col = n - 1;

    while (row < m && col >= 0) {
        if (matrix[row][col] == target) {
            return true;
        } else if (matrix[row][col] > target) {
            col--;
        } else {
            row++;
        }
    }
    return false;
}

```

---

### **Complexity Analysis:**

- **Time Complexity:O(m + n)**, because in the worst case, we move either left or down in each step.
- **Space Complexity:O(1)**, no extra space used.

---

### **Dry Run:**

**Input:**

```
matrix = [
  [1, 4, 7],
  [2, 5, 8],
  [3, 6, 9]
]
target = 6

```

**Execution:**

- Start at `matrix[0][2] = 7`:
    - 7 > 6 → move left (`col--`): now `col=1`.
- Now at `matrix[0][1] = 4`:
    - 4 < 6 → move down (`row++`): now `row=1`.
- Now at `matrix[1][1] = 5`:
    - 5 < 6 → move down (`row++`): now `row=2`.
- Now at `matrix[2][1] = 6`:
    - Match! return `true`.

---

## Summary

| Approach | Time Complexity | Space Complexity | Pros | Cons |
| --- | --- | --- | --- | --- |
| Brute Force | O(m * n) | O(1) | Simple to implement | Inefficient for large matrices |
| Binary Search on Each Row | O(m * log n) | O(1) | More efficient than brute force | Still not optimal |
| Search from Top-Right Corner | O(m + n) | O(1) | Most optimal, efficient | Slightly complex logic |

---

## Final Notes:

- For large matrices, the **search from top-right corner** is the most efficient and preferred approach.
- Always understand the matrix's sorted properties to choose the optimal solution.
- Practice dry runs with different matrix sizes and targets to build intuition.

---

**Happy Coding!**
