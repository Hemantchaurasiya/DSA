# Count the Number of Good Subarrays

This comprehensive guide covers multiple approaches to solve the "Count the Number of Good Subarrays" problem, starting from brute-force and progressing to the most efficient solution. It is structured for quick revision and interview preparation.

---

## Problem Summary
Given an array `nums` of integers and an integer `k`, a subarray is considered **good** if the difference between its maximum and minimum element is less than or equal to `k`. The task is to count the total number of such good subarrays.

---

## Approach 1: Brute Force

### **Core Idea:**
Enumerate all subarrays, compute their maximum and minimum, and check if the difference is ≤ `k`. Count the subarrays satisfying the condition.

---

### **Algorithm:**
1. Iterate over all possible starting indices `i`.
2. For each starting index, iterate over ending indices `j` ≥ `i`.
3. Track the maximum and minimum in the subarray `nums[i..j]`.
4. If `max - min ≤ k`, increment the count.
5. Continue until all subarrays are checked.

---

### **Java Code:**
```java
public int countGoodSubarrays(int[] nums, int k) {
    int count = 0;
    int n = nums.length;

    for (int i = 0; i < n; i++) {
        int maxVal = nums[i];
        int minVal = nums[i];

        for (int j = i; j < n; j++) {
            maxVal = Math.max(maxVal, nums[j]);
            minVal = Math.min(minVal, nums[j]);

            if (maxVal - minVal <= k) {
                count++;
            } else {
                // Since expanding further will only increase subarray size,
                // and max-min won't decrease, break early.
                break;
            }
        }
    }
    return count;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^2)** — For each starting index, we may traverse up to `n` elements, and tracking max/min is constant per iteration.

- **Space Complexity:**  
  **O(1)** — Only variables for max, min, and counters are used.

---

### **Dry Run:**

**Input:**
```plaintext
nums = [1, 3, 2], k = 1
```

| i | j | Subarray | Max | Min | Max-Min | Condition | Count | Explanation |
|---|---|------------|-----|-----|---------|-----------|--------|--------------|
| 0 | 0 | [1]        | 1   | 1   | 0       | yes       | 1      | Single element subarray |
| 0 | 1 | [1,3]      | 3   | 1   | 2       | no        | —      | Exceeds k, break |
| 1 | 1 | [3]        | 3   | 3   | 0       | yes       | 1      | |
| 1 | 2 | [3,2]      | 3   | 2   | 1       | yes       | 2      | |
| 2 | 2 | [2]        | 2   | 2   | 0       | yes       | 1      | |

Total good subarrays = 1 + 1 + 2 + 1 = **5**.

---

## Approach 2: Sliding Window with Monotonic Deques (Most Efficient)

### **Core Idea:**
Use two deques to maintain the maximum and minimum within the current window efficiently, enabling constant-time retrieval of max and min as the window expands or shrinks.

### **Algorithm:**

1. Initialize two deques:
   - `maxDeque` to store indices of elements in decreasing order (front is the maximum).
   - `minDeque` to store indices of elements in increasing order (front is the minimum).
2. Use two pointers (`left` and `right`) to expand and shrink the window:
   - For each `right`, insert `nums[right]` into `maxDeque` and `minDeque` maintaining order.
   - While the difference between `nums[maxDeque.front()]` and `nums[minDeque.front()]` exceeds `k`, shrink the window:
     - Increment `left`.
     - Remove elements from deques if they are out of the current window.
3. For each `right`, the number of valid subarrays ending at `right` is `(right - left + 1)`.
4. Sum these counts to get the total.

---

### **Java Code:**
```java
import java.util.Deque;
import java.util.LinkedList;

public int countGoodSubarrays(int[] nums, int k) {
    int n = nums.length;
    int count = 0;
    int left = 0;

    Deque<Integer> maxDeque = new LinkedList<>();
    Deque<Integer> minDeque = new LinkedList<>();

    for (int right = 0; right < n; right++) {
        // Maintain maxDeque
        while (!maxDeque.isEmpty() && nums[maxDeque.peekLast()] <= nums[right]) {
            maxDeque.pollLast();
        }
        maxDeque.offerLast(right);

        // Maintain minDeque
        while (!minDeque.isEmpty() && nums[minDeque.peekLast()] >= nums[right]) {
            minDeque.pollLast();
        }
        minDeque.offerLast(right);

        // Shrink window if condition is violated
        while (nums[maxDeque.peekFirst()] - nums[minDeque.peekFirst()] > k) {
            if (maxDeque.peekFirst() == left) maxDeque.pollFirst();
            if (minDeque.peekFirst() == left) minDeque.pollFirst();
            left++;
        }

        // Count subarrays ending at right
        count += right - left + 1;
    }
    return count;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** — Each element is added and removed from each deque at most once.

- **Space Complexity:**  
  **O(n)** — Deques store indices of elements, worst case size is `n`.

---

### **Dry Run:**

**Input:**
```plaintext
nums = [1, 3, 2], k = 1
```

| right | maxDeque | minDeque | left | Max | Min | Condition | Subarrays ending at right | Count | Explanation |
|---------|------------|------------|-------|-----|-----|-----------|----------------------------|--------|--------------|
| 0       | [0]        | [0]        | 0     | 1   | 1   | 0 ≤ 1     | 1                          | 1      | [1] |
| 1       | [1, 2]     | [0, 2]     | 0     | 3   | 1   | 2 > 1     | Shrink window: left=1 | | |
|         |            |            | 1     | 3   | 2   | 3 - 2=1 ≤ 1 | Subarrays ending at 1: [3], [1,3] -> count=2 | 2 | |
| 2       | [2]        | [2]        | 1     | 3   | 2   | 3 - 2=1 ≤ 1 | Subarrays ending at 2: [2], [3,2] -> count=2 | 2 + 2=4 | |

Total count = 4.

---

## Summary of Approaches:

| Approach | Time Complexity | Space Complexity | Notes |
|------------|-------------------|--------------------|--------|
| Brute Force | **O(n^2)** | **O(1)** | Simple but inefficient for large inputs. |
| Sliding Window + Deques | **O(n)** | **O(n)** | Most efficient, suitable for large inputs with constraints. |

---

## Final Tips:
- Use sliding window with monotonic deques for optimal performance.
- Maintain max/min efficiently to avoid recomputation.
- Be cautious with large input sizes; prefer the deque approach.
- Test with edge cases, such as all elements equal or strictly increasing/decreasing sequences.

---

This guide equips you with the fundamental and advanced methods to solve "Count the Number of Good Subarrays," suitable for quick revision and interview success.
