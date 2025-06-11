# Rotation Count

This problem involves finding the number of times a sorted array has been rotated. The array is initially sorted in ascending order, and then rotated an unknown number of times. The goal is to determine the rotation count efficiently.

---

## 1. Brute Force Approach

### **Core Idea:**

Traverse the entire array to find the index where the order breaks (i.e., where `arr[i] > arr[i+1]`). The number of rotations is `i+1` (index of the minimum element).

### **Algorithm:**

- Loop through the array from `0` to `n-2`.
- For each index `i`, check if `arr[i] > arr[i+1]`.
- If yes, then `i+1` is the index of the smallest element, which is the rotation count.
- If no break point is found, the array is not rotated; rotation count = 0.

### **Java Code:**

```java
public int countRotationsBruteForce(int[] arr) {
    int n = arr.length;
    for (int i = 0; i < n - 1; i++) {
        if (arr[i] > arr[i + 1]) {
            return i + 1;
        }
    }
    return 0; // Array not rotated
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(n)` — In the worst case, we traverse the entire array once.
- **Space Complexity:** `O(1)` — No extra space used.

### **Dry Run:**

- Input: `[15, 18, 2, 3, 6, 12]`
- Iteration:
    - i=0: 15 > 18? No.
    - i=1: 18 > 2? Yes → Rotation count = 2 (index 2).
- Output: `2`

---

## 2. Better Solution: Find Minimum Element Index (Linear Scan)

### **Core Idea:**

The rotation count equals the index of the minimum element in the rotated sorted array.

### **Algorithm:**

- Traverse the array to find the minimum element's index.
- Return that index as the rotation count.

### **Java Code:**

```java
public int countRotationsLinearScan(int[] arr) {
    int minIndex = 0;
    for (int i = 1; i < arr.length; i++) {
        if (arr[i] < arr[minIndex]) {
            minIndex = i;
        }
    }
    return minIndex;
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(n)` — Linear scan to find minimum.
- **Space Complexity:** `O(1)`.

### **Dry Run:**

- Input: `[15, 18, 2, 3, 6, 12]`
- Iteration:
    - minIndex=0 (15)
    - i=1: 18 < 15? No.
    - i=2: 2 < 15? Yes → minIndex=2.
    - i=3: 3 < 2? No.
    - i=4: 6 < 2? No.
    - i=5: 12 < 2? No.
- Output: `2`.

---

## 3. Most Optimal Solution: Binary Search (O(log n))

### **Core Idea:**

Leverage the properties of a rotated sorted array:

- At least one half of the array is sorted.
- The smallest element (rotation point) is the only element that is smaller than its previous element.
- Use binary search to efficiently locate the minimum element.

---

### **Algorithm:**

1. Initialize `low=0`, `high=n-1`.
2. While `low <= high`:
    - If the subarray `arr[low..high]` is already sorted (`arr[low] <= arr[high]`), then `arr[low]` is the minimum, return `low`.
    - Find `mid = low + (high - low) / 2`.
    - Check if `mid` is the pivot point:
        - If `arr[mid] > arr[mid + 1]`, then `mid + 1` is the minimum.
        - If `arr[mid - 1] > arr[mid]`, then `mid` is the minimum.
    - Decide which half to search:
        - If `arr[mid] >= arr[low]`, the left part is sorted, so the minimum is in the right half (`mid+1` to `high`).
        - Else, minimum is in the left half (`low` to `mid-1`).
3. The index of the minimum element is the rotation count.

### **Java Code:**

```java
public int countRotationsBinarySearch(int[] arr) {
    int low = 0;
    int high = arr.length - 1;

    while (low <= high) {
        // If array is already sorted
        if (arr[low] <= arr[high]) {
            return low;
        }

        int mid = low + (high - low) / 2;

        // Check if mid is the pivot
        if (mid < high && arr[mid] > arr[mid + 1]) {
            return mid + 1;
        }
        if (mid > low && arr[mid - 1] > arr[mid]) {
            return mid;
        }

        // Decide which half to search
        if (arr[mid] >= arr[low]) {
            // Left part is sorted, search right
            low = mid + 1;
        } else {
            // Right part is sorted, search left
            high = mid - 1;
        }
    }
    return 0; // Not rotated
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(log n)` — Divides search space in half each iteration.
- **Space Complexity:** `O(1)`.

### **Dry Run:**

- Input: `[15, 18, 2, 3, 6, 12]`
- Initial: low=0, high=5
    - mid=2: arr[mid]=2
    - Check if arr[mid]>arr[mid+1]? 2>3? No.
    - Check if arr[mid-1]>arr[mid]? 18>2? Yes → pivot at index 2.
- Output: `2`.

---

# Summary of Approaches

| Approach | Idea | Time Complexity | Space Complexity | Suitable For |
| --- | --- | --- | --- | --- |
| Brute Force | Find break point in linear scan | `O(n)` | `O(1)` | Small arrays, quick implementation |
| Linear Scan Min Index | Find index of minimum element | `O(n)` | `O(1)` | Simpler, but less efficient for large arrays |
| Binary Search | Use properties of rotated sorted array | `O(log n)` | `O(1)` | Optimal for large arrays, interview-ready |

---

## Final Note:

- **Binary Search** approach is the most optimal and recommended for large input sizes.
- Always validate edge cases:
    - Array not rotated (`[1, 2, 3, 4, 5]`)
    - Array rotated `n` times (full rotation)
    - Single element array

---

**Happy Coding!**
