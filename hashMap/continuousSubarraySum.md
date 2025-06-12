# Continuous Subarray Sum

## Problem Overview
Given a **circular array** `nums` and an integer `k`, determine if there exists a **continuous subarray** (with length at least 2) whose sum is a multiple of `k`.  
- The subarray can wrap around the end of the array (circular).

---

## 1. Brute Force Approach

### **Core Idea**
Check **all possible subarrays** of length ≥ 2, sum each subarray, and verify if the sum is a multiple of `k`.

### **Algorithm**
1. Loop through each starting index `i`.
2. For each `i`, iterate over subsequent indices `j` (`j > i`).
3. Calculate the sum of the subarray `nums[i..j]`.
4. Check if the sum is divisible by `k`.
5. If yes, return `true`; if no such subarray is found after all checks, return `false`.

### **Java Code**

```java
public boolean checkSubarraySumBruteForce(int[] nums, int k) {
    int n = nums.length;
    for (int start = 0; start < n; start++) {
        int sum = 0;
        for (int end = start; end < n; end++) {
            sum += nums[end];
            if (end - start + 1 >= 2 && k != 0 && sum % k == 0) {
                return true;
            }
        }
    }
    return false;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n²)
  - Nested loops over all subarrays.
- **Space Complexity:** O(1)
  - Uses only a few variables.

### **Dry Run (Example):**
`nums = [23, 2, 4, 6, 7], k = 6`

| start | end | Subarray | Sum | Is sum % k == 0? |
|--------|-------|------------|-------|------------------|
| 0      | 1     | [23, 2]    | 25    | No               |
| 0      | 2     | [23, 2, 4] | 29    | No               |
| 0      | 3     | [23, 2, 4, 6]| 35  | No               |
| 0      | 4     | [23, 2, 4, 6, 7]| 42 | Yes (42 % 6 = 0) |

Result: `true` at subarray `[2, 4, 6, 7]` sum = 19, but need to check carefully; actually, the sum `[23, 2, 4, 6]` = 35, which is not divisible by 6, but `[2, 4, 6, 7]` sum = 19, not divisible by 6. The subarray `[2, 4, 6]` sum = 12, which is divisible by 6, so the result is `true`.

---

## 2. Better Solution: Prefix Sum + Modulo Check

### **Core Idea**
Utilize **prefix sums** and **modulo operation**:
- For a subarray sum to be a multiple of `k`, the **difference** between two prefix sums should be divisible by `k`.
- If two prefix sums give the same remainder when divided by `k`, the subarray between them is divisible by `k`.

### **Algorithm**
1. Initialize a hash map to store the **remainder** of prefix sums and their earliest index.
2. Start with `remainder 0` at index `-1`.
3. Iterate through `nums`:
   - Update the running prefix sum.
   - Compute `remainder = prefixSum % k`.
   - If the same `remainder` has been seen before at index `prevIndex`, check if the subarray length `(currentIndex - prevIndex)` ≥ 2.
   - If yes, return `true`.
   - Else, store this `remainder` with the current index if not already stored.
4. If no such subarray is found, return `false`.

### **Java Code**

```java
import java.util.HashMap;

public boolean checkSubarraySum(int[] nums, int k) {
    HashMap<Integer, Integer> remainderIndexMap = new HashMap<>();
    remainderIndexMap.put(0, -1); // base case
    int prefixSum = 0;

    for (int i = 0; i < nums.length; i++) {
        prefixSum += nums[i];
        int remainder = k != 0 ? prefixSum % k : prefixSum; // handle k=0

        if (remainderIndexMap.containsKey(remainder)) {
            int prevIndex = remainderIndexMap.get(remainder);
            if (i - prevIndex >= 2) {
                return true;
            }
        } else {
            remainderIndexMap.put(remainder, i);
        }
    }

    return false;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n)
- **Space Complexity:** O(min(n, k)) (since at most we store `k` remainders, but in worst case, O(n))

### **Dry Run (Example):**
`nums = [23, 2, 4, 6, 7], k = 6`

| i | num | prefixSum | remainder | Map | prevIndex | Length | Check? |
|---|-------|------------|-----------|-----|-----------|---------|--------|
| 0 | 23    | 23         | 23 % 6=5  | {0:-1, 5:0} | - | - | - |
| 1 | 2     | 25         | 25 % 6=1  | {0:-1, 5:0, 1:1} | - | - | - |
| 2 | 4     | 29         | 29 % 6=5  | {0:-1, 5:0, 1:1} | 0 | i - prevIndex=2-0=2 >=2? Yes | return true |

Result: `true` because subarray `[2, 4]` sum = 6 which is divisible by 6.

---

## 3. Most Optimal Solution: Prefix Sum + HashMap (O(n))

This approach is the most efficient, leveraging prefix sums and mod operation, suitable for large inputs and linear time constraints.

---

## **Summary**

| Approach                   | Time Complexity | Space Complexity | Notes                                |
|----------------------------|-----------------|------------------|-------------------------------------|
| Brute Force                | O(n²)          | O(1)             | Checks all subarrays, slow          |
| Prefix Sum + Modulo Check  | O(n)           | O(n)             | Efficient, uses remainders to detect subarrays |

---

## **Key Takeaways**
- The key to solving "Continuous Subarray Sum" is understanding the relationship between prefix sums and remainders.
- When the same remainder appears at two different indices, the subarray between these indices has a sum divisible by `k`.
- Handle the edge case when `k=0` separately, as modulo operation is undefined for zero.

---

**Happy Coding & Best of luck in your interviews!**
