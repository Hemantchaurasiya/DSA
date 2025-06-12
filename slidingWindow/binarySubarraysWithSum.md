# Binary Subarrays With Sum

## Problem Overview
Given a binary array `nums` and an integer `goal`, **return the number of non-empty subarrays with sum equal to `goal`**.  
- A subarray is a contiguous sequence within the array.

---

## 1. Brute Force Approach

### **Core Idea**
Check **every possible subarray** and count those with sum equal to `goal`. Since `nums` contains only 0s and 1s, summing subarrays is straightforward.

### **Algorithm**
1. Loop through each start index `i`.
2. For each `i`, iterate through each end index `j` (`j >= i`).
3. Calculate the sum of the subarray `nums[i..j]`.
4. If the sum equals `goal`, increment the count.
5. Return the total count after checking all subarrays.

### **Java Code**

```java
public int numSubarraysWithSumBruteForce(int[] nums, int goal) {
    int count = 0;
    int n = nums.length;
    for (int start = 0; start < n; start++) {
        int sum = 0;
        for (int end = start; end < n; end++) {
            sum += nums[end];
            if (sum == goal) {
                count++;
            }
        }
    }
    return count;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n²)
  - Nested loops over all subarrays.
- **Space Complexity:** O(1)
  - Only a few variables used.

### **Dry Run (Example):**
`nums = [1, 0, 1, 0, 1], goal = 2`

| start | end | Subarray | Sum | Count? |
|--------|-------|------------|--------|----------|
| 0      | 0     | [1]        | 1      | No       |
| 0      | 1     | [1, 0]     | 1      | No       |
| 0      | 2     | [1, 0, 1]  | 2      | Yes → count=1 |
| 0      | 3     | [1, 0, 1, 0]| 2     | Yes → count=2 |
| 0      | 4     | [1, 0, 1, 0, 1]| 3 | No       |
| 1      | 1     | [0]        | 0      | No       |
| 1      | 2     | [0, 1]     | 1      | No       |
| 1      | 3     | [0, 1, 0]  | 1      | No       |
| 1      | 4     | [0, 1, 0, 1]| 2     | Yes → count=3 |
| 2      | 2     | [1]        | 1      | No       |
| 2      | 3     | [1, 0]     | 1      | No       |
| 2      | 4     | [1, 0, 1]  | 2      | Yes → count=4 |

Total count = 4

---

## 2. Better Solution: Sliding Window (for specific cases)

### **Core Idea**
When `goal` is 0, the problem simplifies to counting subarrays of consecutive zeros.  
For `goal > 0`, a sliding window approach can be used with some modifications, but it's complicated for arbitrary goals.

**Note:** For the general case with arbitrary goal, the sliding window approach isn't straightforward due to the need to handle zeros and ones differently. So, most solutions rely on prefix sums and hash maps.

---

## 3. Most Optimal Solution: Prefix Sum + HashMap

### **Core Idea**
Use **prefix sums** to count subarrays efficiently:
- Since `nums` is binary, the sum of a subarray is just the count of 1s.
- Maintain a **frequency map** of prefix sums encountered so far.
- For each index, determine how many previous prefix sums differ by `goal`.

### **Algorithm**
1. Initialize a hash map `prefixSumCount` to store counts of prefix sums, starting with `{0:1}`.
2. Keep a running `currentSum`.
3. For each element:
   - Update `currentSum += nums[i]`.
   - Check if `(currentSum - goal)` exists in the map:
     - If yes, add its count to the result.
   - Increment the count of `currentSum` in the map.
4. Return the total count.

### **Java Code**

```java
import java.util.HashMap;

public int numSubarraysWithSum(int[] nums, int goal) {
    HashMap<Integer, Integer> prefixSumFreq = new HashMap<>();
    prefixSumFreq.put(0, 1); // base case: prefix sum zero occurs once
    int currentSum = 0;
    int result = 0;

    for (int num : nums) {
        currentSum += num;
        // Check if there's a prefix sum that when subtracted from currentSum equals goal
        if (prefixSumFreq.containsKey(currentSum - goal)) {
            result += prefixSumFreq.get(currentSum - goal);
        }
        // Update the frequency map
        prefixSumFreq.put(currentSum, prefixSumFreq.getOrDefault(currentSum, 0) + 1);
    }

    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n)
- **Space Complexity:** O(n)

### **Dry Run (Example):**
`nums = [1, 0, 1, 0, 1], goal=2`

| i | num | currentSum | currentSum - goal | Map | Result |
|---|-----|--------------|--------------------|-----|---------|
| 0 | 1   | 1            | -1 (not in map)   | {0:1, 1:1} | 0 |
| 1 | 0   | 1            | -1               | {0:1, 1:2} | 0 |
| 2 | 1   | 2            | 0                | {0:2, 1:2, 2:1} | 1 (since prefix sum 0 occurs once) |
| 3 | 0   | 2            | 0                | {0:2, 1:2, 2:2} | 2 (another prefix sum 0) |
| 4 | 1   | 3            | 1                | {0:2, 1:2, 2:2, 3:1} | 3 (prefix sum 1 occurs twice) |

Total count = 4

---

## **Summary**

| Approach                       | Time Complexity | Space Complexity | Notes                                              |
|--------------------------------|-----------------|------------------|---------------------------------------------------|
| Brute Force                   | O(n²)          | O(1)             | Checks all subarrays, slow                        |
| Prefix Sum + HashMap          | O(n)           | O(n)             | Efficient, leverages prefix sums and counts       |

---

## **Key Takeaways**
- For binary arrays, counting subarrays with sum = `goal` can be optimized using prefix sums and hash maps.
- The core idea is that the difference in prefix sums corresponds to subarrays summing to `goal`.
- Handle special cases when `goal` is 0 or when the array contains only zeros.

---

**Happy coding and good luck with your interviews!**
