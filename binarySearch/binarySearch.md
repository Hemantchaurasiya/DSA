# Binary Search

Binary Search is a fundamental algorithm used to efficiently find an element in a sorted array or determine its absence. It repeatedly divides the search space in half, drastically reducing the search time compared to linear search.

---

## 1. Brute Force Approach

### **Core Idea:**

Check each element sequentially until the target element is found or the array is exhausted.

### **Algorithm:**

1. Traverse the array from start to end.
2. Compare each element with the target.
3. If an element matches the target, return its index.
4. If the end is reached without a match, return -1.

### **Java Code:**

```java
public int binarySearchBruteForce(int[] arr, int target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) {
            return i;
        }
    }
    return -1;
}

```

### **Complexity Analysis:**

- **Time Complexity:O(n)**, since it may need to check every element in the worst case.
- **Space Complexity:O(1)**, no extra space is used apart from variables.

### **Dry Run:**

- **Input:** `arr = [1, 3, 5, 7, 9]`, `target = 5`
- **Execution:**
    - Check `arr[0] = 1` → not match
    - Check `arr[1] = 3` → not match
    - Check `arr[2] = 5` → match! Return index `2`

---

## 2. Binary Search - Classic Implementation

### **Core Idea:**

Since the array is sorted, repeatedly divide the search space into halves, narrowing down where the target could be.

### **Algorithm:**

1. Initialize two pointers: `low = 0`, `high = n-1`.
2. While `low <= high`:
    - Find `mid = low + (high - low) / 2`.
    - If `arr[mid] == target`, return `mid`.
    - If `arr[mid] < target`, set `low = mid + 1`.
    - Else, set `high = mid - 1`.
3. If not found, return -1.

### **Java Code:**

```java
public int binarySearch(int[] arr, int target) {
    int low = 0, high = arr.length - 1;
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
    return -1;
}

```

### **Complexity Analysis:**

- **Time Complexity:O(log n)**, as each iteration halves the search space.
- **Space Complexity:O(1)**, no extra space apart from variables.

### **Dry Run:**

- **Input:** `arr = [1, 3, 5, 7, 9]`, `target = 5`
- **Execution:**
    - `low = 0`, `high = 4`, `mid = 2`
        - `arr[2] = 5` → match! Return `2`.

---

## 3. Variations and Optimizations

### **a. Recursive Binary Search**

**Idea:** Use recursion to implement the same logic.

**Java Code:**

```java
public int binarySearchRecursive(int[] arr, int target, int low, int high) {
    if (low > high) return -1;
    int mid = low + (high - low) / 2;
    if (arr[mid] == target) return mid;
    if (arr[mid] < target) {
        return binarySearchRecursive(arr, target, mid + 1, high);
    } else {
        return binarySearchRecursive(arr, target, low, mid - 1);
    }
}

```

**Complexity:** Same as iterative binary search, **O(log n)** time and **O(log n)** space due to recursion stack.

---

## **Summary Table**

| Approach | Idea | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | Linear search | **O(n)** | **O(1)** | Simple but inefficient for large data |
| Binary Search (Iterative) | Divide and conquer on sorted data | **O(log n)** | **O(1)** | Most common and efficient for sorted data |
| Binary Search (Recursive) | Recursive divide and conquer | **O(log n)** | **O(log n)** (stack) | Alternative implementation |

---

## **Key Takeaways**

- **Binary Search** is efficient in sorted arrays, reducing search time from linear to logarithmic.
- Always ensure the array is sorted before applying binary search.
- Be cautious with mid calculation to avoid overflow: `mid = low + (high - low) / 2`.
- Variants include **lower bound**, **upper bound**, and **first/last occurrence** searches.

---

This comprehensive guide covers the fundamental approaches, their intuition, implementation, and analysis for Binary Search, making it a valuable resource for interview prep and quick revision.
