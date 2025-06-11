# Search in a Sorted Infinite Array

This problem involves searching for a target element in an **infinite sorted array** where the size is unknown. Since the array is conceptually infinite, traditional binary search cannot be directly applied with fixed bounds. The key is to find the appropriate search boundaries first, then perform binary search.

---

## Approach 1: Brute Force (Linear Search)

### Core Idea:

Since the array is infinite and sorted, **a naive approach** is to start from the first element and move sequentially until the target is found or the element exceeds the target.

### Algorithm:

1. Start from index `i = 0`.
2. Increment `i` by 1 in each step.
3. Compare `arr[i]` with target:
    - If `arr[i] == target`, return `i`.
    - If `arr[i] > target`, stop; target doesn't exist.
4. Continue until the target is found or surpasses the target value.

### Java Code:

```java
public int searchInfiniteLinear(int[] arr, int target) {
    int i = 0;
    while (true) {
        if (arr[i] == target) {
            return i;
        } else if (arr[i] > target) {
            return -1; // target not found
        }
        i++;
    }
}

```

### Complexity Analysis:

- **Time Complexity:** O(N), where N is the index of the target (or where array exceeds target). In the worst case, it could be very large.
- **Space Complexity:** O(1).

---

## Approach 2: Bound Expansion + Binary Search

### Core Idea:

Since the array is sorted but of unknown size, **expand the search boundary exponentially** to find an interval that contains the target, then perform binary search within this interval.

### Algorithm:

1. Initialize `low = 0` and `high = 1`.
2. While `arr[high] < target`:
    - Update `low = high`.
    - Double `high` (`high = high * 2`) to exponentially increase the search boundary.
3. Perform binary search between `low` and `high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `arr[mid] == target`, return `mid`.
    - If `arr[mid] < target`, set `low = mid + 1`.
    - Else, set `high = mid - 1`.
4. If target not found, return -1.

### Java Code:

```java
public int searchInfiniteBinarySearch(int[] arr, int target) {
    int low = 0;
    int high = 1;

    // Expand the boundary exponentially
    while (arr[high] < target) {
        low = high;
        high = high * 2;
    }

    // Binary Search within the found bounds
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] == target) {
            return mid;
        } else if (arr[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }

    return -1; // target not found
}

```

### Complexity Analysis:

- **Time Complexity:** O(log N), where `N` is the position of the target, because boundary expansion takes O(log N) steps and binary search also takes O(log N).
- **Space Complexity:** O(1).

---

## Approach 3: Optimized Solution (Most Efficient)

The **second approach with boundary doubling** is considered the most optimal for this problem because it efficiently finds the appropriate bounds in logarithmic time and then performs binary search.

### Summary:

- Use exponential boundary expansion to find a range containing the target.
- Perform binary search within that range.
- This approach leverages the sorted property and exponential growth to avoid linear scanning.

---

## Dry Run Example

### Input:

```
arr = [1, 3, 5, 7, 9, 11, 13, 15, 17, 19, 21]
target = 13

```

### Step-by-step:

| Step | low | high | arr[high] | Action | Description |
| --- | --- | --- | --- | --- | --- |
| 1 | 0 | 1 | 3 | 3 < 13 | Expand boundary |
| 2 | 1 | 2 | 5 | 5 < 13 | Expand boundary |
| 3 | 2 | 4 | 9 | 9 < 13 | Expand boundary |
| 4 | 4 | 8 | 17 | 17 > 13 | Binary search between 4 and 8 |
| Binary Search step 1: mid=6, arr[6]=13 |  |  | 13 | Found target at index 6 |  |

### Final answer:

- **Target 13 found at index 6.**

---

## Summary

| Aspect | Details |
| --- | --- |
| **Naive Approach** | Linear search, simple but inefficient for large arrays. |
| **Efficient Approach** | Exponential boundary expansion + binary search, optimal for infinite sorted arrays. |
| **Time Complexity** | O(log N) for the exponential + binary search approach. |
| **Space Complexity** | O(1). |

---

## Key Takeaways:

- Infinite array means size is unknown; boundary must be dynamically found.
- Exponential boundary expansion helps locate a suitable search range efficiently.
- Binary search is then applied within this range for optimal performance.
- This approach ensures logarithmic time complexity, suitable for large or conceptually infinite arrays.

---

Feel free to practice with different inputs and understand the boundary expansion concept deeply!
