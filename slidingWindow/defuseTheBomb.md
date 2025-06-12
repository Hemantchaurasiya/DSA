# Defuse the Bomb

---

## Problem Statement
Given an array `code` representing the code of a bomb, and an integer `k`, you are allowed to change **at most** `k` digits to `0` to defuse the bomb. The goal is to find the **maximum length** of a subarray (continuous segment) of the array where, after changing at most `k` digits to `0`, all digits in that subarray are `0`.

---

## Approach 1: Brute Force

### **Core Idea**
Check every possible subarray, count how many non-zero digits are in it, and if the count is less than or equal to `k`, calculate its length. Keep track of the maximum such length.

### **Algorithm**
1. Loop over every start index `i`.
2. For each `i`, expand the end index `j`:
   - Count non-zero digits in the subarray `code[i ... j]`.
   - If the count exceeds `k`, break the inner loop.
   - Else, update the maximum length.
3. Return the maximum length found.

### **Java Code**
```java
public int longestSubarrayBruteForce(int[] code, int k) {
    int maxLen = 0;
    int n = code.length;

    for (int i = 0; i < n; i++) {
        int nonZeroCount = 0;
        for (int j = i; j < n; j++) {
            if (code[j] != 0) {
                nonZeroCount++;
            }
            if (nonZeroCount > k) {
                break;
            }
            maxLen = Math.max(maxLen, j - i + 1);
        }
    }
    return maxLen;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n^2)`  
  Because of nested loops over the array.
- **Space Complexity:** `O(1)`  
  No extra data structures used.

### **Dry Run**
- Input: `code = [1, 1, 0, 1, 0, 0, 1]`, `k=2`
- For each starting point, expand:
  - Starting at `0`: `[1, 1, 0, 1, 0, 0, 1]`
    - Count non-zero:
      - Up to index 2: 2 non-zero, OK
      - Index 3: Non-zero count = 3, break
    - Max length for start 0: 3
  - Similarly for other starts, update max length accordingly.

---

## Approach 2: Sliding Window (Better Solution)

### **Core Idea**
Use a sliding window to track the number of non-zero digits within a window of variable size. Expand the window until the count of non-zero digits exceeds `k`, then move the start forward to shrink the window.

### **Algorithm**
1. Initialize two pointers: `left=0`, `maxLen=0`.
2. Maintain a count of non-zero digits within the window.
3. Iterate `right` from 0 to `n-1`:
   - If `code[right]` is non-zero, increment count.
   - While count > `k`, move `left` forward:
     - If `code[left]` is non-zero, decrement count.
     - Increment `left`.
   - Update `maxLen` with the current window size (`right - left + 1`).
4. Return `maxLen`.

### **Java Code**
```java
public int longestSubarraySlidingWindow(int[] code, int k) {
    int left = 0, maxLen = 0, nonZeroCount = 0;
    for (int right = 0; right < code.length; right++) {
        if (code[right] != 0) {
            nonZeroCount++;
        }
        while (nonZeroCount > k) {
            if (code[left] != 0) {
                nonZeroCount--;
            }
            left++;
        }
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)`  
  Each element is visited at most twice (once when expanding `right`, once when moving `left`).
- **Space Complexity:** `O(1)`  
  Only counters and pointers.

### **Dry Run**
- Input: `code = [1, 1, 0, 1, 0, 0, 1]`, `k=2`
- Process:
  - Expand `right`, count non-zero:
    - At index 2: count=2, OK
    - At index 3: count=3, exceeds `k`, move `left`:
      - Increment `left`, subtract `code[left]` if non-zero, until count ≤ 2
  - Track maximum length during expansion/shrinking.

---

## Approach 3: Optimized Sliding Window (Most Efficient)

### **Core Idea**
Same as Approach 2, but with clearer code or slight modifications for optimization.

### **Algorithm**
1. Use two pointers and a counter for non-zero digits.
2. Expand `right` and update counts.
3. When count exceeds `k`, move `left` forward updating counts.
4. Track maximum window length where non-zero count ≤ `k`.
5. Return result.

### **Java Code**
```java
public int longestSubarrayOptimized(int[] code, int k) {
    int left = 0, maxLen = 0, countNonZero = 0;
    for (int right = 0; right < code.length; right++) {
        if (code[right] != 0) {
            countNonZero++;
        }
        while (countNonZero > k) {
            if (code[left] != 0) {
                countNonZero--;
            }
            left++;
        }
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`

### **Dry Run**
- Same as above; expand and shrink window based on non-zero count.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Key Points |
|--------------|--------------|-------------------|---------------------|--------------|
| Brute Force | Check all subarrays, count non-zero digits | O(n^2) | O(1) | Not efficient for large inputs |
| Sliding Window | Expand/shrink window, track non-zero count | O(n) | O(1) | Efficient and scalable |
| Optimized Sliding Window | Same as above, cleaner implementation | O(n) | O(1) | Most practical |

---

## Final Tips
- Use sliding window for problems involving subarrays with constraints.
- Maintain counters efficiently to avoid recomputation.
- Always verify edge cases: all zeros, all non-zero, `k` larger than array length.

---

**Happy Revising!**
