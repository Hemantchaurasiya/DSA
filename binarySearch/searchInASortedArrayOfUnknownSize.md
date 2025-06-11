# Search in a Sorted Array of Unknown Size

This problem involves searching a target value in a sorted array where the size of the array is unknown. The main challenge is to efficiently locate the element without prior knowledge of the array's length.

---

## 1. Brute Force Approach

### Core Idea:

Traverse the array sequentially until reaching the target or the end of the array.

### Algorithm:

- Start from index `0`.
- Check each element:
    - If it matches the target, return the index.
    - If you reach an index where the element exceeds the target (since array is sorted), stop.
- If the array is infinite or the end is unknown, keep moving forward until the element exceeds the target or you find the element.

### Java Code:

```java
public int searchInUnknownSizeArray(List<Integer> list, int target) {
    int index = 0;
    while (true) {
        if (index >= list.size() || list.get(index) > target) {
            break;
        }
        if (list.get(index) == target) {
            return index;
        }
        index++;
    }
    return -1; // Not found
}

```

### Complexity Analysis:

- **Time Complexity:** *O(n)* in the worst case, as we might traverse the entire array.
- **Space Complexity:** *O(1)*, as no extra space is used.

### Dry Run:

- Input: `list = [1, 3, 5, 7, 9, 11, 13]`, `target = 7`
- Traversal:
    - index=0, element=1 → continue
    - index=1, element=3 → continue
    - index=2, element=5 → continue
    - index=3, element=7 → found, return 3

---

## 2. Better Solution: Exponential Search + Binary Search

### Core Idea:

Since the array is sorted, but size is unknown, **exponentially** increase the range to find an upper bound where the target could be, then perform a binary search within that range.

### Algorithm:

1. **Find Range:**
    - Initialize `low = 0`, `high = 1`.
    - While `high` is within bounds and `array[high] < target`:
        - Double `high` (`high *= 2`).
        - Update `low = high / 2`.
2. **Binary Search:**
    - Perform binary search between `low` and `high` to find the target.

### Java Code:

```java
public int searchInUnknownSizeArray(List<Integer> list, int target) {
    int low = 0;
    int high = 1;

    // Find range where target could be
    while (high < list.size() && list.get(high) < target) {
        low = high + 1;
        high *= 2;
        if (high >= list.size()) {
            high = list.size() - 1; // Ensure high doesn't go beyond list
            break;
        }
    }

    // Binary Search within found range
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (mid >= list.size()) {
            high = mid - 1;
            continue;
        }
        if (list.get(mid) == target) {
            return mid;
        } else if (list.get(mid) < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return -1; // Not found
}

```

### Complexity Analysis:

- **Time Complexity:** *O(log n)* for range finding + *O(log n)* for binary search = **O(log n)**.
- **Space Complexity:** *O(1)*, as no extra space is used.

### Dry Run:

- Input: `list = [1, 3, 5, 7, 9, 11, 13]`, `target=7`
- Range Finding:
    - low=0, high=1, list[1]=3 < 7 → high=2
    - high=2, list[2]=5 < 7 → high=4
    - high=4, list[4]=9 ≥ 7 → stop, range= [2,4]
- Binary Search:
    - mid=3, list[3]=7 → found at index 3.

---

## 3. Most Optimal Solution: Exponential Search + Binary Search (Refined)

### Core Idea:

This approach combines exponential search to quickly find an upper bound with binary search for precise positioning. It is optimal for sorted arrays of unknown size.

### Algorithm Summary:

- Use exponential search to determine a search boundary.
- Perform binary search within the boundary.

### Java Code:

```java
public int searchInUnknownSizeArray(List<Integer> list, int target) {
    int index = 1;

    // Find the range
    while (index < list.size() && list.get(index) < target) {
        index *= 2;
    }

    int low = index / 2;
    int high = Math.min(index, list.size() - 1);

    // Binary search
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (list.get(mid) == target) {
            return mid;
        } else if (list.get(mid) < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }

    return -1;
}

```

### Complexity Analysis:

- **Time Complexity:** *O(log n)*, where `n` is the position of the target.
- **Space Complexity:** *O(1)*.

---

# Summary Table

| Approach | Key Idea | Time Complexity | Space Complexity | Suitable for |
| --- | --- | --- | --- | --- |
| Brute Force | Linear traversal | **O(n)** | **O(1)** | Small or unknown array size, no constraints |
| Exponential + Binary Search | Find range exponentially, then binary search | **O(log n)** | **O(1)** | Large or infinite arrays, performance critical |

---

# Final Tips:

- Always start with exponential search to determine a manageable search space.
- Combine with binary search for efficient lookup.
- Handle edge cases where the target might be beyond the current bounds or the array is very small.

---

This structured revision provides a clear understanding of how to approach and solve the problem efficiently, suitable for interviews and quick revision.
