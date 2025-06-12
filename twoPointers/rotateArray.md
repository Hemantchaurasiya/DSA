# Rotate Array

---

## Problem Description
Given an array `nums` and an integer `k`, rotate the array to the right by `k` steps.  
**Example:**  
Input: `nums = [1, 2, 3, 4, 5, 6, 7]`, `k = 3`  
Output: `[5, 6, 7, 1, 2, 3, 4]`

---

## Approach 1: Brute Force Rotation (Repeated Shifting)

### **Core Idea**
Rotate the array by shifting one step at a time, repeating this process `k` times.

### **Algorithm**
1. Normalize `k` by taking `k = k % nums.length` to handle cases where `k >= n`.
2. For each of the `k` steps:
   - Remove the last element.
   - Insert it at the front of the array.
3. Repeat until the array is rotated `k` times.

### **Java Code**
```java
public void rotateBruteForce(int[] nums, int k) {
    int n = nums.length;
    k = k % n;
    for (int i = 0; i < k; i++) {
        int last = nums[n - 1];
        for (int j = n - 1; j > 0; j--) {
            nums[j] = nums[j - 1];
        }
        nums[0] = last;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(k * n)` — For each rotation, shifting elements takes `O(n)`.
- **Space Complexity:** `O(1)` — In-place rotation, no extra space.

### **Dry Run**
Input: `[1, 2, 3, 4, 5, 6, 7]`, `k=3`

| Step | Array before step | Action | Array after step | Comments |
|-------|---------------------|---------|------------------|----------|
| 1     | `[1, 2, 3, 4, 5, 6, 7]` | Shift last to front | `[7, 1, 2, 3, 4, 5, 6]` | First shift |
| 2     | `[7, 1, 2, 3, 4, 5, 6]` | Shift last to front | `[6, 7, 1, 2, 3, 4, 5]` | Second shift |
| 3     | `[6, 7, 1, 2, 3, 4, 5]` | Shift last to front | `[5, 6, 7, 1, 2, 3, 4]` | Third shift |

---

## Approach 2: Using Extra Array for Rotation

### **Core Idea**
Copy elements to a new array in their rotated positions, then copy back to original.

### **Algorithm**
1. Normalize `k` as `k = k % n`.
2. Create a new array `rotated`.
3. For each index `i` in `nums`:
   - Place `nums[i]` at position `(i + k) % n` in `rotated`.
4. Copy `rotated` back to `nums`.

### **Java Code**
```java
public void rotateUsingExtraArray(int[] nums, int k) {
    int n = nums.length;
    k = k % n;
    int[] rotated = new int[n];
    for (int i = 0; i < n; i++) {
        rotated[(i + k) % n] = nums[i];
    }
    System.arraycopy(rotated, 0, nums, 0, n);
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Single pass to assign elements.
- **Space Complexity:** `O(n)` — Extra array used.

### **Dry Run**
Input: `[1, 2, 3, 4, 5, 6, 7]`, `k=3`

| i | `nums[i]` | `(i + k) % n` | `rotated[]` after assignment | Comments |
|---|------------|--------------|------------------------------|----------|
| 0 | 1          | 3            | `[ , , , 1, , , ]` | 1 at index 3 |
| 1 | 2          | 4            | `[ , , , 1, 2, , ]` | 2 at index 4 |
| 2 | 3          | 5            | `[ , , , 1, 2, 3, ]` | 3 at index 5 |
| 3 | 4          | 6            | `[ , , , 1, 2, 3, 4]` | 4 at index 6 |
| 4 | 5          | 0            | `[5, , , 1, 2, 3, 4]` | 5 at index 0 |
| 5 | 6          | 1            | `[5, 6, , 1, 2, 3, 4]` | 6 at index 1 |
| 6 | 7          | 2            | `[5, 6, 7, 1, 2, 3, 4]` | 7 at index 2 |

---

## Approach 3: Reversal Algorithm (Most Optimal)

### **Core Idea**
Rotate the array in-place using three reversals:
- Reverse the entire array.
- Reverse the first `k` elements.
- Reverse the remaining `n-k` elements.

This achieves the rotation efficiently without extra space.

### **Algorithm**
1. Normalize `k` via `k = k % n`.
2. Reverse the entire array.
3. Reverse the first `k` elements.
4. Reverse the remaining `n - k` elements.

### **Java Code**
```java
public void rotateUsingReversal(int[] nums, int k) {
    int n = nums.length;
    k = k % n;
    reverse(nums, 0, n - 1);
    reverse(nums, 0, k - 1);
    reverse(nums, k, n - 1);
}

private void reverse(int[] arr, int start, int end) {
    while (start < end) {
        int temp = arr[start];
        arr[start] = arr[end];
        arr[end] = temp;
        start++;
        end--;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Each element is reversed at most twice.
- **Space Complexity:** `O(1)` — In-place operation.

### **Dry Run**
Input: `[1, 2, 3, 4, 5, 6, 7]`, `k=3`

| Step | Action | Array after step | Comments |
|-------|---------|------------------|----------|
| 1     | Reverse entire array | `[7, 6, 5, 4, 3, 2, 1]` | Reverse all elements |
| 2     | Reverse first `k=3` elements | `[5, 6, 7, 4, 3, 2, 1]` | First 3 elements reversed |
| 3     | Reverse remaining elements | `[5, 6, 7, 1, 2, 3, 4]` | Last 4 elements reversed |

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Suitability |
|------------|--------------|----------------------|-----------------------|--------------|
| Brute Force | Repeatedly shift elements | `O(k * n)` | `O(1)` | Simple but slow for large `k` |
| Extra Array | Use auxiliary array for direct placement | `O(n)` | `O(n)` | Easy to implement, extra space needed |
| Reversal Algorithm | In-place rotation with reversals | `O(n)` | `O(1)` | Most efficient and optimal |

---

## Final Tips
- Always normalize `k` with `k = k % n` to handle cases where `k >= n`.
- The reversal algorithm is the most optimal for large datasets due to its `O(n)` time and `O(1)` space complexity.
- Be cautious with in-place modifications; reversing subarrays must be correctly managed.

---

**Happy Coding!**
