# Ceiling of a Number

---

## Problem Statement

Given a sorted array (ascending order) and a target number, find the **ceiling** of the target in the array.

**Ceiling**: The smallest element in the array which is **greater than or equal to** the target.

---

## Approach 1: Brute Force

### Core Idea

Scan through the entire array to find the minimum element that is greater than or equal to the target.

### Algorithm

1. Initialize a variable, say `res`, to store the ceiling value. Set it to some default (e.g., `1` or `Integer.MAX_VALUE`).
2. Traverse each element in the array:
    - If the current element is **greater than or equal to** the target:
        - Update `res` if the current element is smaller than the current `res`.
3. After traversal, `res` will contain the ceiling value or indicate no ceiling exists if it remains default.

### Java Code

```java
public static int findCeilingBruteForce(int[] arr, int target) {
    int res = -1;
    for (int num : arr) {
        if (num >= target) {
            if (res == -1 || num < res) {
                res = num;
            }
        }
    }
    return res;
}

```

### Complexity Analysis

- **Time Complexity:** O(n), where n is the size of the array, since we scan all elements.
- **Space Complexity:** O(1), no extra space used apart from variables.

### Dry Run

- **Input:** `arr = [1, 3, 5, 6, 8], target = 4`
- Step-by-step:
    - Initialize `res = -1`
    - Loop:
        - `1` < 4 → ignore
        - `3` < 4 → ignore
        - `5` ≥ 4 → `res = 5`
        - `6` ≥ 4 → check if `6 < res (5)`? No → ignore
        - `8` ≥ 4 → check if `8 < res (5)`? No → ignore
    - Final `res = 5`

---

## Approach 2: Binary Search (Optimized Solution)

### Core Idea

Since the array is sorted, we can use **binary search** to find the ceiling efficiently:

- If the target exists, the ceiling is the target itself.
- If not, the smallest element greater than the target will be the ceiling.

### Algorithm

1. Initialize `low = 0`, `high = n - 1`.
2. While `low <= high`:
    - Compute `mid = low + (high - low) / 2`.
    - If `arr[mid] == target`, return `arr[mid]` (ceiling found).
    - If `arr[mid] < target`, move `low` to `mid + 1`.
    - Else (`arr[mid] > target`), update `res` to `arr[mid]` and move `high` to `mid - 1`.
3. If no exact match, `res` will hold the smallest element greater than the target (if exists). Return `res`, or `1` if none.

### Java Code

```java
public static int findCeilingBinarySearch(int[] arr, int target) {
    int low = 0, high = arr.length - 1;
    int res = -1;

    while (low <= high) {
        int mid = low + (high - low) / 2;

        if (arr[mid] == target) {
            return arr[mid]; // exact match
        } else if (arr[mid] < target) {
            low = mid + 1;
        } else {
            res = arr[mid]; // potential ceiling
            high = mid - 1;
        }
    }
    return res;
}

```

### Complexity Analysis

- **Time Complexity:** O(log n), since binary search halves the search space each iteration.
- **Space Complexity:** O(1), only constant extra space.

### Dry Run

- **Input:** `arr = [1, 3, 5, 6, 8], target = 4`
- Step-by-step:
    - `low = 0`, `high = 4`, `res = -1`
    - Iteration 1:
        - `mid = 2` -> `arr[mid] = 5`
        - `5` > `4` → `res = 5`, move `high = 1`
    - Iteration 2:
        - `low = 0`, `high = 1`
        - `mid = 0` -> `arr[mid] = 1`
        - `1` < `4` → `low = 1`
    - Iteration 3:
        - `low = 1`, `high = 1`
        - `mid = 1` -> `arr[mid] = 3`
        - `3` < `4` → `low = 2`
    - Loop ends (`low = 2`, `high = 1`)
    - Final `res = 5`

---

## Summary

| Approach | Idea | Time Complexity | Space Complexity | When to Use |
| --- | --- | --- | --- | --- |
| Brute Force | Linear scan for minimum ≥ target | O(n) | O(1) | Small arrays or quick implementation needed |
| Binary Search (Optimal) | Use sorted property for efficient search | O(log n) | O(1) | Large, sorted arrays |

---

## Final Tips

- Always check if the array is sorted before applying binary search.
- Handle edge cases:
    - Target greater than all elements (ceiling = -1 or indication no ceiling).
    - Target less than or equal to the smallest element.
- Binary search reduces time complexity significantly, making it the preferred approach for large datasets.

---

**Happy Coding!**
