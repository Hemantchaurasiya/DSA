# Search in Sorted Matrix I

## Problem Statement

Given an `n x m` matrix where each row and each column is sorted in ascending order, determine if a target value exists in the matrix.

---

## 1. Brute Force

### Algorithm

- Iterate over every element in the matrix.
- Check if the current element equals the target.
- Return `true` if found; otherwise, after checking all elements, return `false`.

### Java Code

```java
public boolean searchMatrixBruteForce(int[][] matrix, int target) {
    int rows = matrix.length;
    int cols = matrix[0].length;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (matrix[i][j] == target) {
                return true;
            }
        }
    }
    return false;
}

```

### Complexity Analysis

- **Time Complexity:** `O(n * m)`
Because every element is checked once.
- **Space Complexity:** `O(1)`
No extra space used besides variables.

### Dry Run

- Input:
    
    ```
    matrix = [
      [1, 4, 7],
      [2, 5, 8],
      [3, 6, 9]
    ], target = 5
    
    ```
    
- Process:
    - Check (0,0): 1 ≠ 5
    - Check (0,1): 4 ≠ 5
    - Check (0,2): 7 ≠ 5
    - Check (1,0): 2 ≠ 5
    - Check (1,1): 5 == 5 → **Found!**

---

## 2. Better Solution: Row-wise Binary Search

### Algorithm

- For each row:
    - Perform binary search to find the target.
    - If found, return `true`.
- If none of the rows contains the target, return `false`.

### Java Code

```java
public boolean searchMatrixBinarySearch(int[][] matrix, int target) {
    int rows = matrix.length;
    int cols = matrix[0].length;

    for (int i = 0; i < rows; i++) {
        int low = 0;
        int high = cols - 1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (matrix[i][mid] == target) {
                return true;
            } else if (matrix[i][mid] < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
    }
    return false;
}

```

### Complexity Analysis

- **Time Complexity:** `O(n log m)`
For each of the `n` rows, binary search takes `O(log m)`.
- **Space Complexity:** `O(1)`
No extra space apart from variables.

### Dry Run

- Input:
    
    ```
    matrix = [
      [1, 4, 7],
      [2, 5, 8],
      [3, 6, 9]
    ], target = 5
    
    ```
    
- Process:
    - Row 0: Binary search for 5 → check mid (1): 4 < 5 → search right → check mid (2): 7 > 5 → search left → no match.
    - Row 1: Binary search for 5:
        - mid (1): 5 == 5 → **Found!**

---

## 3. Optimal Solution: Search from Top-Right Corner

### Algorithm

- Start from the **top-right corner** of the matrix.
- While the current position is valid:
    - If the current element equals the target, return `true`.
    - If the current element is greater than the target, move **left**.
    - If the current element is less than the target, move **down**.
- Continue until the element is found or indices go out of bounds.

### Intuition

- Because the matrix is sorted row-wise and column-wise:
    - Moving left decreases the value.
    - Moving down increases the value.
- This allows pruning of the search space efficiently.

### Java Code

```java
public boolean searchMatrixOptimal(int[][] matrix, int target) {
    int rows = matrix.length;
    int cols = matrix[0].length;
    int row = 0;
    int col = cols - 1;

    while (row < rows && col >= 0) {
        if (matrix[row][col] == target) {
            return true;
        } else if (matrix[row][col] > target) {
            col--; // move left
        } else {
            row++; // move down
        }
    }
    return false;
}

```

### Complexity Analysis

- **Time Complexity:** `O(n + m)`
In the worst case, the traversal moves at most `n` times down and `m` times left.
- **Space Complexity:** `O(1)`
No extra space used.

### Dry Run

- Input:
    
    ```
    matrix = [
      [1, 4, 7],
      [2, 5, 8],
      [3, 6, 9]
    ], target = 5
    
    ```
    
- Process:
    - Start at (0, 2): 7
        - 7 > 5 → move left to (0, 1): 4
    - (0, 1): 4 < 5 → move down to (1, 1): 5
    - (1, 1): 5 == target → **Found!**

---

## **Summary Table**

| Approach | Algorithm Type | Time Complexity | Space Complexity | Suitability |
| --- | --- | --- | --- | --- |
| Brute Force | Naive iteration | `O(n*m)` | `O(1)` | Small matrices or quick checks |
| Binary Search Row-wise | Binary search on each row | `O(n log m)` | `O(1)` | Moderate-sized matrices |
| Top-Right Search | Greedy from corner | `O(n + m)` | `O(1)` | Large matrices, optimal for interview |

---

## Final Notes

- The **most optimal approach** for this problem is the **search from the top-right corner**, utilizing the sorted properties effectively.
- Always consider the problem constraints to choose the best approach.
- Practice dry runs with different matrix sizes and target positions to strengthen understanding.

---

**Happy Revising!**
