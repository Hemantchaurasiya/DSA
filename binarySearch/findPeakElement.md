# Find Peak Element

---

## Problem Overview

Given an array `nums`, find a **peak element** and return its index. A **peak element** is an element that is **greater than or equal to** its neighbors. For corner elements, only one neighbor needs to be considered.

---

## Approach 1: Brute Force

### Core Idea:

Check each element and determine if it is a peak by comparing it with its neighbors.

### Algorithm:

1. Iterate through every element in the array.
2. For each element:
    - If it's the first element, check if it's greater than or equal to its right neighbor.
    - If it's the last element, check if it's greater than or equal to its left neighbor.
    - For other elements, check if it's greater than or equal to both neighbors.
3. Return the index of the first element satisfying the peak condition.

### Java Code:

```java
public int findPeakElement(int[] nums) {
    int n = nums.length;
    for (int i = 0; i < n; i++) {
        boolean isPeak = true;
        if (i > 0 && nums[i] < nums[i - 1]) {
            isPeak = false;
        }
        if (i < n - 1 && nums[i] < nums[i + 1]) {
            isPeak = false;
        }
        if (isPeak) {
            return i;
        }
    }
    return -1; // theoretically never reached if array length > 0
}

```

### Complexity Analysis:

- **Time Complexity:** `O(n)` — Each element is checked once.
- **Space Complexity:** `O(1)` — Constant extra space.

### Dry Run:

**Input:** `[1, 3, 20, 4, 1]`

| Index | Element | Left Neighbor | Right Neighbor | Is Peak? | Action |
| --- | --- | --- | --- | --- | --- |
| 0 | 1 | - | 3 | No | 1 < 3, not a peak |
| 1 | 3 | 1 | 20 | No | 3 < 20, not a peak |
| 2 | 20 | 3 | 4 | Yes | 20 ≥ 3 and 20 ≥ 4, return index 2 |

---

## Approach 2: Better Solution (Linear Scan with Early Exit)

*(Same as the brute-force but emphasizing early exit when peak is found)*

### Core Idea:

Identify the first peak efficiently during iteration and stop immediately.

### Algorithm:

Same as above, but emphasizing **early exit** upon finding a peak.

*(Implementation is identical to Approach 1)*

---

## Approach 3: **Optimized Solution – Binary Search**

### Core Idea:

Use **binary search** to find a peak element in `O(log n)` time by exploiting the properties of peaks:

- If `nums[mid]` is less than `nums[mid + 1]`, then a peak must exist on the right side.
- If `nums[mid]` is greater than or equal to `nums[mid + 1]`, then a peak is on the left side or at `mid`.

This approach leverages the fact that:

- Moving towards the higher neighbor guarantees approaching a peak.

### Algorithm:

1. Initialize `low = 0`, `high = n - 1`.
2. While `low < high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `nums[mid] < nums[mid + 1]`, move `low` to `mid + 1` (search right).
    - Else, move `high` to `mid` (search left).
3. When `low` meets `high`, it points to a peak element.

### Java Code:

```java
public int findPeakElement(int[] nums) {
    int low = 0, high = nums.length - 1;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] < nums[mid + 1]) {
            low = mid + 1; // move right
        } else {
            high = mid; // move left
        }
    }
    return low; // or high, both point to peak
}

```

### Complexity Analysis:

- **Time Complexity:** `O(log n)` — Binary search halves the search space each step.
- **Space Complexity:** `O(1)` — No extra space used.

### Dry Run:

**Input:** `[1, 3, 20, 4, 1]`

| Step | low | high | mid | nums[mid] | nums[mid + 1] | Action | New low/high |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 4 | 2 | 20 | 4 | 20 ≥ 4, peak on left or at mid | high = mid = 2 |
| 2 | 0 | 2 | 1 | 3 | 20 | 3 < 20, move right | low = mid + 1 = 2 |
| End | 2 | 2 | - | 20 | - | low == high, peak at index 2 |  |

---

## Summary:

| Approach | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- |
| Brute Force | `O(n)` | `O(1)` | Checks each element; simplest but slow for large arrays |
| Linear Scan with Early Exit | `O(n)` | `O(1)` | Slightly optimized, stops at first peak |
| Binary Search (Optimized) | `O(log n)` | `O(1)` | Most efficient; uses property of peaks to halve search space |

---

## Final Tips:

- For large datasets, prefer the binary search approach.
- Always check boundary conditions (first and last elements).
- When implementing binary search, carefully handle mid calculations to avoid overflow.

---

**Happy Coding!**
