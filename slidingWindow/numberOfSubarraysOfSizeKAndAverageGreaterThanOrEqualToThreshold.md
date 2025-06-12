# Number of Sub-arrays of Size K and Average Greater than or Equal to Threshold

---

## Problem Statement
Given an array `nums` of integers, an integer `k` denoting the size of subarrays, and an integer `threshold`, determine how many subarrays of size `k` have an average greater than or equal to `threshold`.

Mathematically:
- Count the number of subarrays `sub` of length `k` where:
  
  \(\frac{\sum_{i \in sub} nums[i]}{k} \geq threshold\)

- Equivalently:
  
  \(\sum_{i \in sub} nums[i] \geq k \times threshold\)

---

## Approach 1: Brute Force

### Core Idea
Enumerate all possible subarrays of size `k`, compute their sum, and check if the sum meets the threshold condition.

### Algorithm
1. Loop through the array from index `0` to `nums.length - k`.
2. For each starting index, sum the next `k` elements.
3. Check if the sum is greater than or equal to `k * threshold`.
4. Increment count if condition is satisfied.
5. Return the total count after processing all subarrays.

### Java Code
```java
public int numOfSubarraysBruteForce(int[] nums, int k, int threshold) {
    int count = 0;
    int targetSum = k * threshold;
    for (int i = 0; i <= nums.length - k; i++) {
        int sum = 0;
        for (int j = i; j < i + k; j++) {
            sum += nums[j];
        }
        if (sum >= targetSum) {
            count++;
        }
    }
    return count;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n * k)*  
  For each starting index, summing `k` elements takes `O(k)`, and there are approximately `n` starting points.
- **Space Complexity:** *O(1)*  
  Only a few variables used, no additional data structures.

### Dry Run
- Input: `nums = [2, 2, 1, 3, 4]`, `k = 3`, `threshold = 3`
- Target sum = 3 * 3 = 9
- Subarrays:
  - `[2, 2, 1]` → sum = 5 (not >= 9)
  - `[2, 1, 3]` → sum = 6 (not >= 9)
  - `[1, 3, 4]` → sum = 8 (not >= 9)
- **Result:** 0

---

## Approach 2: Sliding Window (Better Solution)

### Core Idea
Use a sliding window of size `k` to efficiently compute the sum of subarrays, updating the sum as the window moves, avoiding recomputation.

### Algorithm
1. Calculate the sum of the first `k` elements.
2. Initialize `count` if the sum meets the threshold.
3. Loop from index `k` to `nums.length - 1`:
   - Subtract the element leaving the window (`nums[i - k]`)
   - Add the new element entering the window (`nums[i]`)
   - Check if the current sum meets the threshold, increment count accordingly.
4. Return the count.

### Java Code
```java
public int numOfSubarraysSlidingWindow(int[] nums, int k, int threshold) {
    int count = 0;
    int targetSum = k * threshold;
    int windowSum = 0;
    
    // Initialize the first window sum
    for (int i = 0; i < k; i++) {
        windowSum += nums[i];
    }
    if (windowSum >= targetSum) {
        count++;
    }
    
    // Slide the window
    for (int i = k; i < nums.length; i++) {
        windowSum -= nums[i - k];    // Remove the element leaving the window
        windowSum += nums[i];        // Add the new element
        if (windowSum >= targetSum) {
            count++;
        }
    }
    return count;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*  
  Each element is added once and removed once; total operations proportional to array length.
- **Space Complexity:** *O(1)*  
  Only a few variables used for sum and counters.

### Dry Run
- Input: `nums = [2, 2, 1, 3, 4]`, `k = 3`, `threshold = 3`
- Target sum = 9
- Initial window `[2, 2, 1]`: sum = 5 (<9)
- Move window:
  - Remove `2` (old first element): sum = 5 - 2 = 3
  - Add `3`: sum = 3 + 3 = 6 (<9)
  - Next:
    - Remove `2`: sum = 6 - 2 = 4
    - Add `4`: sum = 4 + 4 = 8 (<9)
- No window reaches sum >= 9, so result is 0.

---

## Approach 3: Optimized Sliding Window with Early Checks (Most Efficient)

This is essentially the same as Approach 2 but emphasizes the constant-time sum update and clear condition check.

### Core Idea
Maintain a running sum with a sliding window, compare with the threshold sum, and count valid subarrays efficiently.

### Algorithm
Same as Approach 2:
- Initialize the sum of the first window.
- Slide through the array, updating the sum in O(1) time.
- Count subarrays where sum >= `k * threshold`.

### Java Code
```java
public int numOfSubarraysOptimized(int[] nums, int k, int threshold) {
    int count = 0;
    int targetSum = k * threshold;
    int windowSum = 0;
    
    // Initial window sum
    for (int i = 0; i < k; i++) {
        windowSum += nums[i];
    }
    if (windowSum >= targetSum) {
        count++;
    }
    
    // Slide window
    for (int i = k; i < nums.length; i++) {
        windowSum += nums[i] - nums[i - k];
        if (windowSum >= targetSum) {
            count++;
        }
    }
    return count;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*, with constant-time updates.
- **Space Complexity:** *O(1)*.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Key Points |
|------------|--------------|-------------------|---------------------|--------------|
| Brute Force | Check all subarrays, sum each | O(n * k) | O(1) | Inefficient for large inputs |
| Sliding Window | Maintain sum dynamically, slide window | O(n) | O(1) | Optimal for large inputs |
| Optimized Sliding Window | Same as above with clear sum update | O(n) | O(1) | Most efficient |

---

## Final Tips
- Use sliding window techniques for subarray sum problems to achieve linear time.
- Remember to compare sum with `k * threshold` instead of computing averages repeatedly.
- Handle edge cases such as empty array or `k` larger than array length.

---

**Happy Revising!**
