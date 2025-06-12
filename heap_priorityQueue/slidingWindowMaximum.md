# Sliding Window Maximum

This document provides a comprehensive guide to solving the **Sliding Window Maximum** problem, covering multiple approaches from brute-force to the most optimal solution, along with explanations, code, and dry runs for effective understanding.

---

## Problem Statement
Given an array `nums` and an integer `k`, find the maximum value in every sliding window of size `k` moving from left to right.

**Example:**
```plaintext
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
```

---

## 1. Brute Force Approach

### Core Idea:
Check every window of size `k` and find the maximum element within it. 

### Algorithm:
1. Loop through the array from index `0` to `n - k`.
2. For each index, examine the subarray `nums[i..i + k - 1]`.
3. Find the maximum element in this subarray.
4. Append the maximum to the result list.

### Java Code:
```java
public int[] maxSlidingWindowBruteForce(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];

    for (int i = 0; i <= n - k; i++) {
        int max = Integer.MIN_VALUE;
        for (int j = i; j < i + k; j++) {
            max = Math.max(max, nums[j]);
        }
        result[i] = max;
    }
    return result;
}
```

### Complexity Analysis:
- **Time Complexity:** `O(n * k)`  
  For each of the `n - k + 1` windows, we scan `k` elements.
- **Space Complexity:** `O(n - k + 1)` for the output array.

### Dry Run:
Input: `nums = [1,3,-1,-3,5,3,6,7]`, `k=3`

| Window Index | Window Elements | Max | Result Array |
|----------------|-------------------|-----|--------------|
| 0              | [1, 3, -1]       | 3   | [3]          |
| 1              | [3, -1, -3]      | 3   | [3, 3]      |
| 2              | [-1, -3, 5]       | 5   | [3, 3, 5]   |
| 3              | [-3, 5, 3]        | 5   | [3, 3, 5, 5]|
| 4              | [5, 3, 6]         | 6   | [3, 3, 5, 5, 6]|
| 5              | [3, 6, 7]         | 7   | [3, 3, 5, 5, 6, 7]|

---

## 2. Better Solution: Using Deque (Double-ended Queue)

### Core Idea:
Utilize a **deque** to keep track of indices of elements in decreasing order. For each new element:
- Remove elements from the back if they are less than the current element.
- Remove elements from the front if they are out of the current window.
- The element at the front of the deque is always the maximum for the current window.

### Algorithm:
1. Initialize an empty deque.
2. Iterate through the array:
   - While the deque is not empty and `nums[current]` is greater than or equal to `nums[deque.back()]`, remove `deque.back()` (pop from back).
   - Append the current index.
   - Remove the front of the deque if it's out of the current window (`index <= i - k`).
   - When `i >= k - 1`, append `nums[deque.front()]` to results as the maximum of the current window.

### Java Code:
```java
import java.util.Deque;
import java.util.LinkedList;

public int[] maxSlidingWindowDeque(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];
    Deque<Integer> deque = new LinkedList<>();
    int ri = 0;

    for (int i = 0; i < n; i++) {
        // Remove smaller elements from the back
        while (!deque.isEmpty() && nums[deque.peekLast()] <= nums[i]) {
            deque.pollLast();
        }
        // Add current index
        deque.offerLast(i);

        // Remove out-of-window elements from the front
        if (deque.peekFirst() <= i - k) {
            deque.pollFirst();
        }

        // Record max for the window
        if (i >= k - 1) {
            result[ri++] = nums[deque.peekFirst()];
        }
    }
    return result;
}
```

### Complexity Analysis:
- **Time Complexity:** `O(n)`  
  Each element is added and removed from the deque at most once.
- **Space Complexity:** `O(k)` for the deque and `O(n - k + 1)` for output.

### Dry Run:
Input: `nums = [1,3,-1,-3,5,3,6,7]`, `k=3`

| i | nums[i] | Deque (indices) | Max for window | Result          |
|---|---------|-----------------|------------------|-----------------|
| 0 | 1       | [0]             |                  |                 |
| 1 | 3       | [1]             |                  |                 |
| 2 | -1      | [1, 2]          | 3                | [3]             |
| 3 | -3      | [1, 2, 3]       | 3                | [3, 3]         |
| 4 | 5       | [4]             | 5                | [3, 3, 5]      |
| 5 | 3       | [4, 5]          | 5                | [3, 3, 5, 5]   |
| 6 | 6       | [6]             | 6                | [3, 3, 5, 5, 6]|
| 7 | 7       | [7]             | 7                | [3, 3, 5, 5, 6, 7]|

---

## 3. Most Optimal Solution: Using Monotonic Queue (Deque)

This approach is essentially the same as the "Better Solution" above, where the deque maintains elements in decreasing order, ensuring that the maximum element in the current window is always accessible at the front.

### Core Idea:
Maintain a **monotonically decreasing deque** of indices:
- Ensures the front always holds the index of the maximum element.
- When a new element arrives, remove all smaller elements from the back.
- Remove elements outside the current window from the front.

### Algorithm:
Same as the deque approach described above, which achieves `O(n)` time complexity.

---

## Summary Table

| Approach                     | Time Complexity | Space Complexity | Key Insight                                              |
|------------------------------|-------------------|---------------------|----------------------------------------------------------|
| Brute Force                  | `O(n * k)`        | `O(n)`              | Check each window independently; naive approach.        |
| Using Deque (Optimized)      | `O(n)`            | `O(k)`              | Maintain a decreasing deque to track maximum efficiently.|

---

## Final Tips for Interviews:
- Always start with the brute-force approach to establish understanding.
- Move to the deque-based solution for an optimal approach.
- Understand how to maintain the deque invariants for maximum retrieval.
- Practice dry runs to build intuition on how the deque updates.

---

Feel free to revisit this guide for quick revision before interviews or coding practice!
