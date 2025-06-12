# Subarray Sum Equals K

## Problem Overview
Given an array of integers `nums` and an integer `k`, **return the total number of continuous subarrays whose sum equals `k`**.

---

## 1. Brute Force Approach

### **Core Idea**
Check **every possible subarray** and count those with sum equal to `k`. This is the most straightforward method but inefficient.

### **Algorithm**
- Use **two nested loops**:
  - Outer loop picks the start index `i`.
  - Inner loop picks the end index `j` (`j >= i`).
  - For each subarray `nums[i..j]`, calculate the sum.
  - If the sum equals `k`, increment the count.
- Sum calculation can be optimized by keeping a running sum within the inner loop.

### **Java Code**

```java
public int subarraySumBruteForce(int[] nums, int k) {
    int count = 0;
    for (int i = 0; i < nums.length; i++) {
        int sum = 0;
        for (int j = i; j < nums.length; j++) {
            sum += nums[j];
            if (sum == k) {
                count++;
            }
        }
    }
    return count;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n²)
  - Nested loops iterate over all subarrays.
- **Space Complexity:** O(1)
  - No extra space used besides variables.

### **Dry Run (Example):**
`nums = [1, 1, 1], k = 2`

| i | j | Subarray | Sum | Count? |
|---|---|------------|-------|---------|
| 0 | 0 | [1]        | 1     | No      |
| 0 | 1 | [1, 1]     | 2     | Yes → count=1 |
| 0 | 2 | [1, 1, 1]  | 3     | No      |
| 1 | 1 | [1]        | 1     | No      |
| 1 | 2 | [1, 1]     | 2     | Yes → count=2 |
| 2 | 2 | [1]        | 1     | No      |

Total count = 2

---

## 2. Better Solution: Prefix Sum with HashMap

### **Core Idea**
Use **prefix sums** to efficiently determine if a subarray sums to `k`.  
- The prefix sum up to index `i` is `sum[0..i]`.
- For a subarray `nums[i+1..j]` to sum to `k`, we need:
  
  `prefixSum[j] - prefixSum[i] = k`  
  => `prefixSum[i] = prefixSum[j] - k`
  
- Maintain a **hash map** to count how many prefix sums have been seen so far.

### **Algorithm**
1. Initialize a `HashMap` to store counts of prefix sums. Start with `{0:1}` because a prefix sum of zero occurs once (empty subarray).
2. Initialize `currentSum = 0` and `result = 0`.
3. Iterate through `nums`:
   - Update `currentSum += nums[i]`.
   - Check if `(currentSum - k)` exists in the map:
     - If yes, it means there's a subarray ending at `i` that sums to `k`.
     - Add the count of `(currentSum - k)` in the map to the result.
   - Add/update the current prefix sum in the map.

### **Java Code**

```java
import java.util.HashMap;

public int subarraySumHashMap(int[] nums, int k) {
    HashMap<Integer, Integer> prefixSumCount = new HashMap<>();
    prefixSumCount.put(0, 1); // base case
    int currentSum = 0;
    int result = 0;

    for (int num : nums) {
        currentSum += num;
        if (prefixSumCount.containsKey(currentSum - k)) {
            result += prefixSumCount.get(currentSum - k);
        }
        prefixSumCount.put(currentSum, prefixSumCount.getOrDefault(currentSum, 0) + 1);
    }

    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n)
  - Single pass through `nums`.
- **Space Complexity:** O(n)
  - HashMap can store up to `n` prefix sums in the worst case.

### **Dry Run (Example):**
`nums = [1, 1, 1], k = 2`

| i | num | currentSum | Check `currentSum - k` | Map Content | result |
|---|-------|--------------|------------------------|--------------|---------|
| 0 | 1     | 1            | -1 (not in map)       | {0:1, 1:1}   | 0       |
| 1 | 1     | 2            | 0 (in map, count=1)   | {0:1, 1:1, 2:1} | 1 |
| 2 | 1     | 3            | 1 (in map, count=1)   | {0:1, 1:1, 2:1, 3:1} | 2 |

Total count = 2

---

## 3. Most Optimal Solution: Prefix Sum + HashMap (O(n))

This approach combines prefix sums with a hash map for **linear time complexity**, making it the most efficient solution suitable for large inputs.

---

## **Summary**

| Approach                  | Time Complexity | Space Complexity | Notes                                |
|---------------------------|-----------------|------------------|-------------------------------------|
| Brute Force               | O(n²)          | O(1)             | Easy to understand, slow for large input |
| Prefix Sum + HashMap      | O(n)           | O(n)             | Efficient, optimal for interviews   |

---

## **Key Takeaways**
- Always consider prefix sums for subarray sum problems.
- Use a hash map to store counts of prefix sums to achieve O(n) complexity.
- Carefully initialize the hash map with `{0:1}` to handle subarrays starting from index 0.
- Understand how prefix sums relate to subarray sums and leverage hash maps for quick lookups.

---

**Happy Coding & Good Luck with your interviews!**
