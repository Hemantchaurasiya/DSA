# Maximum Sum of Distinct Subarrays With Length K

---

## Problem Statement
Given an integer array `nums` and an integer `k`, find the maximum sum among all subarrays of length `k` that contain **distinct elements** (no duplicates).

---

## Approach 1: Brute Force

### Core Idea
Check every possible subarray of size `k`, verify if it contains all distinct elements, and if so, compute its sum. Keep track of the maximum sum encountered.

### Algorithm
1. Loop through the array from index `0` to `nums.length - k`.
2. For each starting index:
    - Extract the subarray of length `k`.
    - Check if all elements are distinct.
    - If distinct, sum the elements.
    - Update the maximum sum if current sum is higher.
3. Return the maximum sum after processing all subarrays.

### Java Code
```java
public int maxSumOfDistinctSubarraysBruteForce(int[] nums, int k) {
    int maxSum = 0;
    for (int i = 0; i <= nums.length - k; i++) {
        Set<Integer> seen = new HashSet<>();
        int sum = 0;
        boolean allDistinct = true;
        for (int j = i; j < i + k; j++) {
            if (seen.contains(nums[j])) {
                allDistinct = false;
                break;
            }
            seen.add(nums[j]);
            sum += nums[j];
        }
        if (allDistinct) {
            maxSum = Math.max(maxSum, sum);
        }
    }
    return maxSum;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n * k)*  
  For each subarray, checking for duplicates and summing takes `O(k)`.
- **Space Complexity:** *O(k)* for the set used to check duplicates.

### Dry Run
- Input: `nums = [4, 1, 1, 2, 3, 4, 1, 2]`, `k = 3`
- Subarrays:
  - `[4, 1, 1]` → duplicates, ignore
  - `[1, 1, 2]` → duplicates, ignore
  - `[1, 2, 3]` → distinct, sum = 6
  - `[2, 3, 4]` → distinct, sum = 9
  - `[3, 4, 1]` → distinct, sum = 8
  - `[4, 1, 2]` → distinct, sum = 7
- **Maximum sum:** 9

---

## Approach 2: Sliding Window with HashSet

### Core Idea
Use a sliding window of size `k` to keep track of elements and ensure all are distinct. If a duplicate is found, move the start pointer until duplicates are removed. Keep track of the sum of the current window.

### Algorithm
1. Initialize two pointers: `left = 0`, `maxSum = 0`, and `currentSum = 0`.
2. Use a HashSet to store current window elements.
3. Iterate `right` from 0 to `nums.length - 1`:
   - Add `nums[right]` to the set and `currentSum`.
   - If `nums[right]` is already in the set, move `left` forward, removing elements until duplicates are eliminated.
   - When the window size reaches `k`, check if all elements are distinct, update `maxSum`.
   - Slide the window forward by removing `nums[left]` and updating `currentSum`.
4. Return `maxSum`.

### Java Code
```java
public int maxSumOfDistinctSubarraysSlidingWindow(int[] nums, int k) {
    Set<Integer> windowSet = new HashSet<>();
    int maxSum = 0, currentSum = 0;
    int left = 0;

    for (int right = 0; right < nums.length; right++) {
        // Remove duplicates in the window
        while (windowSet.contains(nums[right])) {
            // Remove element at 'left'
            windowSet.remove(nums[left]);
            currentSum -= nums[left];
            left++;
        }
        // Add new element
        windowSet.add(nums[right]);
        currentSum += nums[right];

        // When window size reaches k, check for maximum sum
        if (right - left + 1 == k) {
            maxSum = Math.max(maxSum, currentSum);
            // Shrink window from left for next iteration
            windowSet.remove(nums[left]);
            currentSum -= nums[left];
            left++;
        }
    }
    return maxSum;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*  
  Each element is added and removed at most once.
- **Space Complexity:** *O(k)* for the set.

### Dry Run
- Input: `nums = [4, 1, 1, 2, 3, 4, 1, 2]`, `k = 3`
- Process:
  - `right=0`: window `[4]`, size < 3
  - `right=1`: window `[4,1]`, size < 3
  - `right=2`: window `[4,1,1]` → duplicate `1`, remove `4`, window `[1,1]`, sum=2, not valid as size=2
  - Expand window after removing duplicates:
    - After removing `4`, window `[1,1]`, sum=2
  - When window size hits 3 with distinct elements:
    - For example, when `right=4`:
      - `nums[2]` and `nums[3]` are processed, updates accordingly
    - Maximum sum among valid windows is 9 for `[2,3,4]`
- Result: 9

---

## Approach 3: Optimized Sliding Window with Frequency Map (Most Efficient)

### Core Idea
Use a frequency map to keep track of element counts within the window. Expand the window to size `k`, and shrink when duplicates appear, updating the sum accordingly.

### Algorithm
1. Initialize pointers `left=0`, `maxSum=0`, `currentSum=0`.
2. Use a HashMap to store element counts.
3. Expand `right`:
   - Add `nums[right]` to the map and update `currentSum`.
   - If a duplicate is detected (count > 1), move `left` forward, decrementing counts and `currentSum` until duplicates are resolved.
   - When the window size is `k`, update `maxSum`.
4. Return `maxSum`.

### Java Code
```java
public int maxSumOfDistinctSubarraysOptimized(int[] nums, int k) {
    Map<Integer, Integer> freqMap = new HashMap<>();
    int maxSum = 0, currentSum = 0;
    int left = 0;

    for (int right = 0; right < nums.length; right++) {
        freqMap.put(nums[right], freqMap.getOrDefault(nums[right], 0) + 1);
        currentSum += nums[right];

        // If duplicate exists, shrink window
        while (freqMap.get(nums[right]) > 1) {
            freqMap.put(nums[left], freqMap.get(nums[left]) - 1);
            currentSum -= nums[left];
            left++;
        }

        // When window size reaches k
        if (right - left + 1 == k) {
            maxSum = Math.max(maxSum, currentSum);
            // Shrink window from the left for the next iteration
            freqMap.put(nums[left], freqMap.get(nums[left]) - 1);
            currentSum -= nums[left];
            left++;
        }
    }
    return maxSum;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*  
  Each element is processed once, with constant updates.
- **Space Complexity:** *O(k)* for the map.

### Dry Run
- Input: `nums = [4, 1, 1, 2, 3, 4, 1, 2]`, `k=3`
- Process:
  - Expand window, updating counts and sum.
  - When duplicates appear (`1` occurs twice), shrink window from the left.
  - Track maximum sum for windows with all distinct elements of size `k`.
- Final answer: 9 from `[2,3,4]`.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Key Points |
|--------------|--------------|-------------------|---------------------|--------------|
| Brute Force | Check all subarrays, verify distinctness | O(n * k) | O(k) | Not scalable |
| Sliding Window | Use set to maintain distinctness, slide window | O(n) | O(k) | Efficient for large data |
| Frequency Map | Use hash map to track counts, maintain window | O(n) | O(k) | Most flexible, handles duplicates efficiently |

---

## Final Tips
- Always prefer sliding window or hash map-based methods for subarray problems with constraints on element uniqueness.
- Maintain current sum efficiently to avoid recomputation.
- Handle edge cases like empty arrays or `k` larger than array length.

---

**Happy Revising!**
