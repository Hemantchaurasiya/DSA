# Find Minimum in Rotated Sorted Array II

---

## Problem Overview

Given an array **numbers** that was originally sorted in ascending order but then rotated at an unknown pivot, possibly containing duplicates, find the minimum element in the array.

**Example:**

```
Input: [2, 2, 2, 0, 1]
Output: 0

```

---

## Approaches to Solve the Problem

### 1. Brute Force

### Core Idea:

Traverse the entire array to find the minimum element. Since the array could contain duplicates, we cannot leverage the sorted nature effectively here.

### Algorithm:

- Iterate through all elements in the array.
- Keep track of the smallest element encountered.
- Return the smallest element after traversal.

### Java Code:

```java
public int findMinBruteForce(int[] nums) {
    int min = Integer.MAX_VALUE;
    for (int num : nums) {
        if (num < min) {
            min = num;
        }
    }
    return min;
}

```

### Complexity Analysis:

- **Time Complexity:** O(n) — We scan all elements.
- **Space Complexity:** O(1) — No extra space used.

### Dry Run:

- Input: `[2, 2, 2, 0, 1]`
- Traverse each element:
    - min = ∞ initially
    - After first element (2): min = 2
    - Second (2): min = 2
    - Third (2): min = 2
    - Fourth (0): min = 0
    - Fifth (1): min = 0
- **Output:** 0

---

### 2. Better Solution: Linear Search with Early Exit

*Note:* Similar to brute-force but recognizes the sorted nature if no duplicates or rotated segments are detected. However, with duplicates, this reduces effectiveness.

---

### 3. **Optimized Solution: Modified Binary Search (Handling Duplicates)**

### Core Idea:

Use a **modified binary search** to leverage the partial sorted nature of the array, even with duplicates. The key is to compare mid, left, and right elements to determine the minimum's location.

In the presence of duplicates, the binary search might degenerate to linear in the worst case, but generally performs better than linear search.

### Algorithm:

1. Initialize two pointers: **left = 0**, **right = n - 1**.
2. While **left < right**:
    - Find **mid = (left + right) / 2**.
    - If **nums[mid] > nums[right]**:
        - The minimum is in the right half, so **left = mid + 1**.
    - Else if **nums[mid] < nums[right]**:
        - The minimum is in the left half, so **right = mid**.
    - Else (nums[mid] == nums[right]):
        - Reduce **right** by 1 to skip duplicates.
3. When loop ends, **left** points to the minimum element.

### Java Code:

```java
public int findMin(int[] nums) {
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] > nums[right]) {
            // Minimum in right half
            left = mid + 1;
        } else if (nums[mid] < nums[right]) {
            // Minimum in left half including mid
            right = mid;
        } else {
            // nums[mid] == nums[right], reduce right
            right--;
        }
    }
    return nums[left];
}

```

### Complexity Analysis:

- **Time Complexity:** O(log n) on average, but worst-case O(n) due to duplicates.
- **Space Complexity:** O(1) — No extra space used.

### Dry Run:

- Input: `[2, 2, 2, 0, 1]`

| Step | left | right | mid | nums[mid] | Condition | Action |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 4 | 2 | 2 | nums[mid] == nums[right]? | Yes, reduce right by 1 (4) |
| 2 | 0 | 3 | 1 | 2 | nums[mid] == nums[right]? | Yes, reduce right by 1 (3) |
| 3 | 0 | 2 | 1 | 2 | Yes, reduce right by 1 (2) | right=2, now right=2 |
| 4 | 0 | 2 | 1 | 2 | nums[mid] == nums[right]? | Yes, reduce right by 1 (2) |
| 5 | 0 | 1 | 0 | 2 | nums[mid] > nums[right]? | Yes, left=mid+1=1 |
| 6 | 1 | 1 | 1 | 2 | Loop ends, left=1 | Minimum is nums[1]=0 |
- **Output:** 0

---

## Summary

| Approach | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- |
| **Brute Force** | O(n) | O(1) | Simple, always works, but inefficient for large data |
| **Modified Binary Search (with duplicates)** | O(log n) on average, O(n) worst-case | O(1) | Efficient for most cases, handles duplicates effectively |

---

## Final Tips

- Use the binary search approach for optimal performance.
- Handle duplicates carefully by shrinking the search space when `nums[mid] == nums[right]`.
- Remember that in the worst case (many duplicates), the search may degrade to linear time.

---

**Good luck with your interview prep!**
