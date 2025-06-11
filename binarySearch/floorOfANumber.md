# Floor of a Number

---

## Problem Overview

Given a sorted array (ascending order) and a target value, **find the floor** of the target.

**Floor of a number** is the greatest element in the array less than or equal to the target.

---

## Approach 1: Brute Force

### Core Idea

Iterate through the entire array to identify the largest element less than or equal to the target.

### Algorithm

1. Initialize a variable `floor` to -1 (or some sentinel value indicating no valid floor found).
2. Traverse each element in the array:
    - If the current element is less than or equal to the target, update `floor` to this element.
3. After traversal, `floor` holds the required result.

### Java Code

```java
public int findFloorBruteForce(int[] arr, int target) {
    int floor = -1;
    for (int num : arr) {
        if (num <= target) {
            floor = num;
        }
    }
    return floor;
}

```

### Complexity Analysis

- **Time Complexity:** **O(n)**
Because we traverse the entire array once.
- **Space Complexity:** **O(1)**
No extra space used apart from variables.

### Dry Run

| Array | Target | Step-by-step | Final `floor` |
| --- | --- | --- | --- |
| `[2, 4, 6, 8, 10]` | `5` | Check 2 (<=5), `floor=2`; Check 4 (<=5), `floor=4`; Check 6 (>5), ignore; Check 8, ignore; Check 10, ignore | `4` |

---

## Approach 2: Binary Search (Optimized Solution)

### Core Idea

Since the array is sorted, binary search can efficiently find the floor in **O(log n)** time.

### Algorithm

1. Initialize `low = 0`, `high = arr.length - 1`, `result = -1`.
2. While `low <= high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `arr[mid]` equals the target, return `arr[mid]` (exact match).
    - If `arr[mid]` is less than the target:
        - Update `result = arr[mid]` because it's a candidate for the floor.
        - Move `low` to `mid + 1` to search for a potentially larger value.
    - Else, move `high` to `mid - 1` to look for smaller values.
3. Return `result` after the loop ends.

### Java Code

```java
public int findFloorBinarySearch(int[] arr, int target) {
    int low = 0, high = arr.length - 1;
    int result = -1;

    while (low <= high) {
        int mid = low + (high - low) / 2;

        if (arr[mid] == target) {
            return arr[mid]; // Exact match
        } else if (arr[mid] < target) {
            result = arr[mid]; // Candidate for floor
            low = mid + 1; // Search right side for a larger value <= target
        } else {
            high = mid - 1; // Search left side
        }
    }
    return result;
}

```

### Complexity Analysis

- **Time Complexity:** **O(log n)**
Because of binary search, halving the search space each iteration.
- **Space Complexity:** **O(1)**
No extra space apart from variables.

### Dry Run

| Array | Target | Initial low=0, high=4, result=-1 | Step-by-step | Final `result` |
| --- | --- | --- | --- | --- |
| `[2, 4, 6, 8, 10]` | `5` | `low=0`, `high=4` | 1. `mid=2`, `arr[mid]=6` (>5), move `high=1` |  |
|  |  |  | 2. `mid=0`, `arr[mid]=2` (<5), `result=2`, move `low=1` | 2 |
|  |  |  | 3. `mid=1`, `arr[mid]=4` (<5), `result=4`, move `low=2` | 4 |
|  |  |  | Loop ends (`low=2`, `high=1`) | Final `result=4` |

---

## Summary

| Approach | Time Complexity | Space Complexity | Suitable When |
| --- | --- | --- | --- |
| Brute Force | O(n) | O(1) | Small datasets or initial checks |
| Binary Search (Optimized) | O(log n) | O(1) | Large datasets, sorted arrays |

---

## Final Tips

- Use binary search when the array is sorted for efficiency.
- Always handle edge cases like:
    - Array is empty.
    - Target less than the smallest element.
    - Target greater than the largest element.

This comprehensive guide covers multiple approaches to finding the floor of a number in a sorted array, with clear explanations, code, and dry runs for quick revision and interview prep.
