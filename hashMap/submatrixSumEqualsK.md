# Submatrix Sum Equals K

---

## Problem Overview

Given a 2D matrix of integers and an integer `k`, find the number of **submatrices** (contiguous rectangular regions) that sum to `k`.

**Example:**
```plaintext
Input:
matrix = [
  [1, -1],
  [-1, 1]
], k = 0

Output: 5

Explanation: The submatrices that sum to 0 are:
- Each individual element that equals 0 (none here)
- Submatrix [[1, -1], [-1, 1]] (the entire matrix)
- 4 submatrices of size 1x2 or 2x1 summing to zero, etc.
```

---

## Approach 1: Brute Force

### **Core Idea**
Examine every possible submatrix and calculate its sum. Count how many sum to `k`.

### **Algorithm**
1. Enumerate all possible top-left corners `(r1, c1)` of submatrices.
2. For each `(r1, c1)`, enumerate all possible bottom-right corners `(r2, c2)` such that `r2 >= r1` and `c2 >= c1`.
3. For each submatrix defined by `(r1, c1)` and `(r2, c2)`:
   - Calculate the sum of all elements within this submatrix.
   - Check if the sum equals `k`.
   - Increment count if true.
4. Return the total count.

**Note:** Calculating the sum for each submatrix naively takes `O((r2 - r1 + 1) * (c2 - c1 + 1))`, leading to high complexity.

---

### **Java Code**
```java
public class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int k) {
        int rows = matrix.length, cols = matrix[0].length, count = 0;
        
        for (int r1 = 0; r1 < rows; r1++) {
            for (int c1 = 0; c1 < cols; c1++) {
                for (int r2 = r1; r2 < rows; r2++) {
                    for (int c2 = c1; c2 < cols; c2++) {
                        int sum = 0;
                        for (int r = r1; r <= r2; r++) {
                            for (int c = c1; c <= c2; c++) {
                                sum += matrix[r][c];
                            }
                        }
                        if (sum == k) {
                            count++;
                        }
                    }
                }
            }
        }
        return count;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(R^2 * C^2 * R * C)` = `O(R^3 * C^3)` in the worst case, due to nested loops and sum calculation.
- **Space Complexity:**  
  `O(1)` extra space, as we only use variables for counting and sums.

---

## Approach 2: Prefix Sum Optimization

### **Core Idea**
Precompute prefix sums to efficiently calculate submatrix sums in constant time, reducing overall complexity.

### **Algorithm**
1. Construct a `prefixSum` matrix where `prefixSum[r][c]` contains sum of elements from `(0,0)` to `(r,c)`.
2. For each pair of rows `(r1, r2)`:
   - Compress the 2D problem into a 1D problem by summing columns between `r1` and `r2`.
   - Use a hash map to count subarrays with sum `k` in this compressed array.
3. Sum the counts for all row pairs.

### **Java Code**
```java
import java.util.*;

public class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int k) {
        int rows = matrix.length, cols = matrix[0].length, count = 0;

        // Build prefix sum for each row
        for (int r = 0; r < rows; r++) {
            for (int c = 1; c < cols; c++) {
                matrix[r][c] += matrix[r][c - 1];
            }
        }

        // Fix start and end rows
        for (int c1 = 0; c1 < cols; c1++) {
            for (int c2 = c1; c2 < cols; c2++) {
                Map<Integer, Integer> sumFreq = new HashMap<>();
                sumFreq.put(0, 1);
                int currSum = 0;

                for (int r = 0; r < rows; r++) {
                    int rowSum = matrix[r][c2] - (c1 > 0 ? matrix[r][c1 - 1] : 0);
                    currSum += rowSum;
                    count += sumFreq.getOrDefault(currSum - k, 0);
                    sumFreq.put(currSum, sumFreq.getOrDefault(currSum, 0) + 1);
                }
            }
        }

        return count;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(R^2 * C^2)` due to nested row and column pairs, with each step doing `O(R)` for the compressed sums.
