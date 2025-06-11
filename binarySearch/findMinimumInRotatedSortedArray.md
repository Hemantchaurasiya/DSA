# Find Minimum in Rotated Sorted Array

---

## Problem Statement

Given a **rotated sorted array** `nums` (initially sorted in ascending order and then rotated at an unknown pivot), find the **minimum element** in the array.

---

## Approach 1: Brute Force

### Core Idea

Traverse the entire array to find the smallest element.

### Algorithm

1. Initialize a variable `minElement` with `nums[0]`.
2. Loop through each element in the array.
3. Update `minElement` if a smaller element is found.
4. Return `minElement`.

### Java Code

```java
public int findMinBruteForce(int[] nums) {
    int minElement = nums[0];
    for (int num : nums) {
        if (num < minElement) {
            minElement = num;
        }
    }
    return minElement;
}

```

### Complexity Analysis

- **Time Complexity:** `O(n)` because all elements are checked once.
- **Space Complexity:** `O(1)` as only a few variables are used.

### Dry Run

- Input: `[4, 5, 6, 7, 0, 1, 2]`
- Traverse:
    - minElement = 4 initially
    - Check 5 → minElement = 4
    - Check 6 → minElement = 4
    - Check 7 → minElement = 4
    - Check 0 → minElement = 0 (updated)
    - Check 1 → minElement = 0
    - Check 2 → minElement = 0
- Output: `0`

---

## Approach 2: Better Solution (Linear Search with Early Exit)

### Core Idea

Since the array is rotated, the minimum element is the only element where the previous element is greater than it. But in the brute force, we already find the minimum efficiently. This approach is similar but emphasizes early detection if possible.

*Note:* Since the array is rotated, the minimum element is at the point where the rotation occurs, which can sometimes be detected during traversal.

### Algorithm

1. Iterate through the array.
2. If an element is less than its previous element, that element is the minimum.
3. If not found, the first element is the minimum (array not rotated).

### Java Code

```java
public int findMinLinearScan(int[] nums) {
    for (int i = 1; i < nums.length; i++) {
        if (nums[i] < nums[i - 1]) {
            return nums[i];
        }
    }
    return nums[0]; // Array not rotated
}

```

### Complexity Analysis

- **Time Complexity:** `O(n)` in the worst case.
- **Space Complexity:** `O(1)`

### Dry Run

- Input: `[4, 5, 6, 7, 0, 1, 2]`
- Iteration:
    - i=1: 5 ≥ 4 → continue
    - i=2: 6 ≥ 5 → continue
    - i=3: 7 ≥ 6 → continue
    - i=4: 0 < 7 → return 0
- Output: `0`

---

## Approach 3: **Optimal Solution: Binary Search**

### Core Idea

Utilize the properties of the rotated sorted array and apply binary search to find the minimum element efficiently.

**Key observations:**

- The array is sorted but rotated, so:
    - One part is sorted in ascending order.
    - The minimum element is the point where the order breaks.
- If `nums[mid]` is greater than `nums[end]`, the minimum is in the right half.
- If `nums[mid]` is less than `nums[end]`, the minimum is in the left half.

### Algorithm

1. Initialize two pointers: `start = 0` and `end = nums.length - 1`.
2. While `start < end`:
    - Find `mid = start + (end - start) / 2`.
    - If `nums[mid] > nums[end]`, the minimum is in the right half, so set `start = mid + 1`.
    - Else, the minimum is in the left half including mid, so set `end = mid`.
3. When `start == end`, `nums[start]` is the minimum.

### Java Code

```java
public int findMinBinarySearch(int[] nums) {
    int start = 0, end = nums.length - 1;
    while (start < end) {
        int mid = start + (end - start) / 2;
        if (nums[mid] > nums[end]) {
            start = mid + 1;
        } else {
            end = mid;
        }
    }
    return nums[start];
}

```

### Complexity Analysis

- **Time Complexity:** `O(log n)` because binary search halves the search space each iteration.
- **Space Complexity:** `O(1)` as variables are used only for pointers.

### Dry Run

- Input: `[4, 5, 6, 7, 0, 1, 2]`
| Step | start | end | mid | nums[mid] | nums[end] | Action | Resulting start/end |
|-------|--------|-----|-----|------------|-----------|---------|---------------------|
| 1 | 0 | 6 | 3 | 7 | 2 | nums[mid] > nums[end], so min in right | start = mid + 1 = 4 |
| 2 | 4 | 6 | 5 | 1 | 2 | nums[mid] < nums[end], min in left (including mid) | end = mid = 5 |
| 3 | 4 | 5 | 4 | 0 | 1 | nums[mid] < nums[end], end = mid = 4 |
| Loop ends: start=4, end=4 | | | | | | | Return nums[start] = 0 |

---

## Summary

| Approach | Time Complexity | Space Complexity | Remarks |
| --- | --- | --- | --- |
| Brute Force | `O(n)` | `O(1)` | Simple, straightforward |
| Linear Search with Early Exit | `O(n)` | `O(1)` | Slightly optimized, detects break point early |
| Binary Search (Optimal) | `O(log n)` | `O(1)` | Most efficient, exploits array properties |

---

## Final Tip

- Always consider the properties of the rotated sorted array.
- Binary search is the go-to approach for optimal solutions in such problems.

---

**Happy Coding!**
