# Count Occurrences in Sorted Matrix

This problem involves counting the number of times a target element appears in a **sorted 2D matrix** where each row and each column is sorted in non-decreasing order.

---

## Problem Statement

Given a **rows x columns** matrix where each row and column is sorted in non-decreasing order, and a target value, find the total number of occurrences of that target in the matrix.

---

## Approach 1: Brute Force

### Algorithm

- Traverse every element in the matrix.
- For each element, check if it is equal to the target.
- Increment a counter whenever the element matches the target.
- Return the counter after traversing the entire matrix.

### Java Code

```java
public int countOccurrencesBruteForce(int[][] matrix, int target) {
    int count = 0;
    int rows = matrix.length;
    int cols = matrix[0].length;

    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (matrix[i][j] == target) {
                count++;
            }
        }
    }
    return count;
}

```

### Complexity Analysis

- **Time Complexity:** `O(R * C)` where `R` is the number of rows and `C` is the number of columns, because we check every element.
- **Space Complexity:** `O(1)` as only a few variables are used.

### Dry Run

Suppose:

```
matrix = [
  [1, 2, 3],
  [2, 3, 4],
  [3, 4, 5]
]
target = 3

```

| Step | i | j | matrix[i][j] | Match? | count |
| --- | --- | --- | --- | --- | --- |
| 1 | 0 | 0 | 1 | No | 0 |
| 2 | 0 | 1 | 2 | No | 0 |
| 3 | 0 | 2 | 3 | Yes | 1 |
| 4 | 1 | 0 | 2 | No | 1 |
| 5 | 1 | 1 | 3 | Yes | 2 |
| 6 | 1 | 2 | 4 | No | 2 |
| 7 | 2 | 0 | 3 | Yes | 3 |
| 8 | 2 | 1 | 4 | No | 3 |
| 9 | 2 | 2 | 5 | No | 3 |

Result: **3**

---

## Approach 2: Binary Search in Each Row (Better Solution)

### Algorithm

- Since each row is sorted, perform a binary search to find the **first** and **last** occurrence of the target in each row.
- For each row:
    - Use binary search to find the first occurrence index of target.
    - Use binary search to find the last occurrence index of target.
    - The number of occurrences in that row is `(lastIndex - firstIndex + 1)` if the target exists.
- Sum occurrences across all rows.

### Java Code

```java
public int countOccurrencesBinarySearch(int[][] matrix, int target) {
    int totalCount = 0;
    int rows = matrix.length;
    int cols = matrix[0].length;

    for (int i = 0; i < rows; i++) {
        int firstIdx = firstOccurrence(matrix[i], target);
        int lastIdx = lastOccurrence(matrix[i], target);
        if (firstIdx != -1 && lastIdx != -1) {
            totalCount += (lastIdx - firstIdx + 1);
        }
    }
    return totalCount;
}

private int firstOccurrence(int[] row, int target) {
    int low = 0, high = row.length - 1;
    int result = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (row[mid] == target) {
            result = mid;
            high = mid - 1; // look for earlier occurrence
        } else if (row[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return result;
}

private int lastOccurrence(int[] row, int target) {
    int low = 0, high = row.length - 1;
    int result = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (row[mid] == target) {
            result = mid;
            low = mid + 1; // look for later occurrence
        } else if (row[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return result;
}

```

### Complexity Analysis

- **Time Complexity:** `O(R * log C)` where `R` is the number of rows, and `C` is the number of columns, since binary search is performed per row.
- **Space Complexity:** `O(1)` (excluding input storage), as only variables are used.

### Dry Run

Using the same matrix:

```
matrix = [
  [1, 2, 3],
  [2, 3, 4],
  [3, 4, 5]
]
target = 3

```

- For each row:
    - Row 0: `[1, 2, 3]`
        - First occurrence of 3: index 2
        - Last occurrence of 3: index 2
        - Count: 1
    - Row 1: `[2, 3, 4]`
        - First occurrence: 1
        - Last occurrence: 1
        - Count: 1
    - Row 2: `[3, 4, 5]`
        - First occurrence: 0
        - Last occurrence: 0
        - Count: 1

Total count = 3

---

## Approach 3: Optimal Solution Using "Search from Top-Right Corner"

### Algorithm

- Initialize two pointers:
    - `row = 0` (top row)
    - `col = C - 1` (rightmost column)
- While `row < R` and `col >= 0`:
    - If `matrix[row][col] == target`:
        - Count all occurrences in this position (if duplicates exist).
        - Move down to next row (`row++`) and left (`col--`) to check for more.
    - If `matrix[row][col] > target`:
        - Move left (`col--`) to find smaller values.
    - Else (`matrix[row][col] < target`):
        - Move down (`row++`) to find larger values.
- Count the total occurrences during traversal.

### Note:

- Because matrix rows and columns are sorted, this approach efficiently finds all occurrences in **O(R + C)** time by exploiting the sorted properties.

### Java Code

```java
public int countOccurrencesOptimal(int[][] matrix, int target) {
    int count = 0;
    int rows = matrix.length;
    int cols = matrix[0].length;
    int row = 0;
    int col = cols - 1;

    while (row < rows && col >= 0) {
        if (matrix[row][col] == target) {
            // Count all duplicates in this position
            int tempCol = col;
            // Count left duplicates
            while (tempCol >= 0 && matrix[row][tempCol] == target) {
                count++;
                tempCol--;
            }
            // Move down to next row
            row++;
        } else if (matrix[row][col] > target) {
            col--;
        } else {
            row++;
        }
    }
    return count;
}

```

### Complexity Analysis

- **Time Complexity:** `O(R + C)` because each element is visited at most once.
- **Space Complexity:** `O(1)` as only variables are used.

### Dry Run

Using previous example:

```
matrix = [
  [1, 2, 3],
  [2, 3, 4],
  [3, 4, 5]
]
target = 3

```

- Start at `(0, 2)`: value = 3 → count++, move down, left
    - Count all duplicates in same row at this position: 1 (the current position)
    - Move to `(1, 1)`: value = 3 → count++, move down, left
    - Count duplicates at `(1, 1)`: 1
    - Move to `(2, 0)`: value = 3 → count++, move down (out of bounds)
    - Total count = 3

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Best Use Case |
| --- | --- | --- | --- | --- |
| Brute Force | Check every element | `O(R*C)` | `O(1)` | Small matrices, quick implementation |
| Binary Search per Row | Use binary search on each row | `O(R*log C)` | `O(1)` | Larger matrices, when rows are large |
| Search from Top-Right | Exploit sorted properties | `O(R + C)` | `O(1)` | Very large matrices, efficient solution |

---

This comprehensive guide covers multiple approaches to **Count Occurrences in a Sorted Matrix**, with explanations, code, and dry runs to solidify understanding.
