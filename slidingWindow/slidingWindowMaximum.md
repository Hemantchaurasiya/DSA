# Sliding Window Maximum

---

## Problem Overview
Given an array `nums` and an integer `k`, find the maximum element in each sliding window of size `k` as it moves from the start to the end of the array.

**Example:**
```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
```

---

## Approach 1: Brute Force

### **Core Idea**
- For each window of size `k`, scan all `k` elements to find the maximum.
- Repeat for all possible windows.

### **Algorithm**
1. Loop from `i = 0` to `i = n - k`:
   - For each window starting at `i`, scan elements from `i` to `i + k - 1`.
   - Find the maximum element in this window.
2. Store the maximum for each window in the result array.

### **Java Code**
```java
public int[] maxSlidingWindow(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];
    for (int i = 0; i <= n - k; i++) {
        int max = nums[i];
        for (int j = i + 1; j < i + k; j++) {
            max = Math.max(max, nums[j]);
        }
        result[i] = max;
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n * k)**, as for each of the `(n - k + 1)` windows, we scan `k` elements.
- **Space Complexity:** **O(n - k + 1)** for the output array.

### **Dry Run**
- Input: `nums = [1,3,-1,-3,5,3,6,7], k=3`
- Windows and max:
  - `[1,3,-1]` → 3
  - `[3,-1,-3]` → 3
  - `[-1,-3,5]` → 5
  - `[-3,5,3]` → 5
  - `[5,3,6]` → 6
  - `[3,6,7]` → 7

---

## Approach 2: Better Solution using Deque (Most Efficient)

### **Core Idea**
- Use a **double-ended queue (deque)** to keep track of elements' indices in decreasing order.
- The deque ensures the maximum element's index is always at the front.
- When a new element arrives:
  - Remove all elements smaller than it from the back of the deque (since they can't be maximum if a larger element exists after them).
  - Add the new element's index.
- Remove elements from the front if they are out of the current window.
- The front of the deque always contains the index of the maximum element for the current window.

### **Algorithm**
1. Initialize a deque.
2. Iterate through each element `i` in `nums`:
   - While deque is not empty and `nums[i]` >= `nums[deque's last element]`, remove the last element.
   - Add `i` at the back of the deque.
   - Remove the front if it is out of the current window (`i - k + 1 > deque's front index`).
   - Starting from `i >= k - 1`, record `nums[deque's front]` as the maximum for the current window.
3. Continue until all windows are processed.

### **Java Code**
```java
import java.util.*;

public int[] maxSlidingWindow(int[] nums, int k) {
    int n = nums.length;
    int[] result = new int[n - k + 1];
    Deque<Integer> deque = new LinkedList<>();
    for (int i = 0; i < n; i++) {
        // Remove smaller elements from the back
        while (!deque.isEmpty() && nums[i] >= nums[deque.peekLast()]) {
            deque.pollLast();
        }
        deque.offerLast(i);
        // Remove elements out of current window
        if (i - k >= deque.peekFirst()) {
            deque.pollFirst();
        }
        // Record max when first window is complete
        if (i >= k - 1) {
            result[i - k + 1] = nums[deque.peekFirst()];
        }
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n)**, each element is added and removed at most once.
- **Space Complexity:** **O(k)**, for the deque.

### **Dry Run**
- Input: `nums = [1,3,-1,-3,5,3,6,7], k=3`

| i | nums[i] | Deque contents (indices) | Max for window | Explanation                                           |
|---|---------|--------------------------|----------------|-------------------------------------------------------|
| 0 | 1       | [0]                      | -              | Add index 0                                              |
| 1 | 3       | [1] (removes 0 since 3 >= 1) | -            | Remove index 0, add index 1                            |
| 2 | -1      | [1, 2]                   | 3             | Add index 2 (no removal)                                |
| 3 | -3      | [1, 2, 3]                | 3             | Add index 3 (no removal, index 1 still max)           |
| 4 | 5       | [4] (removes 3, 2, 1)    | 5             | Remove indices 3,2,1 because 5 >= their values        |
| 5 | 3       | [4, 5]                   | 5             | Add index 5 (no removal, 5 still max)                 |
| 6 | 6       | [6] (removes 5,4)        | 6             | Remove index 5 and 4, add index 6                     |
| 7 | 7       | [7] (removes 6)            | 7             | Remove index 6, add index 7                           |

Result array: `[3,3,5,5,6,7]`

---

## Summary

| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Brute Force | **O(n*k)** | **O(1)** (excluding result) | Simple but inefficient for large `n`. |
| 2 | Deque (Optimal) | **O(n)** | **O(k)** | Uses a deque to maintain max efficiently, optimal for large inputs. |

---

## Final Tips for Interviews
- Always consider the sliding window approach for maximum efficiency.
- Master the deque-based method, as it's a common pattern for sliding window problems.
- Practice variations: min sliding window, sum sliding window, etc.
- Be prepared to explain why the deque maintains the maximum efficiently.

---

**Happy Coding!**
