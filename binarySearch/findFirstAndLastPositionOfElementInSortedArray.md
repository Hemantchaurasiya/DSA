# Find First and Last Position of Element in Sorted Array

This problem asks us to find the starting and ending positions of a target element in a sorted array. If the target isn't present, return `[-1, -1]`. The array is sorted, which hints at efficient searching strategies.

---

## 1. Brute Force Approach

### Core Idea:

Scan the entire array to find all occurrences of the target, then determine the first and last positions.

### Algorithm:

- Iterate through the array from start to end.
- Whenever the element equals the target, record the index.
- After traversal, if the target is found, return the first and last occurrence indices.
- If not found, return `[-1, -1]`.

### Java Code:

```java
public int[] searchRange(int[] nums, int target) {
    int first = -1, last = -1;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] == target) {
            if (first == -1) first = i;
            last = i;
        }
    }
    return new int[] {first, last};
}

```

### Complexity Analysis:

- **Time Complexity:** **O(n)**, as we scan the entire array.
- **Space Complexity:** **O(1)**, only constant extra space.

### Dry Run:

**Input:** `nums = [5,7,7,8,8,10], target = 8`

| Index | Element | Action | First | Last |
| --- | --- | --- | --- | --- |
| 0 | 5 | No | -1 | -1 |
| 1 | 7 | No | -1 | -1 |
| 2 | 7 | No | -1 | -1 |
| 3 | 8 | Found target, first=3, last=3 | 3 | 3 |
| 4 | 8 | Update last=4 | 3 | 4 |
| 5 | 10 | No | 3 | 4 |

**Result:** `[3, 4]`

---

## 2. Better Solution: Binary Search for First and Last Occurrences

### Core Idea:

Leverage the sorted nature of the array to perform two binary searches:

- One for the **first occurrence** of the target.
- One for the **last occurrence** of the target.

This reduces the time complexity from linear to logarithmic.

### Algorithm:

1. **Find First Occurrence:**
    - Use binary search to find the leftmost index where `nums[mid] == target`.
    - Continue searching on the left half to find an earlier occurrence.
2. **Find Last Occurrence:**
    - Use binary search to find the rightmost index where `nums[mid] == target`.
    - Continue searching on the right half to find a later occurrence.
3. Return `[firstIndex, lastIndex]` or `[-1, -1]` if not found.

### Java Code:

```java
public int[] searchRange(int[] nums, int target) {
    int[] result = {-1, -1};
    result[0] = findBound(nums, target, true);
    result[1] = findBound(nums, target, false);
    return result;
}

private int findBound(int[] nums, int target, boolean isFirst) {
    int low = 0, high = nums.length - 1;
    int bound = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) {
            bound = mid;
            if (isFirst) {
                high = mid - 1; // Search on the left for first occurrence
            } else {
                low = mid + 1;  // Search on the right for last occurrence
            }
        } else if (nums[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return bound;
}

```

### Complexity Analysis:

- **Time Complexity:** **O(log n)**, because binary search is performed twice.
- **Space Complexity:** **O(1)**, only constant extra space.

### Dry Run:

**Input:** `nums = [5,7,7,8,8,10], target = 8`

- **Find First Occurrence:**
    - low=0, high=5
    - mid=2 -> nums[2]=7 <8, low=3
    - mid=4 -> nums[4]=8 == target, record index=4, high=3 (search left)
    - mid=3 -> nums[3]=8== target, record index=3, high=2 (search left)
    - Loop ends, first occurrence = 3
- **Find Last Occurrence:**
    - low=0, high=5
    - mid=2 -> nums[2]=7<8, low=3
    - mid=4 -> nums[4]=8== target, record index=4, low=5 (search right)
    - mid=5 -> nums[5]=10>8, high=4
    - Loop ends, last occurrence=4

**Result:** `[3, 4]`

---

## 3. Most Optimal Solution: Binary Search with `O(log n)` Time Complexity

The above approach is the most efficient for this problem, exploiting the sorted array to find the first and last positions in logarithmic time.

---

## **Summary Table**

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | Linear scan for all occurrences | O(n) | O(1) | Straightforward but inefficient for large arrays |
| Binary Search for bounds | Use binary search twice to find first and last occurrence | O(log n) | O(1) | Most efficient; leverages sorted array |

---

## **Final Tips for Interviews:**

- Always consider the properties of the data (sorted/unsorted).
- Binary search is key for sorted arrays to achieve optimal time complexity.
- Carefully implement boundary conditions to avoid off-by-one errors.
- Test with edge cases:
    - Target not in array.
    - Array with all elements same.
    - Array with only one element.

---

This comprehensive guide should help you understand the problem deeply and prepare for related questions efficiently!
