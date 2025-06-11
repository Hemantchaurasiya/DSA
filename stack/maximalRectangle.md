# Maximal Rectangle

---

## Problem Overview
Given a 2D binary matrix filled with `'0'`s and `'1'`s, find the largest rectangle containing only `'1'`s and return its area.

**Example:**
```
Input:
[
  ["1","0","1","0","0"],
  ["1","0","1","1","1"],
  ["1","1","1","1","1"],
  ["1","0","0","1","0"]
]
Output: 6
```
**Explanation:**
The largest rectangle of `'1'`s has an area of 6 (covering the rectangle in the middle rows).

---

## Approach 1: Brute Force (Naive)

### **Core Idea**
- Consider all possible rectangles in the matrix.
- For each possible top-left corner, expand to the right and down to compute the maximum rectangle containing `'1'`.

### **Algorithm**
1. Iterate over all cells `(i, j)` as potential top-left corners.
2. For each starting point, expand rightward to define the width.
3. For each width, expand downward to include more rows:
   - Keep track of the minimum height among the included rows to ensure all are `'1'`.
   - Calculate the area: `width * minimum_height`.
4. Keep track of the maximum area encountered.

### **Java Code**
```java
public int maximalRectangle(char[][] matrix) {
    int maxArea = 0;
    int m = matrix.length;
    int n = (m == 0) ? 0 : matrix[0].length;

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == '1') {
                int minHeight = Integer.MAX_VALUE;
                for (int k = j; k < n && matrix[i][k] == '1'; k++) {
                    int height = 0;
                    for (int l = i; l >= 0 && matrix[l][k] == '1'; l--) {
                        height++;
                    }
                    minHeight = Math.min(minHeight, height);
                    int width = k - j + 1;
                    maxArea = Math.max(maxArea, width * minHeight);
                }
            }
        }
    }
    return maxArea;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(m^2 * n^2)* — nested loops over rows and columns, with additional nested loops for height calculation.
- **Space Complexity:** *O(1)* — no extra significant space used.

### **Dry Run**
Input:
```
[
  ["1","0","1"],
  ["1","1","1"],
  ["1","1","0"]
]
```
- The algorithm considers all starting points, expanding right and down, updating the max area accordingly.

---

## Approach 2: Using Largest Rectangle in Histogram per Row

### **Core Idea**
- For each row, treat the row as the base of a histogram where the height of each bar is the count of consecutive `'1'`s above (including current row).
- Compute the largest rectangle in the histogram for each row.
- The maximum among these is the answer.

### **Algorithm**
1. Initialize an array `heights` of size `n` (columns) with all zeros.
2. For each row:
   - Update `heights[j]`:
     - If `matrix[i][j] == '1'`, `heights[j] += 1`.
     - Else, reset `heights[j] = 0`.
   - Use the Largest Rectangle in Histogram algorithm on `heights` to find the maximum rectangle for that row.
3. Keep track of the maximum area across all rows.

### **Java Code**
```java
public int maximalRectangle(char[][] matrix) {
    if (matrix.length == 0) return 0;
    int maxArea = 0;
    int n = matrix[0].length;
    int[] heights = new int[n];

    for (int i = 0; i < matrix.length; i++) {
        for (int j = 0; j < n; j++) {
            if (matrix[i][j] == '1') {
                heights[j]++;
            } else {
                heights[j] = 0;
            }
        }
        maxArea = Math.max(maxArea, largestRectangleInHistogram(heights));
    }
    return maxArea;
}

private int largestRectangleInHistogram(int[] heights) {
    int maxArea = 0;
    Deque<Integer> stack = new ArrayDeque<>();
    for (int i = 0; i <= heights.length; i++) {
        int currentHeight = (i == heights.length) ? 0 : heights[i];
        while (!stack.isEmpty() && currentHeight < heights[stack.peek()]) {
            int height = heights[stack.pop()];
            int width = stack.isEmpty() ? i : i - stack.peek() - 1;
            maxArea = Math.max(maxArea, height * width);
        }
        stack.push(i);
    }
    return maxArea;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(m * n)* — for each row, histogram calculation and largest rectangle computation are O(n).
- **Space Complexity:** *O(n)* — for the `heights` array and the stack.

### **Dry Run**
Input:
```
[
  ["1","0","1"],
  ["1","1","1"],
  ["1","1","0"]
]
```
- For each row, update `heights` and compute max rectangle in histogram.
- For example, after second row:
  - `heights = [3, 2, 3]`
  - Largest rectangle in histogram for `[3, 2, 3]` is 4 (bars with heights 3 and 2).

---

## **Summary Table**

| Approach                                    | Time Complexity | Space Complexity | Comments                                             |
|----------------------------------------------|----------------|------------------|------------------------------------------------------|
| Brute Force                                | O(m^2 * n^2)   | O(1)             | Not efficient for large matrices                     |
| Using Largest Rectangle in Histogram per row | O(m * n)       | O(n)             | Efficient; reduces problem to histogram largest rectangle |

---

## **Final Tips for Interviews**
- The row-by-row histogram approach is preferred due to its efficiency.
- Focus on implementing the Largest Rectangle in Histogram solution well, as it is the core subproblem.
- Remember to update the heights array after each row.
- Practice edge cases like empty matrices, matrices with all `'0'` or all `'1'`.

---

**Happy coding!**
