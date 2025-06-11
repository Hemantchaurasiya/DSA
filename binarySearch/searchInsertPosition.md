# Search Insert Position

---

## Problem Statement

Given a sorted array of distinct integers `nums` and a target value `target`, return the index if the target is found. If not, return the index where it would be inserted in the sorted array.

---

## Approach 1: Brute Force

### Core Idea

Iterate through the array linearly to find the correct position of the target or determine where it should be inserted.

### Algorithm

1. Loop through each element in the array.
2. If the current element is equal to `target`, return its index.
3. If the current element is greater than `target`, return its index since `target` should be inserted before this element.
4. If none of the above conditions are met, and the loop completes, the `target` should be inserted at the end of the array.

### Java Code

```java
public int searchInsert(int[] nums, int target) {
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] >= target) {
            return i;
        }
    }
    return nums.length;
}

```

### Complexity Analysis

- **Time Complexity:O(n)** — The algorithm scans through the array once in the worst case.
- **Space Complexity:O(1)** — Uses constant additional space.

### Dry Run

- **Input:** `nums = [1, 3, 5, 6]`, `target = 5`

| i | nums[i] | Condition `nums[i] >= target` | Action |
| --- | --- | --- | --- |
| 0 | 1 | 1 >= 5? No | continue |
| 1 | 3 | 3 >= 5? No | continue |
| 2 | 5 | 5 >= 5? Yes | return 2 |

**Output:** `2`

---

## Approach 2: Better Solution — Binary Search

### Core Idea

Since the array is sorted, we can apply binary search to efficiently locate the position for `target`. If `target` exists, return its index; otherwise, return the position where it could be inserted.

### Algorithm

1. Initialize `low = 0` and `high = nums.length - 1`.
2. While `low <= high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `nums[mid] == target`, return `mid`.
    - If `nums[mid] < target`, move `low` to `mid + 1`.
    - Else, move `high` to `mid - 1`.
3. If the loop ends, `low` will be at the position where `target` should be inserted.

### Java Code

```java
public int searchInsert(int[] nums, int target) {
    int low = 0, high = nums.length - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return low;
}

```

### Complexity Analysis

- **Time Complexity:O(log n)** — The binary search halves the search space with each iteration.
- **Space Complexity:O(1)** — Uses constant space.

### Dry Run

- **Input:** `nums = [1, 3, 5, 6]`, `target = 5`

| Step | low | high | mid | nums[mid] | Condition | Action |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 3 | 1 | 3 | 3 < 5? Yes | low = mid + 1 = 2 |
| 2 | 2 | 3 | 2 | 5 | nums[mid] == target? Yes | return 2 |

**Output:** `2`

---

## Approach 3: Optimal Solution — Binary Search with Precise Boundary Handling

This is essentially the same as the previous binary search but emphasizes that the `low` pointer will always indicate the correct insertion position after the loop ends if the target isn't found.

### Summary

- Use binary search to find the target or the appropriate insertion point efficiently.
- The key insight is that after the loop ends, `low` points to the correct insertion position.

---

# Final Notes

- Since the array is sorted and contains distinct elements, binary search provides an optimal solution.
- Always verify boundary conditions and ensure that the insertion point is correctly represented by `low` after the binary search loop.

---

**Happy Coding!**
