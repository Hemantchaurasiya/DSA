# Minimum Number of Operations to Make Array Continuous

---

## Problem Overview
Given an array `nums`, you can perform operations to make the array "continuous," meaning the array contains a sequence of consecutive integers with no duplicates. An operation involves removing an element from the array.

**Goal:**  
Find the **minimum number of operations** required to make `nums` a continuous sequence.

---

## Approach 1: Brute Force (Check All Possible Intervals)

### **Core Idea**
- The problem reduces to finding the longest subarray that can be rearranged into a sequence of consecutive integers.
- Once the longest such subarray is found, the minimum operations are `n - length_of_that_subarray`.

### **Algorithm**
1. For each starting index `i`, consider the subarray `nums[i..j]`.
2. Track the minimum and maximum elements in this subarray.
3. Check if this subarray can be rearranged into a sequence of consecutive integers:
   - Valid if `max - min + 1 == length of subarray`.
4. Keep track of the maximum length of such subarrays.
5. The answer is `n - max_length`.

### **Java Code**
```java
public int minOperations(int[] nums) {
    int n = nums.length;
    int maxLen = 0;
    for (int i = 0; i < n; i++) {
        int minVal = nums[i], maxVal = nums[i];
        for (int j = i; j < n; j++) {
            minVal = Math.min(minVal, nums[j]);
            maxVal = Math.max(maxVal, nums[j]);
            int length = j - i + 1;
            if (maxVal - minVal + 1 == length) {
                maxLen = Math.max(maxLen, length);
            }
        }
    }
    return n - maxLen;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n^3)** (due to nested loops and min/max tracking for each subarray).
- **Space Complexity:** **O(1)**.

### **Dry Run**
- Input: `nums = [1, 2, 3, 5, 6]`
- Longest subarray that can be rearranged into consecutive numbers: `[1, 2, 3]` (length 3).
- Minimum operations: `5 - 3 = 2`.

---

## Approach 2: Optimized Using Hashing and Sliding Window (Most Efficient)

### **Core Idea**
- The problem simplifies to finding the length of the **longest subarray** that can be rearranged into a sequence of consecutive integers.
- To do this efficiently:
  - Use a **hash map** to track the earliest occurrence of each element.
  - Use a **sliding window** to expand the subarray.
  - Keep track of the minimum and maximum values in the current window.
- The **answer** is `n - length_of_longest_valid_subarray`.

### **Algorithm**
1. Create a hash map `posMap` to track the last occurrence of each element.
2. Initialize variables:
   - `left = 0`
   - `maxLen = 0`
3. Iterate `right` over the array:
   - Update the last occurrence position of `nums[right]`.
   - If `nums[right]` repeats within the current window, move `left` to the position after its last occurrence.
   - Track the minimum and maximum in the current window.
   - If the window is valid (max - min + 1 == window size), update `maxLen`.
4. Return `n - maxLen`.

### **Java Code**
```java
import java.util.*;

public int minOperations(int[] nums) {
    int n = nums.length;
    int maxLen = 0;
    Map<Integer, Integer> lastPos = new HashMap<>();
    int left = 0;
    int minVal = Integer.MAX_VALUE, maxVal = Integer.MIN_VALUE;

    for (int right = 0; right < n; right++) {
        if (lastPos.containsKey(nums[right])) {
            // Move left if this element was seen before inside current window
            left = Math.max(left, lastPos.get(nums[right]) + 1);
        }
        lastPos.put(nums[right], right);
        // Track min and max in the current window
        minVal = Math.min(minVal, nums[right]);
        maxVal = Math.max(maxVal, nums[right]);
        int windowSize = right - left + 1;
        // Check if current window can form consecutive sequence
        if (maxVal - minVal + 1 == windowSize) {
            maxLen = Math.max(maxLen, windowSize);
        }
        // Reset minVal and maxVal if window changes
        if (right - left + 1 > maxVal - minVal + 1) {
            minVal = Arrays.stream(Arrays.copyOfRange(nums, left, right + 1)).min().getAsInt();
            maxVal = Arrays.stream(Arrays.copyOfRange(nums, left, right + 1)).max().getAsInt();
        }
    }
    return n - maxLen;
}
```

**Note:** In practice, updating min and max efficiently is complex; a balanced data structure or monotonic queues are often used for optimal performance. Here, the core idea is to keep track of the longest valid subarray.

### **Complexity Analysis**
- **Time Complexity:** **O(n)**, as each element is visited once; updating min/max can be optimized with data structures.
- **Space Complexity:** **O(n)** for hash map.

---

## Summary

| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Brute Force | **O(n^3)** | **O(1)** | Checks all subarrays, slow for large inputs. |
| 2 | Sliding Window with Hashing | **O(n)** | **O(n)** | Efficient, tracks last occurrences and window validity.|

---

## Final Tips for Interviews
- Recognize that the problem revolves around finding the longest subarray that can be rearranged into a sequence of consecutive integers.
- Use the sliding window approach to track valid subarrays efficiently.
- Pay attention to duplicate elements and how they affect the window boundaries.
- Optimize min/max tracking with appropriate data structures if needed for large inputs.

---

**Happy Coding!**
