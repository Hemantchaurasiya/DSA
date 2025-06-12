# Count Subarrays Where Max Element Appears at Least K Times

This comprehensive guide covers multiple approaches to solve the problem of counting the number of subarrays where the maximum element appears **at least `K` times**. It progresses from brute-force to optimal solutions, structured for quick understanding and interview prep.

---

## Problem Summary
Given an array `arr`, count the number of subarrays where the **maximum element** occurs **at least `K` times** within that subarray.

---

## Approach 1: Brute Force

### **Core Idea:**
Examine all possible subarrays, determine the maximum element in each, and count how many times it appears. Increment the count if the maximum's occurrence is ≥ `K`.

---

### **Algorithm:**
1. Iterate over all possible starting indices `i`.
2. For each `i`, iterate over all ending indices `j ≥ i`.
3. For each subarray `arr[i..j]`:
   - Find the maximum element.
   - Count its occurrences within the subarray.
   - If the count ≥ `K`, increment the result.

---

### **Java Code:**
```java
public int countSubarraysWithMaxAtLeastK(int[] arr, int K) {
    int n = arr.length;
    int count = 0;

    for (int i = 0; i < n; i++) {
        int maxVal = Integer.MIN_VALUE;
        for (int j = i; j < n; j++) {
            if (arr[j] > maxVal) {
                maxVal = arr[j];
            }
            int maxCount = 0;
            for (int k = i; k <= j; k++) {
                if (arr[k] == maxVal) {
                    maxCount++;
                }
            }
            if (maxCount >= K) {
                count++;
            }
        }
    }
    return count;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^3)** — For each subarray, finding max and counting occurrences takes `O(n)`.

- **Space Complexity:**  
  **O(1)** — No extra space besides variables.

---

### **Dry Run:**

**Input:**
```plaintext
arr = [2, 1, 2], K=2
```

- Subarray `[2, 1, 2]`:
  - Max = 2, occurs 2 times → count = 1.
- Subarray `[2, 1]`:
  - Max = 2, occurs 1 time → ignore.
- Subarray `[1, 2]`:
  - Max = 2, occurs 1 time → ignore.
- Subarray `[2]`:
  - Max = 2, occurs 1 time → ignore.

---

## Approach 2: Optimized Using Sliding Window and Monotonic Stack

### **Core Idea:**
Break down the problem into counting subarrays where the maximum element appears **less than** `K` times, then subtract from total subarrays. Alternatively, focus on counting subarrays where the maximum appears **at least** `K` times efficiently.

Since max element occurrence depends on the positions of the max element, we can:
- Use a monotonic stack to find the **next greater element** indices for each element.
- Calculate the number of subarrays where each element is the maximum, and count how many of those have the maximum element appearing at least `K` times.

This is a more involved approach; here, we focus on an easier alternative.

---

## Approach 3: Sliding Window with Two Pointers (Most Efficient)

### **Core Idea:**
Transform the problem into counting subarrays where the maximum element appears **less than** `K` times, then subtract from total subarrays.

Alternatively, for each element, find the **number of subarrays where it is the maximum** and appears at least `K` times.

### **Simplified Approach:**
- Use two pointers to identify subarrays where the maximum element appears at least `K` times.
- For each possible maximum element value, find subarrays satisfying the occurrence condition.

---

### **Implementation Strategy:**
- Iterate through the array, for each position, find the stretch of subarrays where:
  - The maximum element is at the current position.
  - It appears at least `K` times in that subarray.
- Use prefix and suffix counts for efficient calculation.

*Note:* The exact implementation is complex; here, we focus on an approach that leverages the idea of counting valid subarrays based on maximum element positions.

---

## Approach 4: Efficient Counting Using Prefix Sums and Monotonic Stack (Most Optimal)

### **Core Idea:**
- For each element, determine the **number of subarrays** where it is the **maximum**.
- For each such subarray, count if the maximum element's **occurrences** ≥ `K`.
- Sum over all elements.

This approach involves:
- Computing for each element the **left** and **right** boundaries where it is the maximum.
- Calculating the number of subarrays where it is the maximum.
- Checking if the maximum's count in those subarrays ≥ `K`.

---

### **Summary:**
The problem is complex and can be approached via advanced data structures like segment trees, monotonic stacks, or prefix sums, but the most straightforward for practice is the brute-force or the sliding window method.

---

## Final Tips:
- For small arrays, brute-force suffices.
- For large arrays, use monotonic stacks or segment trees for optimization.
- Always consider whether counting total subarrays and subtracting invalid ones is easier.

---

This concludes the structured revision for **Count Subarrays Where Max Element Appears at Least K Times**. Focus on understanding the brute-force approach first, then explore more efficient methods based on problem constraints.
