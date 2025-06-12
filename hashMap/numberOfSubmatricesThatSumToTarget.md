# Number of Submatrices That Sum to Target

---

## Problem Overview

Given a 2D matrix of integers and an integer `target`, find the **number of submatrices** whose elements sum up to `target`.

A submatrix is defined by its top-left corner `(r1, c1)` and bottom-right corner `(r2, c2)`.

---

## Approach 1: Brute Force (Enumerate All Submatrices)

### **Core Idea**
Enumerate all possible submatrices and calculate their sums to check if they equal `target`.

### **Algorithm**
1. Loop over all pairs `(r1, r2)` to define the row boundaries.
2. Loop over all pairs `(c1, c2)` to define the column boundaries.
3. For each submatrix `(r1, c1)` to `(r2, c2)`:
   - Calculate the sum of elements within it.
   - If sum == `target`, increment the count.
4. Return the total count.

### **Java Code**
```java
public class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        int n = matrix.length;
        int m = matrix[0].length;
        int count = 0;

        for (int r1 = 0; r1 < n; r1++) {
            for (int r2 = r1; r2 < n; r2++) {
                for (int c1 = 0; c1 < m; c1++) {
                    for (int c2 = c1; c2 < m; c2++) {
                        int sum = 0;
                        for (int r = r1; r <= r2; r++) {
                            for (int c = c1; c <= c2; c++) {
                                sum += matrix[r][c];
                            }
                        }
                        if (sum == target) {
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
  `O(N^2 * M^2 * (N * M))` in worst case, as nested loops for all submatrices and summation.
  Simplifies approximately to `O(N^3 * M^3)` in worst case.
- **Space Complexity:**  
  `O(1)` (ignoring input storage).

---

## Approach 2: Prefix Sum Optimization

### **Core Idea**
Use prefix sums to compute submatrix sums efficiently, reducing summation time.

### **Algorithm**
1. Precompute a prefix sum matrix `prefixSum`, where `prefixSum[r][c]` is sum of elements from `(0,0)` to `(r,c)`.
2. Enumerate all submatrices `(r1, c1)` to `(r2, c2)`.
3. Calculate the sum using prefix sums:
   ```
   sum = prefixSum[r2][c2] 
       - prefixSum[r1-1][c2] (if r1 > 0)
       - prefixSum[r2][c1-1] (if c1 > 0)
       + prefixSum[r1-1][c1-1] (if r1 > 0 and c1 > 0)
   ```
4. Check if sum == target, increment count.

### **Java Code**
```java
public class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        int n = matrix.length;
        int m = matrix[0].length;
        int count = 0;

        // Compute prefix sum matrix
        int[][] prefixSum = new int[n + 1][m + 1];
        for (int r = 1; r <= n; r++) {
            for (int c = 1; c <= m; c++) {
                prefixSum[r][c] = matrix[r - 1][c - 1]
                                + prefixSum[r - 1][c]
                                + prefixSum[r][c - 1]
                                - prefixSum[r - 1][c - 1];
            }
        }

        for (int r1 = 1; r1 <= n; r1++) {
            for (int r2 = r1; r2 <= n; r2++) {
                for (int c1 = 1; c1 <= m; c1++) {
                    for (int c2 = c1; c2 <= m; c2++) {
                        int sum = prefixSum[r2][c2]
                                - prefixSum[r1 - 1][c2]
                                - prefixSum[r2][c1 - 1]
                                + prefixSum[r1 - 1][c1 - 1];
                        if (sum == target) {
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
  `O(N^2 * M^2)` due to all submatrix enumerations, with constant time for sum calculation using prefix sums.
- **Space Complexity:**  
  `O(N * M)` for storing the prefix sum matrix.

---

## Approach 3: Reduce to 1D Subarray Sum Problem (HashMap + Prefix Sum for 1D)

### **Core Idea**
Fix two rows `(r1, r2)` and reduce the problem to 1D: find subarrays in the compressed array (column sums between `r1` and `r2`) that sum to `target`.

### **Algorithm**
1. Iterate over all pairs of rows `(r1, r2)`:
   - Create a compressed 1D array `colSum` where `colSum[c]` is sum of elements between rows `r1` and `r2` in column `c`.
2. Use a hashmap to count the number of subarrays in `colSum` with sum equal to `target`:
   - Maintain a running prefix sum.
   - For each prefix sum, check if `(prefixSum - target)` exists in the map.
   - Increment count accordingly.
3. Sum counts for all row pairs.

### **Java Code**
```java
import java.util.*;

public class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        int n = matrix.length;
        int m = matrix[0].length;
        int count = 0;

        for (int r1 = 0; r1 < n; r1++) {
            int[] colSum = new int[m];
            for (int r2 = r1; r2 < n; r2++) {
                // Update colSum for current row pair
                for (int c = 0; c < m; c++) {
                    colSum[c] += matrix[r2][c];
                }
                // Now find subarrays in colSum that sum to target
                count += countSubarraysWithSum(colSum, target);
            }
        }
        return count;
    }

    private int countSubarraysWithSum(int[] arr, int target) {
        Map<Integer, Integer> prefixMap = new HashMap<>();
        prefixMap.put(0, 1);
        int count = 0, prefixSum = 0;

        for (int num : arr) {
            prefixSum += num;
            count += prefixMap.getOrDefault(prefixSum - target, 0);
            prefixMap.put(prefixSum, prefixMap.getOrDefault(prefixSum, 0) + 1);
        }
        return count;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(N^2 * M)` for row pairs, and `O(M)` for each subarray counting, total `O(N^2 * M)`.
- **Space Complexity:**  
  `O(M)` for the hashmap in subarray sum counting.

---

## **Dry Run: Step-by-step Example**

**Input:**
```plaintext
matrix = [[0,1,0],
          [1,1,1],
          [0,1,0]]
target = 4
```

### For row pair `(r1=0, r2=1)`:
- `colSum` after summing rows 0 and 1:
  - c=0: 0+1=1
  - c=1: 1+1=2
  - c=2: 0+1=1

- Find subarrays with sum = 4 in `[1, 2, 1]`:
  - No subarray sums to 4, so count remains 0.

Repeat for other row pairs, summing over all.

---

## Summary Table

| Approach | Core Idea | Algorithm Highlights | Code Snippet | Time Complexity | Space Complexity |
|------------|------------|------------------------|--------------|-----------------|------------------|
| Brute Force | Enumerate all submatrices | Nested loops, sum calculation | Provided above | `O(N^2 * M^2 * N*M)` | `O(1)` |
| Prefix Sum Optimization | Use prefix sums for quick sum calculation | 4 nested loops, prefix sum array | Provided above | `O(N^2 * M^2)` | `O(N*M)` |
| Reduce to 1D Subarray Sum | Fix row pairs, solve 1D problem | HashMap + prefix sums for subarrays | Provided above | `O(N^2 * M)` | `O(M)` |

---

## Final Tips
- Use prefix sums to optimize sum retrieval.
- Reduce 2D to 1D sub-problems when possible.
- Efficiently count subarrays with target sum using HashMaps.
- Practice handling large inputs and edge cases.

---

This guide provides a comprehensive understanding of solving **Number of Submatrices That Sum to Target** efficiently, suitable for interview prep!
