# Find the Power of K-Size Subarrays I

---

## Problem Statement
Given an array of integers, find the **maximum sum** (or "power") of any subarray of size `k`. This problem helps in understanding sliding window techniques, especially for fixed-size subarrays.

---

## Approach 1: Brute Force

### **Core Idea**
Check **every** subarray of size `k`, compute its sum, and keep track of the maximum sum encountered.

### **Algorithm**
1. Loop through the array from index `0` to `n - k`.
2. For each starting index `i`, compute the sum of the subarray `arr[i ... i + k - 1]`.
3. Keep track of the maximum sum found so far.
4. Return the maximum sum after checking all subarrays.

### **Java Code**
```java
public int maxPowerBruteForce(int[] arr, int k) {
    int maxSum = Integer.MIN_VALUE;
    int n = arr.length;
    for (int i = 0; i <= n - k; i++) {
        int currentSum = 0;
        for (int j = i; j < i + k; j++) {
            currentSum += arr[j];
        }
        maxSum = Math.max(maxSum, currentSum);
    }
    return maxSum;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n * k)`  
  For each starting index, summing `k` elements.
- **Space Complexity:** `O(1)`  
  No extra data structures used.

### **Dry Run**
- Input: `arr = [1, 4, 2, 10, 23, 3, 1, 0, 20]`, `k=4`
- Check subarrays:
  - `[1, 4, 2, 10]` → sum=17
  - `[4, 2, 10, 23]` → sum=39
  - `[2, 10, 23, 3]` → sum=38
  - `[10, 23, 3, 1]` → sum=37
  - `[23, 3, 1, 0]` → sum=27
  - `[3, 1, 0, 20]` → sum=24
- **Maximum sum:** 39

---

## Approach 2: Sliding Window (Better Solution)

### **Core Idea**
Use a sliding window of size `k` to efficiently compute subarray sums:
- Initialize the sum of the first window.
- Slide the window forward by removing the element going out and adding the new element coming in.
- Update the maximum sum during each slide.

### **Algorithm**
1. Calculate the sum of the first `k` elements; store as `currentSum`.
2. Initialize `maxSum` with `currentSum`.
3. Loop from index `k` to `n-1`:
   - Subtract `arr[i - k]` from `currentSum`.
   - Add `arr[i]` to `currentSum`.
   - Update `maxSum` if `currentSum` is greater.
4. Return `maxSum`.

### **Java Code**
```java
public int maxPowerSlidingWindow(int[] arr, int k) {
    int n = arr.length;
    int currentSum = 0;
    // Calculate sum of first window
    for (int i = 0; i < k; i++) {
        currentSum += arr[i];
    }
    int maxSum = currentSum;

    for (int i = k; i < n; i++) {
        currentSum -= arr[i - k];  // Remove element exiting window
        currentSum += arr[i];      // Add new element entering window
        maxSum = Math.max(maxSum, currentSum);
    }
    return maxSum;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)`  
  Each element is added and removed exactly once.
- **Space Complexity:** `O(1)`  
  Only variables for sum and pointers.

### **Dry Run**
- Input: `arr = [1, 4, 2, 10, 23, 3, 1, 0, 20]`, `k=4`
- Initial window sum: `1+4+2+10=17`
- Slide:
  - Remove 1, add 23 → sum = 17 - 1 + 23 = 39 (max=39)
  - Remove 4, add 3 → sum = 39 - 4 + 3 = 38
  - Remove 2, add 1 → sum = 38 - 2 + 1 = 37
  - Remove 10, add 0 → sum = 37 - 10 + 0 = 27
  - Remove 23, add 20 → sum = 27 - 23 + 20 = 24
- Maximum sum: 39

---

## Approach 3: Optimized Sliding Window (Most Efficient)

### **Core Idea**
Same as Approach 2, but with clear variable naming and concise implementation for clarity.

### **Algorithm**
- Initialize sum with first `k` elements.
- Use two pointers to slide the window:
  - When sliding, subtract the element leaving the window and add the new element.
  - Track the maximum sum during the process.

### **Java Code**
```java
public int maxPowerOptimized(int[] arr, int k) {
    int n = arr.length;
    int sum = 0;
    for (int i = 0; i < k; i++) {
        sum += arr[i];
    }
    int maxSum = sum;

    for (int i = k; i < n; i++) {
        sum += arr[i] - arr[i - k];
        maxSum = Math.max(maxSum, sum);
    }
    return maxSum;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`

### **Dry Run**
Same as above, process sliding window over the array to find maximum sum.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Key Points |
|--------------|--------------|-------------------|---------------------|--------------|
| Brute Force | Check all subarrays of size `k`, compute sums | O(n * k) | O(1) | Inefficient for large arrays |
| Sliding Window | Use a window to maintain sum, slide through array | O(n) | O(1) | Efficient and scalable |
| Optimized Sliding Window | Same as above, with clean implementation | O(n) | O(1) | Most practical |

---

## Final Tips
- Sliding window is the go-to technique for fixed-size subarray problems involving sums, maximums, or minimums.
- Always initialize the window sum properly.
- Update the sum efficiently during each slide to avoid recomputation.

---

**Happy Revising!**
