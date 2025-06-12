# Minimum Operations to Reduce X to Zero

This problem involves finding the minimum number of operations required to reduce a given integer `X` to zero by removing elements from either end of an array, where each removal subtracts the value of the removed element from `X`.

---

## 1. Problem Restatement
Given an array `nums` of positive integers and an integer `X`, you can remove elements either from the start or the end of the array. Each removal reduces `X` by the value of the removed element. The goal is to find the **minimum number of removals** to reduce `X` to zero, or determine that it's impossible.

---

## 2. Approach 1: Brute Force (Recursive / Backtracking)

### **Approach Name:**  
Naive Recursive / Backtracking

### **Core Idea:**  
Try all possible combinations of removing elements from either end until `X` becomes zero, tracking the minimum number of operations.

### **Algorithm:**  
1. Define a recursive function that takes the current `X` and the current subarray boundaries (`left`, `right`).
2. If `X` == 0, return the total number of operations performed.
3. If `X` < 0 or no elements left, return infinity (impossible).
4. Recurse by removing from the left (`left + 1`) or from the right (`right - 1`), subtracting the respective element's value from `X`.
5. Take the minimum of both options.
6. Return the minimum number of steps found.

**Note:** This approach has exponential time complexity and is impractical for large inputs.

### **Java Code:**
```java
public int minOperations(int[] nums, int X) {
    return dfs(nums, 0, nums.length - 1, X);
}

private int dfs(int[] nums, int left, int right, int X) {
    if (X == 0) return 0;
    if (X < 0 || left > right) return Integer.MAX_VALUE;

    int removeLeft = dfs(nums, left + 1, right, X - nums[left]);
    int removeRight = dfs(nums, left, right - 1, X - nums[right]);
    int res = Math.min(removeLeft, removeRight);
    return res == Integer.MAX_VALUE ? res : res + 1;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(2^n)** (exponential due to all combinations)  
- **Space Complexity:** **O(n)** (recursion stack)

---

## 3. Approach 2: Prefix and Suffix Sum with Hash Map (Optimized)

### **Approach Name:**  
Transform the problem into a subarray sum problem

### **Core Idea:**  
Removing elements from either end is equivalent to keeping a middle subarray. If we find the longest subarray in `nums` whose sum equals `sum(nums) - X`, then the remaining elements (from ends) sum to `X`. The minimal operations needed are the total length minus the length of this subarray.

### **Algorithm:**  
1. Calculate `target = sum(nums) - X`.
2. If `target` < 0, it's impossible; return -1.
3. Use a sliding window to find the longest subarray with sum = `target`.
4. The answer is `n - length of this subarray`.
5. If no such subarray exists, return -1.

### **Step-by-step:**
- Compute total sum of array.
- Find the longest subarray with sum = `total sum - X`.
- Use two pointers (`left`, `right`) to expand and shrink the window to find maximum length subarray with desired sum.
- The minimal operations = total length - maximum subarray length.

### **Java Code:**
```java
public int minOperations(int[] nums, int X) {
    int totalSum = 0;
    for (int num : nums) totalSum += num;

    int target = totalSum - X;
    if (target < 0) return -1; // impossible to reduce X to zero
    if (target == 0) return nums.length; // need to remove all elements

    int maxLen = -1;
    int currentSum = 0;
    int left = 0;

    for (int right = 0; right < nums.length; right++) {
        currentSum += nums[right];

        while (currentSum > target && left <= right) {
            currentSum -= nums[left];
            left++;
        }

        if (currentSum == target) {
            maxLen = Math.max(maxLen, right - left + 1);
        }
    }

    return maxLen == -1 ? -1 : nums.length - maxLen;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**, as each element is visited at most twice.  
- **Space Complexity:** **O(1)** (additional variables used).

---

## 4. Dry Run Example

**Input:**

```plaintext
nums = [1, 1, 4, 2, 3]
X = 5
```

### Step-by-step:

- Calculate total sum: `1 + 1 + 4 + 2 + 3 = 11`
- Compute target: `11 - 5 = 6`
- Find longest subarray with sum = 6:

| `right` | Current Sum | Window `[left..right]` | Conditions | Max Length | Operations Needed |
|-----------|--------------|------------------------|------------|--------------|-------------------|
| 0         | 1            | [1]                    | sum=1 < 6 | -            | -                 |
| 1         | 2            | [1, 1]                 | sum=2 < 6 | -            | -                 |
| 2         | 6            | [1, 1, 4]              | sum=6 == 6| maxLen=3     | -                 |
| 3         | 8            | [1, 1, 4, 2]           | sum>6     | shift left  | sum=7, left=1  |
|           |              |                        |            |             | shift left  | sum=6, left=2  |
| 4         | 9            | [4, 2, 3]              | sum=9>6  | shift left  | sum=8, left=3  |
|           |              |                        |            |             | shift left  | sum=6, left=4  |

- Max subarray length with sum=6 is 3 (from `[1,1,4]`).
- Minimum operations = total length - maxLen = 5 - 3 = 2.

**Answer:** 2 operations (remove `[1, 1]` from start and `[3]` from end).

---

## 5. Summary

| Approach | Time Complexity | Space Complexity | Key Idea |
|--------------|------------------|------------------|------------|
| Brute Force | O(2^n) | O(n) | Explore all removal combinations recursively |
| Prefix Sum + Sliding Window | O(n) | O(1) | Reformulate as longest subarray with sum = total - X |
| Efficient Sliding Window | O(n) | O(1) | Find maximum length subarray with sum = total - X |

---

## **Final Tips for Interviews:**
- Recognize the problem as a variation of the subarray sum problem.
- Use prefix sums and sliding window to optimize.
- For large inputs, avoid brute-force recursion.
- Always think about transforming the problem into a subarray sum problem for efficiency.

---

**Happy Revising!**
