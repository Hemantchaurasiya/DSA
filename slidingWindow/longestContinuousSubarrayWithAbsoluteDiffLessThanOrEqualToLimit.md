# Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit

---

## Problem Overview
Given an array `nums` and an integer `limit`, find the length of the **longest subarray** such that the absolute difference between any two elements in this subarray is less than or equal to `limit`.

**Example:**
```
Input: nums = [8, 2, 4, 7], limit = 4
Output: 2
```
The subarrays `[8, 2]`, `[2, 4]`, `[4, 7]` each satisfy the condition with maximum length 2.

---

## Approach 1: Brute Force (Check All Subarrays)

### **Core Idea**
- Generate all possible subarrays.
- For each subarray, find the maximum and minimum elements.
- Check if `max - min <= limit`.
- Keep track of the maximum length of such subarrays.

### **Algorithm**
1. Iterate over all starting indices `i`.
2. For each `i`, extend the subarray to `j` (`i <= j < n`).
3. Find the max and min in `nums[i..j]`.
4. If `max - min <= limit`, update the maximum length found.
5. Stop expanding when the condition is violated for the current subarray.

### **Java Code**
```java
public int longestSubarray(int[] nums, int limit) {
    int n = nums.length;
    int maxLen = 0;
    for (int i = 0; i < n; i++) {
        int maxVal = nums[i], minVal = nums[i];
        for (int j = i; j < n; j++) {
            maxVal = Math.max(maxVal, nums[j]);
            minVal = Math.min(minVal, nums[j]);
            if (maxVal - minVal <= limit) {
                maxLen = Math.max(maxLen, j - i + 1);
            } else {
                break; // No need to check further for this starting point
            }
        }
    }
    return maxLen;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n^3)** in the worst case (nested loops + min/max calculations).
- **Space Complexity:** **O(1)**.

### **Dry Run**
- Input: `nums = [8, 2, 4, 7]`, `limit=4`
- Checks subarrays:
  - `[8, 2]` → max=8, min=2, diff=6 > 4 → invalid
  - `[2, 4]` → max=4, min=2, diff=2 ≤ 4 → max length=2
  - `[4, 7]` → max=7, min=4, diff=3 ≤ 4 → max length=2
- Result: 2

---

## Approach 2: Sliding Window with Monotonic Queues (Optimized)

### **Core Idea**
- Use **two monotonic queues**:
  - One to track the **maximum** element in the current window.
  - One to track the **minimum** element in the current window.
- Expand the window by moving `right`.
- Shrink the window from the left when the condition `max - min > limit` is violated.
- The size of the window at each step gives the maximum length.

### **Algorithm**
1. Initialize two deques:
   - `maxDeque`: stores elements in decreasing order (front is max).
   - `minDeque`: stores elements in increasing order (front is min).
2. Use two pointers `left` and `right` to represent the window.
3. Iterate `right` over the array:
   - While `maxDeque`'s last element is less than `nums[right]`, pop it.
   - While `minDeque`'s last element is greater than `nums[right]`, pop it.
   - Add `nums[right]` to both deques.
   - Check if `maxDeque.front() - minDeque.front() > limit`:
     - If yes, move `left` forward:
       - Remove `nums[left]` from deques if they are at the front.
   - Update the maximum window size during the process.

### **Java Code**
```java
import java.util.*;

public int longestSubarray(int[] nums, int limit) {
    Deque<Integer> maxDeque = new LinkedList<>();
    Deque<Integer> minDeque = new LinkedList<>();
    int left = 0, maxLen = 0;

    for (int right = 0; right < nums.length; right++) {
        // Maintain decreasing order for maxDeque
        while (!maxDeque.isEmpty() && nums[right] > maxDeque.peekLast()) {
            maxDeque.pollLast();
        }
        maxDeque.offerLast(nums[right]);

        // Maintain increasing order for minDeque
        while (!minDeque.isEmpty() && nums[right] < minDeque.peekLast()) {
            minDeque.pollLast();
        }
        minDeque.offerLast(nums[right]);

        // Shrink window if condition is violated
        while (!maxDeque.isEmpty() && !minDeque.isEmpty() && (maxDeque.peekFirst() - minDeque.peekFirst() > limit)) {
            if (nums[left] == maxDeque.peekFirst()) {
                maxDeque.pollFirst();
            }
            if (nums[left] == minDeque.peekFirst()) {
                minDeque.pollFirst();
            }
            left++;
        }

        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n)**, each element is added and removed at most once from the deques.
- **Space Complexity:** **O(n)** for the deques.

---

## Summary

| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Brute Force | **O(n^3)** | **O(1)** | Checks all subarrays, slow for large inputs. |
| 2 | Sliding Window with Monotonic Queues | **O(n)** | **O(n)** | Efficient, maintains max/min in current window dynamically. |

---

## Final Tips for Interviews
- Recognize that the problem involves maintaining a window where the difference between max and min elements is constrained.
- Use **monotonic queues** for efficient retrieval of current window max and min.
- Always carefully handle window shrinking when the condition is violated.
- Think of sliding window as a dynamic way to process subarrays in linear time.

---

**Happy Coding!**
