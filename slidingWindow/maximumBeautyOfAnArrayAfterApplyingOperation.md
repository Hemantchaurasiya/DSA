# Maximum Beauty of an Array After Applying Operation

---

## Problem Overview
Given an array `nums`, you can perform operations to increase or decrease elements by a fixed value `k`. Your goal is to maximize the **difference** between the maximum and minimum value in the array after any number of such operations.

**Key insight:**  
You can adjust elements by `+k` or `-k` to bring them closer or farther apart, but the goal is to maximize the difference.

---

## Approach 1: Brute Force (Check All Possibilities)

### **Core Idea**
- Try all possible ways of adjusting each element by `+k` or `-k`.
- For each configuration, find the max and min.
- The maximum difference across all configurations is the answer.

### **Algorithm**
1. Generate all configurations where each element is either increased or decreased by `k`.
2. For each configuration:
   - Calculate the max and min.
   - Update the maximum difference.
3. Return the maximum difference found.

### **Java Code**
```java
public int largestDifference(int[] nums, int k) {
    int n = nums.length;
    int maxDiff = 0;

    // Generate all combinations with +k or -k
    for (int mask = 0; mask < (1 << n); mask++) {
        int maxVal = Integer.MIN_VALUE;
        int minVal = Integer.MAX_VALUE;
        for (int i = 0; i < n; i++) {
            int val = nums[i] + ((mask & (1 << i)) != 0 ? k : -k);
            maxVal = Math.max(maxVal, val);
            minVal = Math.min(minVal, val);
        }
        maxDiff = Math.max(maxDiff, maxVal - minVal);
    }
    return maxDiff;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(2^n * n)** — exponential in `n` due to all configurations.
- **Space Complexity:** **O(1)**.

---

## Approach 2: Greedy Strategy Based on Sorting

### **Core Idea**
- After sorting, the array's elements are in order.
- The elements that need to be adjusted to maximize difference are likely at the ends.
- Adjust the minimum possible elements upward and maximum possible elements downward, within the `k` constraints.

### **Algorithm**
1. Sort the array.
2. Initialize the answer as `max(nums) - min(nums)`.
3. For each position `i` (from `0` to `n-2`):
   - Consider `nums[i]` as the lower boundary (possibly increased by `k`).
   - Consider `nums[i+1]` as the upper boundary (possibly decreased by `k`).
   - The potential max difference:
     - `max(nums[n-1] - k, nums[i] + k) - min(nums[0] + k, nums[i+1] - k)`
4. Take the maximum over all these potential differences.

### **Java Code**
```java
import java.util.Arrays;

public int maxDifferenceAfterOperations(int[] nums, int k) {
    Arrays.sort(nums);
    int n = nums.length;
    int maxDiff = nums[n - 1] - nums[0]; // initial difference

    for (int i = 0; i < n - 1; i++) {
        int high = Math.max(nums[n - 1] - k, nums[i] + k);
        int low = Math.min(nums[0] + k, nums[i + 1] - k);
        maxDiff = Math.max(maxDiff, high - low);
    }
    return maxDiff;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n log n)** for sorting.
- **Space Complexity:** **O(1)** (ignoring the sort space).

---

## Approach 3: Most Optimal Solution (Sorting + Greedy)

### **Core Idea**
- Sorting simplifies the problem.
- The maximum difference achievable after operations depends on the smallest and largest elements, but also on how elements are adjusted near the boundaries.
- The key is to consider the minimal and maximal elements after adjustment and the boundary points where adjustments cross over.

### **Algorithm**
1. Sort the array.
2. Initialize `diff = nums[n-1] - nums[0]`.
3. For each `i` from `0` to `n-2`:
   - Compute potential maximum difference:
     - `max(nums[n-1] - k, nums[i] + k) - min(nums[0] + k, nums[i+1] - k)`
   - Update the result with the maximum of current `diff` and this value.
4. Return the maximum difference.

### **Java Code**
```java
import java.util.Arrays;

public int maximumDifference(int[] nums, int k) {
    Arrays.sort(nums);
    int result = nums[nums.length - 1] - nums[0]; // initial max difference
    for (int i = 0; i < nums.length - 1; i++) {
        int high = Math.max(nums[nums.length - 1] - k, nums[i] + k);
        int low = Math.min(nums[0] + k, nums[i + 1] - k);
        result = Math.max(result, high - low);
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n log n)** for sorting.
- **Space Complexity:** **O(1)**.

---

## Summary

| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Brute Force (All configurations) | **O(2^n * n)** | **O(1)** | Exhaustive, not feasible for large `n`. |
| 2 | Greedy based on sorting and boundary adjustments | **O(n log n)** | **O(1)** | Efficient, leverages sorting and boundary analysis. |
| 3 | Optimized Sorting + Boundary Calculation | **O(n log n)** | **O(1)** | Simplest and most efficient approach for large inputs. |

---

## Final Tips for Interviews
- Recognize that the problem revolves around boundary adjustments and sorting.
- The greedy approach is optimal and straightforward once sorted.
- Think about how the minimal and maximal elements can be adjusted within the constraints to maximize the difference.
- Test edge cases with small arrays and arrays where all elements are equal.

---

**Happy Coding!**
