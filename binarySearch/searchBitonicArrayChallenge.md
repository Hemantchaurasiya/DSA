# Search Bitonic Array

---

## Problem Overview

Given a bitonic array (an array that first increases and then decreases), the goal is to find a target element efficiently.

---

## Approach 1: Brute Force

### Core Idea

Scan the entire array linearly to find the target element.

### Algorithm

1. Iterate through each element of the array.
2. Compare the current element with the target.
3. If a match is found, return the index.
4. If no match after traversing the entire array, return -1.

### Java Code

```java
public int searchBitonicArray(int[] arr, int target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            return i;
        }
    }
    return -1;
}

```

### Complexity Analysis

- **Time Complexity:** **O(n)**
Because we may need to scan all elements.
- **Space Complexity:** **O(1)**
No extra space required.

### Dry Run

- Input: `arr = [1, 3, 8, 12, 4, 2]`, `target = 4`
- Traverse array:
    - 1 (no)
    - 3 (no)
    - 8 (no)
    - **12 (no)**
    - **4 (yes)** → return index 4

---

## Approach 2: Find Peak & Binary Search

### Core Idea

Leverage the bitonic property:

- Find the **peak** (maximum element) using binary search.
- Perform binary search on the **ascending part**.
- If not found, perform binary search on the **descending part**.

### Algorithm

1. **Find Peak Element:**
    - Use binary search to find the index of the maximum element.
2. **Binary Search in Ascending Part:**
    - Search from start to peak.
3. **Binary Search in Descending Part:**
    - Search from peak+1 to end, considering the array is decreasing.

### Java Code

```java
public int searchBitonicArray(int[] arr, int target) {
    int peakIndex = findPeak(arr);
    int index = binarySearch(arr, target, 0, peakIndex, true);
    if (index != -1) {
        return index;
    }
    return binarySearch(arr, target, peakIndex + 1, arr.length - 1, false);
}

private int findPeak(int[] arr) {
    int low = 0, high = arr.length - 1;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] > arr[mid + 1]) {
            high = mid;
        } else {
            low = mid + 1;
        }
    }
    return low;
}

private int binarySearch(int[] arr, int target, int low, int high, boolean ascending) {
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (arr[mid] == target) {
            return mid;
        }
        if (ascending) {
            if (arr[mid] < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        } else {
            if (arr[mid] > target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
    }
    return -1;
}

```

### Complexity Analysis

- **Time Complexity:** **O(log n)**
    - Finding peak: `O(log n)`
    - Binary searches: each `O(log n)`
    - Total: `O(log n)`
- **Space Complexity:** **O(1)**
    - No extra space used.

### Dry Run

- Input: `arr = [1, 3, 8, 12, 4, 2]`, `target = 4`

| Step | Variables/Array Segment | Description |
| --- | --- | --- |
| Find Peak | low=0, high=5 | mid=2, arr[mid]=8, arr[mid+1]=12 → arr[mid]<arr[mid+1], so low=mid+1=3 |
|  | low=3, high=5 | mid=4, arr[mid]=4, arr[mid+1]=2 → arr[mid]>arr[mid+1], so high=mid=4 |
|  | low=3, high=4 | mid=3, arr[mid]=12, arr[mid+1]=4 → arr[mid]>arr[mid+1], high=mid=3 |
| Binary Search in Ascending (indices 0 to 3): | low=0, high=3 | mid=1, arr[mid]=3, target=4 → move right |
|  | low=2, high=3 | mid=2, arr[mid]=8, > 4 → move left |
| Binary Search in Descending (indices 4 to 5): | low=4, high=5 | mid=4, arr[mid]=4, target=4 → found! Return 4 |

---

## Approach 3: Most Optimal - Binary Search on Bitonic Array

### Core Idea

Combine the peak-finding and binary search in a single efficient process:

- **Step 1:** Find the peak using binary search.
- **Step 2:** Search for the element in both parts using binary search tailored for ascending and descending order.

### Algorithm

Same as Approach 2, but optimized into a unified process, minimizing overhead.

### Java Code

(Same as Approach 2, as it is already optimized)

---

## Final Notes:

- **Choosing the right approach** depends on problem constraints.
- For large arrays, binary search-based methods are preferred for their efficiency.
- Always remember to **find the peak** first, then do binary searches.

---

## Summary Table

| Approach | Time Complexity | Space Complexity | Suitable For |
| --- | --- | --- | --- |
| Brute Force | O(n) | O(1) | Small datasets or quick implementation |
| Peak + Binary Search | O(log n) | O(1) | Large datasets, optimal solution |

---

**Happy coding!**
