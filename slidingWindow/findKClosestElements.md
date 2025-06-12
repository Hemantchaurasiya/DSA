# Find K Closest Elements

---

## Problem Overview
Given a sorted integer array `arr`, two integers `x` and `k`, find the `k` closest elements to `x` in the array. The result should be sorted in ascending order. If there is a tie (i.e., two elements equally close to `x`), the smaller element should be preferred.

---

## Approach 1: Brute Force (Sort and Select)

### **Core Idea**
- Calculate the absolute difference of each element with `x`.
- Sort the array based on this difference.
- Select the first `k` elements.
- Sort these `k` elements in ascending order (as the final output should be sorted).

### **Algorithm**
1. Create a list of pairs `(element, difference)` for each element in `arr`.
2. Sort the list based on the difference; if differences are equal, compare actual elements.
3. Extract the first `k` elements from the sorted list.
4. Sort these `k` elements in ascending order.
5. Return the resulting list.

### **Java Code**
```java
import java.util.*;

public List<Integer> findClosestElements(int[] arr, int k, int x) {
    List<int[]> diffList = new ArrayList<>();
    for (int num : arr) {
        diffList.add(new int[]{num, Math.abs(num - x)});
    }
    diffList.sort((a, b) -> {
        if (a[1] == b[1]) return Integer.compare(a[0], b[0]);
        return Integer.compare(a[1], b[1]);
    });
    List<Integer> result = new ArrayList<>();
    for (int i = 0; i < k; i++) {
        result.add(diffList.get(i)[0]);
    }
    Collections.sort(result);
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n log n)** due to sorting the entire array based on differences.
- **Space Complexity:** **O(n)** for storing pairs.

### **Dry Run**
- Input: `arr = [1, 2, 3, 4, 5], k=4, x=3`
- Step-by-step:
  - Differences: `(1,2)`, `(2,1)`, `(3,0)`, `(4,1)`, `(5,2)`
  - Sorted by difference: `(3,0)`, `(2,1)`, `(4,1)`, `(1,2)`, `(5,2)`
  - First `k=4` elements: `[3, 2, 4, 1]`
  - Sorted result: `[1, 2, 3, 4]`

---

## Approach 2: Binary Search + Sliding Window (Most Efficient)

### **Core Idea**
- Use binary search to find the starting index of the window of size `k` that contains the closest elements to `x`.
- This approach leverages the sorted nature of the array to efficiently narrow down the window.

### **Algorithm**
1. Set `low = 0`, `high = arr.length - k`.
2. While `low < high`:
   - Calculate `mid = (low + high) / 2`.
   - Compare distances:
     - If `x - arr[mid] > arr[mid + k] - x`, move `low` to `mid + 1`.
     - Else, move `high` to `mid`.
3. The starting index of the window is `low`.
4. Return the subarray from `low` to `low + k - 1`.

### **Java Code**
```java
import java.util.*;

public List<Integer> findClosestElements(int[] arr, int k, int x) {
    int low = 0;
    int high = arr.length - k;
    while (low < high) {
        int mid = low + (high - low) / 2;
        if (x - arr[mid] > arr[mid + k] - x) {
            low = mid + 1;
        } else {
            high = mid;
        }
    }
    List<Integer> result = new ArrayList<>();
    for (int i = low; i < low + k; i++) {
        result.add(arr[i]);
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(log(n - k) + k)**, dominated by binary search, with the final subarray extraction.
- **Space Complexity:** **O(k)** for storing the result.

### **Dry Run**
- Input: `arr = [1, 2, 3, 4, 5], k=4, x=3`
- Step-by-step:
  - `low=0`, `high=1` (since `len=5`, `k=4`)
  - `mid=0`
  - Compare:
    - `x - arr[mid] = 3 - 1 = 2`
    - `arr[mid + k] - x = arr[4] - 3 = 5 - 3 = 2`
  - Since both are equal, move `high` to `mid=0`.
  - Resulting window starts at index 0: `[1, 2, 3, 4]`.

---

## Summary

| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Sorting by difference | **O(n log n)** | **O(n)** | Straightforward, easier to implement for small constraints. |
| 2 | Binary Search + Sliding Window | **O(log(n - k)) + O(k)** | **O(k)** | Most efficient, leverages sorted property effectively. |

---

## Final Tips for Interviews
- For sorted arrays, always consider binary search for optimal solutions.
- Understand the concept of sliding windows to reduce time complexity.
- Practice edge cases:
  - `k` equals array length.
  - `x` smaller than all elements.
  - `x` larger than all elements.
- Be ready to explain your approach and optimize based on constraints.

---

**Happy Coding!**
