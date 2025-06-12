# Count Nice Pairs in an Array

---

## Problem Overview

Given an array `nums`, a **nice pair** is defined as a pair of indices `(i, j)` such that:

```
i < j and (nums[i] + nums[j]) == (i + j)
```

Your task is to **count the total number of nice pairs** in the array.

---

## Approach 1: Brute Force

### **Core Idea**
Check all pairs `(i, j)` with `i < j` and verify if they satisfy the condition.

### **Algorithm**
1. Initialize a counter `count` to 0.
2. Loop through all pairs `(i, j)` where `i < j`.
3. For each pair:
   - Check if `nums[i] + nums[j] == i + j`.
   - If true, increment `count`.
4. Return `count`.

### **Java Code**
```java
public class Solution {
    public int countNicePairs(int[] nums) {
        int count = 0;
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (nums[i] + nums[j] == i + j) {
                    count++;
                }
            }
        }
        return count;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(N^2)` due to nested loops over all pairs.
- **Space Complexity:**  
  `O(1)` (no extra space apart from variables).

---

## Approach 2: Mathematical Transformation + Hash Map (Optimized)

### **Core Idea**
Transform the condition to a form that allows counting pairs efficiently:

```
nums[i] + nums[j] == i + j
=> (nums[i] - i) == (nums[j] - j)
```

So, pairs are formed when `(nums[i] - i)` is equal for different indices.

### **Algorithm**
1. Initialize a hash map to count occurrences of `(nums[i] - i)`.
2. Loop through the array:
   - Calculate `key = nums[i] - i`.
   - If `key` has been seen before, the number of pairs formed with current index is equal to the current count of `key`.
   - Increment the count of `key` in the map.
3. Sum up all such pairs during iteration.
4. Return the total count.

### **Java Code**
```java
import java.util.*;

public class Solution {
    public int countNicePairs(int[] nums) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            int key = nums[i] - i;
            int freq = freqMap.getOrDefault(key, 0);
            count += freq; // number of pairs with existing identical key
            freqMap.put(key, freq + 1);
        }
        return count;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(N)` — single pass through array.
- **Space Complexity:**  
  `O(N)` — in the worst case, all `(nums[i] - i)` are distinct.

---

## **Dry Run: Step-by-step Example**

**Input:**
```plaintext
nums = [1, 2, 3, 4]
```

### Step 1: Initialization
- `freqMap = {}`  
- `count = 0`

### Step 2: Iteration
| i | nums[i] | key = nums[i] - i | freqMap before update | count before update | Update freqMap | count after update |
|---|---------|-------------------|-----------------------|---------------------|----------------|-------------------|
| 0 | 1       | 1 - 0 = 1         | {}                    | 0                   | {1: 1}       | 0                 |
| 1 | 2       | 2 - 1 = 1         | {1: 1}                | 0                   | {1: 2}       | 1 (pairs with previous 1) |
| 2 | 3       | 3 - 2 = 1         | {1: 2}                | 1                   | {1: 3}       | 3 (pairs with previous 2) |
| 3 | 4       | 4 - 3 = 1         | {1: 3}                | 3                   | {1: 4}       | 6 (pairs with previous 3) |

**Total nice pairs = 6**

---

## Summary Table

| Approach | Core Idea | Algorithm Highlights | Code Snippet | Time Complexity | Space Complexity |
|------------|------------|------------------------|--------------|-----------------|------------------|
| Brute Force | Check all pairs | Nested loops, direct comparison | Provided above | `O(N^2)` | `O(1)` |
| Mathematical Transformation + Hash Map | Map `(nums[i] - i)` to counts | Single pass, count pairs using map | Provided above | `O(N)` | `O(N)` |

---

## Final Tips
- Recognize the algebraic manipulation that simplifies the problem.
- Use hashing to efficiently count and combine pairs.
- Be cautious about integer overflow in large inputs (not a concern here unless constraints are huge).
- Practice similar problems involving transforming conditions for efficient counting.

---

This comprehensive guide should help you understand and implement an optimal solution efficiently, preparing you well for interviews!
