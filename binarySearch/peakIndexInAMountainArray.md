# Peak Index in a Mountain Array

---

## Problem Overview

Given a **mountain array** (an array where elements initially increase then decrease), find the **peak index** — the index of the maximum element.

**Example:**

```
Input: arr = [0, 2, 4, 3, 1]
Output: 2

```

---

## Approach 1: Brute Force

### Core Idea

Scan the entire array to find the maximum element's index.

### Algorithm

1. Initialize `maxIndex = 0`.
2. Loop through the array from `0` to `n-1`.
3. Track the index of the maximum value encountered.
4. Return the index of the maximum element after the loop.

### Java Code

```java
public int peakIndexInMountainArray(int[] arr) {
    int maxIndex = 0;
    for (int i = 1; i < arr.length; i++) {
        if (arr[i] > arr[maxIndex]) {
            maxIndex = i;
        }
    }
    return maxIndex;
}

```

### Complexity Analysis

- **Time Complexity:** **O(n)** — traverses the entire array once.
- **Space Complexity:** **O(1)** — only constant extra space.

### Dry Run

| Step | arr | i | maxIndex | arr[maxIndex] | Comment |
| --- | --- | --- | --- | --- | --- |
| 1 | [0, 2, 4, 3, 1] | 1 | 0 | 0 | arr[1]=2 > arr[0]=0; maxIndex=1 |
| 2 | [0, 2, 4, 3, 1] | 2 | 1 | 2 | arr[2]=4 > arr[1]=2; maxIndex=2 |
| 3 | [0, 2, 4, 3, 1] | 3 | 2 | 4 | arr[3]=3 < arr[2]=4; no change |
| 4 | [0, 2, 4, 3, 1] | 4 | 2 | 4 | arr[4]=1 < arr[2]=4; no change |
| End | - | - | 2 | 4 | Result: index 2 |

---

## Approach 2: Binary Search (Optimized Solution)

### Core Idea

Leverage the **mountain property**: for a peak index `i`, the array increases before `i` and decreases after `i`. Use binary search to find the peak efficiently:

- If `arr[mid] < arr[mid + 1]`, the peak is to the right.
- Else, the peak is to the left or at `mid`.

### Algorithm

1. Initialize `low = 0` and `high = arr.length - 1`.
2. While `low < high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `arr[mid] < arr[mid + 1]`, move `low` to `mid + 1`.
    - Else, move `high` to `mid`.
3. When `low == high`, it points to the peak index.
4. Return `low`.

### Java Code

```java
public int peakIndexInMountainArray(int[] arr) {
    int low = 0, high = arr.length - 1;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] < arr[mid + 1]) {
            // Peak is to the right
            low = mid + 1;
        } else {
            // Peak is at mid or to the left
            high = mid;
        }
    }
    return low;
}

```

### Complexity Analysis

- **Time Complexity:** **O(log n)** — binary search halves the search space each iteration.
- **Space Complexity:** **O(1)** — constant extra space.

### Dry Run

Using `arr = [0, 2, 4, 3, 1]`:

| Step | low | high | mid | arr[mid] | arr[mid+1] | Action | Result |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 4 | 2 | 4 | 3 | Since arr[2]=4 > arr[3]=3, move high to mid: high=2 | low=0, high=2 |
| 2 | 0 | 2 | 1 | 2 | 4 | arr[1]=2 < arr[2]=4, move low to mid+1: low=2 | low=2, high=2 (loop ends) |
| End | - | - | - | - | - | Loop exits, low=2 is peak index | Result: 2 |

---

## Summary

| Approach | Time Complexity | Space Complexity | When to Use |
| --- | --- | --- | --- |
| Brute Force | O(n) | O(1) | Small arrays or initial implementation |
| Binary Search | O(log n) | O(1) | Large arrays, performance-critical scenarios |

---

## Final Tips

- Recognize the **mountain array** property: strictly increasing then decreasing.
- Use binary search to achieve optimal time complexity.
- Always verify boundary conditions, especially for `mid` and `mid+1`.

---

This structured revision should help you understand the problem deeply, master the solution approaches, and prepare effectively for interviews.
