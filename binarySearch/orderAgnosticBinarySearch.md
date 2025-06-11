# Order-Agnostic Binary Search

This guide provides a comprehensive overview of the **Order-Agnostic Binary Search** problem, exploring multiple solution approaches from brute-force to the most optimal. It is structured for quick revision and interview preparation.

---

## 1. Brute Force Approach

### Core Idea:

Since the array could be either sorted in ascending or descending order, and the order is **unknown**, the simplest approach is to **scan through the entire array** to find the target.

### Algorithm:

- Iterate through each element in the array.
- For each element, check if it matches the target.
- If found, return the index.
- If not found after complete traversal, return -1.

### Java Code:

```java
public int orderAgnosticSearch(int[] arr, int target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            return i;
        }
    }
    return -1; // Target not found
}

```

### Complexity Analysis:

- **Time Complexity:O(n)**, since in the worst case, we scan the entire array.
- **Space Complexity:O(1)**, no extra space used apart from variables.

### Dry Run:

- Input: `arr = [4, 5, 6, 7, 0, 1, 2]`, `target = 0`
- Process:
    - Check each element:
        - 4 ≠ 0
        - 5 ≠ 0
        - 6 ≠ 0
        - 7 ≠ 0
        - 0 == 0 → Found at index 4.
- Output: `4`

---

## 2. Better Solution: Determine Order and Use Binary Search

### Core Idea:

First, determine whether the array is sorted ascending or descending by comparing the first and last elements. Then, perform a **binary search tailored to the order**.

### Algorithm:

1. Check array order:
    - If `arr[0] < arr[arr.length - 1]`, array is ascending.
    - Else, array is descending.
2. Use binary search:
    - Set `low = 0`, `high = arr.length - 1`.
    - While `low <= high`:
        - Find `mid = low + (high - low) / 2`.
        - If `arr[mid] == target`, return `mid`.
        - Depending on order:
            - For ascending:
                - If `arr[mid] < target`, move `low = mid + 1`.
                - Else, move `high = mid - 1`.
            - For descending:
                - If `arr[mid] > target`, move `low = mid + 1`.
                - Else, move `high = mid - 1`.
3. If not found, return -1.

### Java Code:

```java
public int orderAgnosticBinarySearch(int[] arr, int target) {
    int low = 0;
    int high = arr.length - 1;

    // Determine if array is ascending or descending
    boolean isAscending = arr[low] < arr[high];

    while (low <= high) {
        int mid = low + (high - low) / 2;

        if (arr[mid] == target) {
            return mid;
        }

        if (isAscending) {
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
    return -1; // Target not found
}

```

### Complexity Analysis:

- **Time Complexity:O(log n)**, since binary search halves the search space each iteration.
- **Space Complexity:O(1)**, no extra space used.

### Dry Run:

- Input: `arr = [4, 5, 6, 7, 0, 1, 2]`, `target = 0`
- Step-by-step:
    - Check order: `4 < 2`? No, `4 > 2`, so descending? No, because `4 > 2`, but the array is actually ascending.
        - Actually, in this example, `arr[0] = 4`, `arr[arr.length - 1] = 2`, so `4 > 2` → descending? No, it's ascending?
        - **Note:** The array is ascending if `arr[0] < arr[arr.length - 1]`. Here, `4 > 2`, so descending? No, it's ascending?
        - Since `4 > 2`, the array is **ascending**.
    - Binary search:
        - `low=0`, `high=6`, `mid=3`, `arr[mid]=7`
        - `7 != 0`, since ascending:
            - `7 > 0`, move `high=2`.
        - `low=0`, `high=2`, `mid=1`, `arr[mid]=5`
        - `5 != 0`, `5 > 0`, move `high=0`.
        - `low=0`, `high=0`, `mid=0`, `arr[mid]=4`
        - `4 != 0`, `4 > 0`, move `high=-1`.
        - Loop ends, target not found.**Note:** Because the array is ascending with 0 at index 4, our initial check should be `arr[0]=4`, `arr[6]=2`, so `4 > 2` → descending? No, because the first element is greater than last, indicating descending.
        - So, the array is **descending**?
        - Wait, the array is `[4, 5, 6, 7, 0, 1, 2]`. First element is 4, last is 2, so `4 > 2` → descending?
        - But the array is not sorted in descending order; it's sorted ascending then rotated?
        - **Important:** The problem states "order-agnostic" but the array can be sorted ascending or descending.
        - **In this case, the array is sorted ascending, but rotated?**
        - **Assumption:** the array is fully sorted, either ascending or descending, not rotated.
        - For the purpose of this problem, assume the array is fully sorted (not rotated).
        - To handle rotated arrays, more logic is needed.
        - For now, assume fully sorted arrays in either ascending or descending order.

---

## 3. Most Optimal Solution: Order-Agnostic Binary Search (Generalized)

### Core Idea:

- First, **determine the array order** (ascending or descending).
- Then, perform a **binary search** adapting the comparison logic accordingly.

### Algorithm:

1. Check whether the array is ascending or descending by comparing `arr[0]` and `arr[arr.length - 1]`.
2. Use a modified binary search:
    - Calculate `mid`.
    - If `arr[mid] == target`, return `mid`.
    - Depending on the order:
        - For ascending:
            - If `arr[mid] < target`, `low = mid + 1`.
            - Else, `high = mid - 1`.
        - For descending:
            - If `arr[mid] > target`, `low = mid + 1`.
            - Else, `high = mid - 1`.

### Java Code:

```java
public int orderAgnosticBinarySearch(int[] arr, int target) {
    if (arr == null || arr.length == 0) {
        return -1;
    }

    int low = 0;
    int high = arr.length - 1;
    boolean isAscending = arr[low] < arr[high];

    while (low <= high) {
        int mid = low + (high - low) / 2;

        if (arr[mid] == target) {
            return mid;
        }

        if (isAscending) {
            if (arr[mid] < target) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        } else { // descending
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

### Complexity Analysis:

- **Time Complexity:O(log n)**, owing to binary search.
- **Space Complexity:O(1)**.

### Dry Run Example:

- Input: `arr = [9, 7, 5, 3, 1]`, `target=5`
- Process:
    - Check order: `9 > 1`, so descending.
    - Binary search:
        - `low=0`, `high=4`, `mid=2`, `arr[mid]=5`
        - `5 == target`, return `2`.

---

## **Summary Table**

| Approach | Core Idea | Algorithm | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- | --- |
| Brute Force | Linear scan, no assumption about order | Loop through array, compare each element | **O(n)** | **O(1)** | Simple but inefficient for large datasets |
| Order-Check + Binary Search | Determine order, then binary search accordingly | Check first and last element, binary search with order-based comparisons | **O(log n)** | **O(1)** | Efficient, handles both ascending and descending arrays |
| Most Optimal | Same as above, optimized for clarity and robustness | Same as above | **O(log n)** | **O(1)** | Best for large datasets |

---

## **Final Tips for Interview**

- Always first check the order of the array (ascending or descending).
- Use binary search to achieve logarithmic time complexity.
- Handle edge cases such as empty arrays or arrays with one element.
- Remember that order-agnostic means the array could be in either sorted order, not rotated.

---

This concludes the comprehensive revision note on **Order-Agnostic Binary Search**. Happy coding!
