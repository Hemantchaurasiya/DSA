# Frequency of the Most Frequent Element

This guide provides a detailed overview of various approaches to solve the "Frequency of the Most Frequent Element" problem. It progresses from brute-force to the most efficient solution, structured for quick revision and interview prep.

---

## Problem Summary
Given an array `nums` and an integer `k`, you can perform at most `k` operations, where in each operation you can increment any element by 1. The goal is to find the maximum frequency of the most frequent element after performing at most `k` increments.

---

## Approach 1: Brute Force (Sorting + Counting)

### **Core Idea:**
Try every element as the target to make all elements in a window equal to, using increments, and count the maximum achievable frequency.

---

### **Algorithm:**

1. Sort the array to group similar elements together.
2. Use a sliding window to check subarrays:
   - For each window, calculate the total increments needed to make all elements equal to the rightmost element (which is the largest in the window).
   - If the total increments needed are within `k`, update the maximum frequency.
3. Continue sliding through the array to find the maximum window size satisfying the condition.

---

### **Java Code:**
```java
import java.util.Arrays;

public int maxFrequency(int[] nums, int k) {
    Arrays.sort(nums);
    int maxFreq = 1;
    long total = 0;
    int left = 0;

    for (int right = 0; right < nums.length; right++) {
        total += nums[right];

        // Shrink window if total increments needed exceed k
        while ((long)nums[right] * (right - left + 1) - total > k) {
            total -= nums[left];
            left++;
        }

        maxFreq = Math.max(maxFreq, right - left + 1);
    }

    return maxFreq;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n log n)** due to sorting, plus **O(n)** for the sliding window traversal.

- **Space Complexity:**  
  **O(1)** — Sorting is in-place; only variables used are constant.

---

### **Dry Run:**

**Input:**
```plaintext
nums = [1, 2, 4], k = 5
```

| Step | `right` | Current window (`left` to `right`) | Sorted array | Sum `total` | Max frequency update | Explanation |
|-------|-----------|---------------------------------------|--------------|--------------|----------------------|--------------|
| 1     | 0         | [1]                                   | [1, 2, 4]    | 1            | 1                    | Single element, max freq = 1 |
| 2     | 1         | [1, 2]                               | [1, 2, 4]    | 3            | 2                    | Need to make both 2s, total increments = 2 - 1*2=1 ≤ k |
| 3     | 2         | [1, 2, 4]                            | [1, 2, 4]    | 7            | 2                    | Increments needed to make all 4s: (4*3 - sum=7)=5 ≤ k, max frequency = 3 |

---

## Approach 2: Sliding Window with Sorting (Most Efficient)

### **Core Idea:**
Sort the array and use a sliding window to find the largest window where the total increments needed to make all elements equal to the largest element in the window are within `k`.

### **Algorithm:**

1. Sort the array.
2. Initialize two pointers (`left` and `right`) and a variable `total` to keep the sum of elements in the window.
3. Expand `right`:
   - Add `nums[right]` to `total`.
   - Calculate the increments needed: `nums[right] * (window size) - total`.
   - If increments > `k`, shrink the window from the left:
     - Subtract `nums[left]` from `total`.
     - Increment `left`.
4. Update maximum window size during each iteration.

---

### **Java Code:**
```java
import java.util.Arrays;

public int maxFrequency(int[] nums, int k) {
    Arrays.sort(nums);
    int maxFreq = 1;
    long total = 0;
    int left = 0;

    for (int right = 0; right < nums.length; right++) {
        total += nums[right];

        while ((long)nums[right] * (right - left + 1) - total > k) {
            total -= nums[left];
            left++;
        }

        maxFreq = Math.max(maxFreq, right - left + 1);
    }

    return maxFreq;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n log n)** due to sorting, and **O(n)** for sliding window traversal.

- **Space Complexity:**  
  **O(1)** — Sorting in-place; only variables used.

---

### **Dry Run (Same example):**

```plaintext
nums = [1, 2, 4], k = 5
Sorted: [1, 2, 4]

- Initialize: left=0, total=0, maxFreq=1
- right=0: total=1; window=[1]; increment needed=1*1 - 1=0 ≤ k; maxFreq=1
- right=1: total=3; window=[1,2]; increments=2*2 - 3=1 ≤ k; maxFreq=2
- right=2: total=7; window=[1,2,4]; increments=4*3 - 7=12 - 7=5 ≤ k; maxFreq=3

Result: 3
```

---

## Summary of Approaches:

| Approach | Time Complexity | Space Complexity | Notes |
|------------|-------------------|--------------------|--------|
| Sorting + Counting | **O(n log n)** | **O(1)** | Basic approach, efficient for small to moderate inputs. |
| Sliding Window + Sorting | **O(n log n)** | **O(1)** | Most optimal, handles large inputs efficiently. |

---

## Final Tips:
- Sorting simplifies the problem by grouping similar elements.
- Use sliding window to efficiently find maximum range satisfying the conditions.
- Be cautious with large numbers; use `long` for sum calculations.
- Always consider in-place algorithms to optimize space.

---

This guide provides a clear understanding of the problem and the most efficient solution, ready for quick revision or interview use.
