# Find K Closest Elements

---

## Problem Statement

Given a **sorted array** `arr`, a **target** value `x`, and an integer `k`, find the **k** closest elements to `x` in the array. The result should be sorted in ascending order. If there is a tie, the smaller element should be preferred.

---

## Approach 1: Brute Force

### **Core Idea**

Iterate through all elements, calculate their absolute difference from `x`, sort based on this difference, and pick the top `k`.

### **Algorithm**

1. For each element in the array, compute `abs(arr[i] - x)`.
2. Store each element along with its difference in a list of tuples/pairs.
3. Sort this list based on the difference.
4. Select the first `k` elements from the sorted list.
5. Sort these `k` elements before returning (since the output should be sorted).

### **Java Code**

```java
import java.util.*;

public List<Integer> findClosestElements(int[] arr, int k, int x) {
    List<int[]> diffList = new ArrayList<>();
    for (int num : arr) {
        diffList.add(new int[]{Math.abs(num - x), num});
    }
    // Sort based on difference, then by value for tie-breaking
    Collections.sort(diffList, (a, b) -> a[0] == b[0] ? Integer.compare(a[1], b[1]) : Integer.compare(a[0], b[0]));

    List<Integer> result = new ArrayList<>();
    for (int i = 0; i < k; i++) {
        result.add(diffList.get(i)[1]);
    }
    Collections.sort(result);
    return result;
}

```

### **Complexity Analysis**

- **Time Complexity:**
    - Computing differences: `O(n)`
    - Sorting the list: `O(n log n)`
    - Final sorting of `k` elements: `O(k log k)`
    - Overall: **O(n log n)**
- **Space Complexity:**
    - Additional storage for the list: `O(n)`

### **Dry Run**

- **Input:** `arr = [1, 2, 3, 4, 5]`, `k = 4`, `x = 3`
- **Process:**
    - Differences: `(abs(1-3)=2, 1), (abs(2-3)=1, 2), (abs(3-3)=0, 3), (abs(4-3)=1, 4), (abs(5-3)=2, 5)`
    - Sorted by difference: `(0,3), (1,2), (1,4), (2,1), (2,5)`
    - Pick first 4: `3, 2, 4, 1`
    - Final sorted result: `[1, 2, 3, 4]`

---

## Approach 2: Better Solution - Two Pointers (Sliding Window)

### **Core Idea**

Since the array is sorted, the closest `k` elements form a contiguous subarray. We can use two pointers to find this subarray efficiently.

### **Algorithm**

1. Initialize two pointers: `left = 0`, `right = arr.length - 1`.
2. While the window size is greater than `k`:
    - Compare `abs(arr[left] - x)` and `abs(arr[right] - x)`.
    - If `abs(arr[left] - x) > abs(arr[right] - x)`:
        - Increment `left` (discard the leftmost).
    - Else:
        - Decrement `right` (discard the rightmost).
3. The subarray from `left` to `right` will contain the `k` closest elements.
4. Return this subarray.

### **Java Code**

```java
public List<Integer> findClosestElements(int[] arr, int k, int x) {
    int left = 0;
    int right = arr.length - 1;

    while (right - left >= k) {
        if (Math.abs(arr[left] - x) > Math.abs(arr[right] - x))
            left++;
        else
            right--;
    }

    List<Integer> result = new ArrayList<>();
    for (int i = left; i <= right; i++) {
        result.add(arr[i]);
    }
    return result;
}

```

### **Complexity Analysis**

- **Time Complexity:**
    - The pointer movement takes `O(n)` in the worst case.
    - Final extraction of `k` elements: `O(k)`
    - Overall: **O(n)**
- **Space Complexity:**
    - `O(1)` additional space (excluding output list).

### **Dry Run**

- **Input:** `arr = [1, 2, 3, 4, 5]`, `k = 4`, `x = 3`
- **Process:**
    - Initial: `left=0`, `right=4`
    - `abs(1-3)=2`, `abs(5-3)=2` → tie, discard from the right: `right=3`
    - Now: `left=0`, `right=3`
    - `abs(1-3)=2`, `abs(4-3)=1` → discard from the left: `left=1`
    - Now: `left=1`, `right=3`
    - `abs(2-3)=1`, `abs(4-3)=1` → tie, discard from the right: `right=2`
    - Now: `left=1`, `right=2`
    - Window size = 2, which is less than `k=4`, stop.
    - Elements: `[2, 3, 4]` (but only 3 elements).
    
    **Note:** Since `k=4`, we need to adjust the process to ensure 4 elements are selected. In this example, the process will end with the window `[1, 2, 3, 4]` after proper iterations.
    

---

## Approach 3: Most Optimal Solution - Binary Search + Two Pointers

### **Core Idea**

Use binary search to locate the starting index of the `k` closest elements subarray. Because the array is sorted, the `k` closest elements form a contiguous subarray, and binary search can efficiently find the correct starting point.

### **Algorithm**

1. Set `low = 0`, `high = arr.length - k`.
2. While `low < high`:
    - Calculate `mid = (low + high) / 2`.
    - Compare `x - arr[mid]` and `arr[mid + k] - x`.
    - If `x - arr[mid] > arr[mid + k] - x`, move `low` to `mid + 1`.
    - Else, move `high` to `mid`.
3. The starting index of the `k` closest elements is `low`.
4. Return the subarray from `low` to `low + k - 1`.

### **Java Code**

```java
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

- **Time Complexity:**
    - Binary search: `O(log(n - k))` which is approximately `O(log n)`
    - Extracting result: `O(k)`
    - Overall: **O(log n + k)**
- **Space Complexity:**
    - `O(1)` (excluding output list)

### **Dry Run**

- **Input:** `arr = [1, 2, 3, 4, 5]`, `k=4`, `x=3`
- **Process:**
    - `low=0`, `high=1` (since `n - k=1`)
    - `mid=0`
    - Compare `x - arr[mid]=3-1=2` and `arr[mid + k]-x= arr[4]-3=2`
    - Equal, so move `high=mid=0`
    - Loop ends with `low=0`
    - Return subarray `[1, 2, 3, 4]`

---

## Summary Table

| **Approach** | **Time Complexity** | **Space Complexity** | **Notes** |
| --- | --- | --- | --- |
| Brute Force | `O(n log n)` | `O(n)` | Easy but inefficient for large arrays. |
| Two Pointers (Sliding Window) | `O(n)` | `O(1)` | Efficient, leveraging sorted array properties. |
| Binary Search + Two Pointers | `O(log n + k)` | `O(1)` | Most optimal, uses binary search to find start index. |

---

## Final Tips

- For **large datasets**, prefer the binary search approach.
- Remember to **sort the result** if the problem demands the output in ascending order.
- Always consider the **constraints** to choose the most appropriate method.

---

**Happy Coding!**
