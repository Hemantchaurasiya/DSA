# Count Negative Numbers in a Sorted Matrix

This problem involves counting the number of negative numbers in a row-wise and column-wise sorted matrix.

---

## Problem Statement

Given a **m x n** matrix where each row and column is sorted in non-increasing order (or sorted ascending, depending on the problem variation), count the total number of negative numbers in the matrix.

---

## Approach 1: Brute Force

### Core Idea

Iterate through each element of the matrix and count how many are negative.

### Algorithm

1. Initialize a count variable to 0.
2. Loop through each row.
3. Loop through each element in the row.
4. If an element is negative, increment the count.
5. Return the count after traversing the entire matrix.

### Java Code

```java
public int countNegatives(int[][] grid) {
    int count = 0;
    for (int[] row : grid) {
        for (int num : row) {
            if (num < 0) {
                count++;
            }
        }
    }
    return count;
}

```

### Complexity Analysis

- **Time Complexity:** **O(m * n)** - Must traverse all elements.
- **Space Complexity:** **O(1)** - Uses only a few variables; no extra space.

### Dry Run

- **Input:**
    
    ```
    grid = [
      [4, 3, 2, -1],
      [3, 2, -1, -2],
      [1, 1, -1, -3]
    ]
    
    ```
    
- **Execution:**
    - Check each element:
        - Row 1: 4, 3, 2, -1 → count = 1 (for -1)
        - Row 2: 3, 2, -1, -2 → count = 3 (additional for -1 and -2)
        - Row 3: 1, 1, -1, -3 → count = 5 (additional for -1 and -3)
- **Result:** 5

---

## Approach 2: Better Solution Using Binary Search (Row-wise)

### Core Idea

Since each row is sorted, we can binary search in each row to find the first negative number. All elements after that index in the row are negative, so counting is straightforward.

### Algorithm

1. Initialize a total count to 0.
2. For each row:
    - Use binary search to find the first negative number's index.
    - The count of negatives in that row = total elements in the row - index of first negative.
3. Sum these counts for all rows.

### Java Code

```java
public int countNegatives(int[][] grid) {
    int totalNegatives = 0;
    for (int[] row : grid) {
        int left = 0, right = row.length - 1;
        int firstNegativeIndex = row.length; // default if no negative
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (row[mid] < 0) {
                firstNegativeIndex = mid;
                right = mid - 1; // move left to find first occurrence
            } else {
                left = mid + 1; // move right
            }
        }
        totalNegatives += row.length - firstNegativeIndex;
    }
    return totalNegatives;
}

```

### Complexity Analysis

- **Time Complexity:** **O(m log n)**, where `m` is rows and `n` is columns, due to binary search in each row.
- **Space Complexity:** **O(1)**, as only a few variables are used.

### Dry Run

- **Input:**
    
    ```
    grid = [
      [4, 3, 2, -1],
      [3, 2, -1, -2],
      [1, 1, -1, -3]
    ]
    
    ```
    
- **Execution:**
    - Row 1: Binary search finds first negative at index 3, negatives = 4 - 3 = 1
    - Row 2: First negative at index 2, negatives = 4 - 2 = 2
    - Row 3: First negative at index 2, negatives = 4 - 2 = 2
- Total negatives = 1 + 2 + 2 = 5

---

## Approach 3: Most Optimal Solution Using Two Pointers (O(m + n))

### Core Idea

Since the matrix is sorted row-wise and column-wise, we can start from the top-right corner and move left or down based on the value:

- If the current element is negative, then all elements below in that column are also negative, so add `(number of rows remaining)` to the count and move left.
- If the current element is non-negative, move down to the next row.

### Algorithm

1. Initialize `row = 0`, `col = n - 1`, `count = 0`.
2. While `row < m` and `col >= 0`:
    - If `matrix[row][col] < 0`:
        - All elements below in this column are negative, so add `(rowCount = m - row)` to the count.
        - Move left (`col--`).
    - Else:
        - Move down (`row++`).
3. Return `count`.

### Java Code

```java
public int countNegatives(int[][] grid) {
    int m = grid.length;
    int n = grid[0].length;
    int row = 0, col = n - 1, count = 0;
    while (row < m && col >= 0) {
        if (grid[row][col] < 0) {
            count += (m - row);
            col--;
        } else {
            row++;
        }
    }
    return count;
}

```

### Complexity Analysis

- **Time Complexity:** **O(m + n)**, since each element is visited at most once.
- **Space Complexity:** **O(1)**, only counting variables are used.

### Dry Run

- **Input:**
    
    ```
    grid = [
      [4, 3, 2, -1],
      [3, 2, -1, -2],
      [1, 1, -1, -3]
    ]
    
    ```
    
- **Execution:**
    - Start at (row=0, col=3): value = -1 → negatives = 3 - 0 = 3, move left (col=2).
    - (0,2): value=2, non-negative → move down (row=1).
    - (1,2): value=-1 → negatives += (3 - 1) = 2, move left (col=1).
    - (1,1): value=2, non-negative → move down (row=2).
    - (2,1): value=1, non-negative → move down (row=3), stop as row=3 >= m=3.
- **Total count:** 3 + 2 = 5.

---

# Summary of Approaches

| Approach | Core Idea | Time Complexity | Space Complexity | Suitable For |
| --- | --- | --- | --- | --- |
| Brute Force | Check all elements | O(m * n) | O(1) | Small matrices or quick implementation |
| Binary Search per row | Use row-wise sorted property | O(m log n) | O(1) | Larger matrices with sorted rows |
| Two Pointers | Exploit matrix sorted property from top-right | O(m + n) | O(1) | Large matrices, optimal performance |

---

## Final Notes

- Use the **Two Pointers** approach for maximum efficiency.
- Binary search is a good compromise when the matrix is large but still sorted.
- Brute force is easiest to implement but not optimal for large inputs.

---

**Happy Coding!**
