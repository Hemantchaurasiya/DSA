# Search a 2D Matrix

This problem involves determining whether a target value exists within a 2D matrix that satisfies certain sorted properties. The matrix is usually sorted in row-wise and/or column-wise order, enabling efficient search strategies.

---

## Problem Statement

Given an `m x n` matrix where:

- Integers in each row are sorted from left to right.
- The first integer of each row is greater than the last integer of the previous row.

Write an algorithm to **search for a target value** in the matrix. Return `true` if the target exists, otherwise `false`.

---

## Approach 1: Brute Force

### Core Idea

Traverse every element in the matrix sequentially to check if it matches the target.

### Algorithm

1. Iterate through each row of the matrix.
2. For each row, iterate through each element.
3. Check if the current element equals the target.
4. If found, return `true`.
5. If after traversing all elements, the target is not found, return `false`.

### Java Code

```java
public boolean searchMatrix(int[][] matrix, int target) {
    for (int[] row : matrix) {
        for (int num : row) {
            if (num == target) {
                return true;
            }
        }
    }
    return false;
}

```

### Complexity Analysis

- **Time Complexity:** `O(m * n)`
Because in the worst case, you scan all elements.
- **Space Complexity:** `O(1)`
No extra space used.

### Dry Run

| Input Matrix | Target | Step-by-step |
| --- | --- | --- |
| [[1, 3, 5], [7, 9, 11]] | 9 | Check 1, 3, 5, 7, 9 → found at position (1,1) → return `true` |

---

## Approach 2: Binary Search on Each Row (Row-wise Binary Search)

### Core Idea

Since each row is sorted, perform binary search on each row to find the target.

### Algorithm

1. Loop through each row in the matrix.
2. For each row, perform binary search:
    - Initialize `left = 0`, `right = n-1`.
    - While `left <= right`:
        - Find `mid = left + (right - left) / 2`.
        - If `row[mid] == target`, return `true`.
        - If `row[mid] < target`, move `left` to `mid + 1`.
        - Else, move `right` to `mid - 1`.
3. If no match found after searching all rows, return `false`.

### Java Code

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

### Complexity Analysis

- **Time Complexity:** `O(m * log n)`
For each of the `m` rows, binary search takes `O(log n)`.
- **Space Complexity:** `O(1)`

### Dry Run

| Input Matrix | Target | Step-by-step |
| --- | --- | --- |
| [[1, 3, 5], [7, 9, 11]] | 9 | Row 1: binary search for 9 → not found; Row 2: binary search for 9 → found at index 1 → return `true` |

---

## Approach 3: Treat the 2D Matrix as a Sorted 1D Array (Optimal Solution - Binary Search)

### Core Idea

Since the matrix's properties are such that:

- The entire matrix can be viewed as a sorted 1D array.
- The element at `matrix[row][col]` corresponds to `matrix[mid / n][mid % n]` in a 1D representation.

Perform binary search over the entire matrix considering it as a flattened sorted array.

### Algorithm

1. Let `rows = m`, `cols = n`.
2. Initialize `left = 0`, `right = m * n - 1`.
3. While `left <= right`:
    - Compute `mid = left + (right - left) / 2`.
    - Convert `mid` to 2D indices:
        - `row = mid / n`
        - `col = mid % n`
    - Compare `matrix[row][col]` with target:
        - If equal, return `true`.
        - If less, move `left` to `mid + 1`.
        - If greater, move `right` to `mid - 1`.
4. Return `false` if the element is not found.

### Java Code

```java
public boolean searchMatrix(int[][] matrix, int target) {
    if (matrix == null || matrix.length == 0 || matrix[0].length == 0) {
        return false;
    }
    int m = matrix.length;
    int n = matrix[0].length;
    int left = 0, right = m * n - 1;

    while (left <= right) {
        int mid = left + (right - left) / 2;
        int midValue = matrix[mid / n][mid % n];

        if (midValue == target) {
            return true;
        } else if (midValue < target) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    return false;
}

```

### Complexity Analysis

- **Time Complexity:** `O(log (m * n))`
Binary search over the total number of elements.
- **Space Complexity:** `O(1)`

### Dry Run

| Input Matrix | Target | Walkthrough |
| --- | --- | --- |
| [[1, 3, 5], [7, 9, 11]] | 9 | Total elements = 6; `left=0`, `right=5` |
| Mid calculation: `mid=2` (index 2) | `matrix[2/3][2%3]=matrix[0][2]=5` | 5 < 9 → `left=3` |
| Next: `mid=4` (index 4) | `matrix[4/3][4%3]=matrix[1][1]=9` | 9 == target → return `true` |

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Best Use Case |
| --- | --- | --- | --- | --- |
| Brute Force | Linear search through all elements | `O(m*n)` | `O(1)` | Small matrices, quick implementation |
| Row-wise Binary Search | Binary search in each row | `O(m*log n)` | `O(1)` | Large matrices with sorted rows |
| Flattened Binary Search | Treat matrix as sorted 1D array | `O(log(m*n))` | `O(1)` | Very large matrices, optimal search |

---

## Final Recommendations

- For **small matrices**, brute force is sufficient.
- For **larger matrices**, prefer **binary search approaches**.
- The **most optimal** is the **flattened binary search**, leveraging the sorted properties of the entire matrix.

---

Feel free to practice with different matrix inputs and target values to master the problem!