- **Space Complexity:**  
  `O(R * C)` for the prefix sums and hash maps.

---

## Approach 3: 2D Prefix Sum + Hash Map (Most Optimal)

### **Core Idea**
Reduce the 2D problem to multiple 1D subarray sum problems by fixing pairs of rows and using prefix sums with hash maps.

### **Algorithm**
1. Loop over all pairs of rows `(r1, r2)`:
   - Create a `colSum` array to store the sum of elements between rows `r1` and `r2` for each column.
2. Use a hash map to count subarrays in `colSum` that sum to `k`:
   - Maintain a running sum.
   - For each position, check if `runningSum - k` has been seen before.
   - Increment count accordingly.
3. Sum all counts obtained for each row pair.

### **Java Code**
```java
import java.util.*;

public class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int k) {
        int rows = matrix.length, cols = matrix[0].length, count = 0;

        for (int r1 = 0; r1 < rows; r1++) {
            int[] colSum = new int[cols];
            for (int r2 = r1; r2 < rows; r2++) {
                for (int c = 0; c < cols; c++) {
                    colSum[c] += matrix[r2][c];
                }
                count += subarraySumEqualsK(colSum, k);
            }
        }
        return count;
    }

    private int subarraySumEqualsK(int[] nums, int k) {
        Map<Integer, Integer> sumFreq = new HashMap<>();
        sumFreq.put(0, 1);
        int sum = 0, count = 0;
        for (int num : nums) {
            sum += num;
            count += sumFreq.getOrDefault(sum - k, 0);
            sumFreq.put(sum, sumFreq.getOrDefault(sum, 0) + 1);
        }
        return count;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(R^2 * C)` because for each pair of rows, we process the columns once.
- **Space Complexity:**  
  `O(C)` for the hash map used in subarray sum counting.

---

## Dry Run: Step-by-step Example for Approach 3

**Input:**
```plaintext
matrix = [
  [0, 1, 0],
  [1, 1, 1],
  [0, 1, 0]
], k = 0
```

### Step 1: Fix row `r1=0`, iterate over `r2`:

- For `r2=0`:
  - `colSum` after first row: `[0, 1, 0]`
  - Find subarrays summing to 0:
    - Using prefix sums: count subarrays with sum = 0.
    - Subarray `[0]` (index 0), `[0, 1, 0]`, etc.
- For `r2=1`:
  - `colSum` becomes `[1, 2, 1]` (adding second row)
  - Count subarrays summing to 0.
- For `r2=2`:
  - `colSum` becomes `[1, 3, 1]` (adding third row)
  - Count subarrays summing to 0.

### Step 2: Sum counts for all pairs.

This method efficiently counts all possible submatrices summing to `k`.

---

## Summary Table

| Approach | Core Idea | Algorithm Highlights | Code Snippet | Time Complexity | Space Complexity |
|------------|------------|------------------------|--------------|-----------------|------------------|
| Brute Force | Check all submatrices | Nested loops, sum calculation | Provided above | `O(R^2 * C^2 * R * C)` | `O(1)` |
| Prefix Sum Optimization | Use prefix sums to compute submatrix sums quickly | Precompute prefix sums for rows, then nested loops | Provided above | `O(R^2 * C^2)` | `O(R*C)` |
| 2D Prefix Sum + Hash Map | Fix row pairs, reduce to 1D subarray sum problem | Fix top and bottom rows, use hash map for subarray sums | Provided above | `O(R^2 * C)` | `O(C)` |

---

## Final Tips for Interview Preparation
- Understand how to convert a 2D problem into multiple 1D subproblems.
- Master prefix sums for quick submatrix sum calculation.
- Practice hash map usage for subarray sum counting.
- Be mindful of edge cases: all zeros, negative numbers, small matrices.
- Recognize the importance of fixing rows or columns to optimize counting.

---

This guide provides a comprehensive overview of **Submatrix Sum Equals K** and prepares you for efficient problem-solving and interview success.
