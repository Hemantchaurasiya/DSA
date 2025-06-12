# Minimum Recolors to Get K Consecutive Black Blocks

---

## Problem Statement
Given a string `blocks` consisting of `'B'` (black) and `'W'` (white) characters, and an integer `k`, find the **minimum number of recolors** needed to get **k consecutive black blocks**.

**Recolor** means changing a `'W'` to `'B'`. The goal is to find the minimum recolors required for any substring of length `k` that can be turned into all `'B'`.

---

## Approach 1: Brute Force

### **Core Idea**
Check every substring of length `k`, count how many `'W'` are in each, and determine the minimum number of recolors needed.

### **Algorithm**
1. Loop through each index `i` from `0` to `blocks.length - k`.
2. For each substring `blocks[i ... i + k - 1]`:
   - Count the number of `'W'` in this substring.
3. Track the minimum number of `'W'` found in any such substring.
4. Return this minimum value as the answer.

### **Java Code**
```java
public int minimumRecolorsBruteForce(String blocks, int k) {
    int minRecolors = Integer.MAX_VALUE;
    int n = blocks.length();

    for (int i = 0; i <= n - k; i++) {
        int whiteCount = 0;
        for (int j = i; j < i + k; j++) {
            if (blocks.charAt(j) == 'W') {
                whiteCount++;
            }
        }
        minRecolors = Math.min(minRecolors, whiteCount);
    }
    return minRecolors;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n * k)`  
  For each starting position, counting `'W'` over `k` characters.
- **Space Complexity:** `O(1)`  
  Only a few variables used.

### **Dry Run**
- Input: `blocks = "WBBWWB"`, `k = 3`
- Substrings:
  - `"WBB"` → `'W'` count = 1
  - `"BBW"` → `'W'` count = 1
  - `"BWW"` → `'W'` count = 2
  - `"WWB"` → `'W'` count = 2
- **Minimum recolors:** 1

---

## Approach 2: Sliding Window (Better Solution)

### **Core Idea**
Use a sliding window of size `k` to efficiently count `'W'` characters, updating as the window moves, avoiding recomputation for each substring.

### **Algorithm**
1. Initialize a count of `'W'` in the first window of size `k`.
2. Set `minRecolors` to the initial `'W'` count.
3. Slide the window from index `1` to `n - k`:
   - Remove the impact of the character leaving the window (`blocks[i - 1]`).
   - Add the impact of the new character entering the window (`blocks[i + k - 1]`).
   - Update `minRecolors` with the minimum between current and previous `'W'` count.
4. Return `minRecolors`.

### **Java Code**
```java
public int minimumRecolorsSlidingWindow(String blocks, int k) {
    int n = blocks.length();
    int whiteCount = 0;
    // Count 'W's in the first window
    for (int i = 0; i < k; i++) {
        if (blocks.charAt(i) == 'W') {
            whiteCount++;
        }
    }
    int minRecolors = whiteCount;

    // Slide over the rest of the string
    for (int i = k; i < n; i++) {
        if (blocks.charAt(i - k) == 'W') {
            whiteCount--;
        }
        if (blocks.charAt(i) == 'W') {
            whiteCount++;
        }
        minRecolors = Math.min(minRecolors, whiteCount);
    }
    return minRecolors;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)`  
  Each element is processed once during the sliding.
- **Space Complexity:** `O(1)`  
  Only counters used.

### **Dry Run**
- Input: `blocks = "WBBWWB"`, `k=3`
- Initial window `"WBB"`: `'W'` count = 1
- Slide over:
  - Next window `"BBW"`: remove `'W'` (index 0), add `'W'` (index 3), `'W'` count remains 1
  - Next `"BWW"`: remove `'B'` (index 1), add `'W'` (index 4), `'W'` count = 2
  - Next `"WWB"`: remove `'B'` (index 2), add `'B'` (index 5), `'W'` count = 1
- **Minimum recolors:** 1

---

## Approach 3: Two-Pointer Sliding Window (Most Efficient)

### **Core Idea**
Same as Approach 2; maintain a sliding window of size `k` tracking `'W'` counts, updating efficiently as the window moves.

### **Algorithm**
- Initialize `whiteCount` for the first window.
- Move the window forward, updating `'W'` counts by removing the left element and adding the right element.
- Keep track of the minimum `'W'` count encountered.
- Return the minimum count after processing.

### **Java Code**
```java
public int minimumRecolorsOptimized(String blocks, int k) {
    int n = blocks.length();
    int whiteCount = 0;
    int minRecolors = Integer.MAX_VALUE;
    int left = 0;

    for (int right = 0; right < n; right++) {
        if (blocks.charAt(right) == 'W') {
            whiteCount++;
        }
        if (right - left + 1 > k) {
            if (blocks.charAt(left) == 'W') {
                whiteCount--;
            }
            left++;
        }
        if (right - left + 1 == k) {
            minRecolors = Math.min(minRecolors, whiteCount);
        }
    }
    return minRecolors;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)`  
  Each character is visited once.
- **Space Complexity:** `O(1)`.

### **Dry Run**
- Input: `blocks = "WBBWWB"`, `k=3`
- Process:
  - Expand window to size 3, count `'W'`.
  - Slide window, updating `'W'` count.
  - Track minimum `'W'` count at each step.
- Result: 1

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Key Points |
|--------------|--------------|-------------------|---------------------|--------------|
| Brute Force | Check all substrings of length `k` | O(n * k) | O(1) | Inefficient for large inputs |
| Sliding Window | Count `'W'` in window, slide efficiently | O(n) | O(1) | Most practical solution |
| Two-Pointer Window | Same as above, with optimized pointer handling | O(n) | O(1) | Clean and fast |

---

## Final Tips
- Use sliding window techniques to optimize substring problems involving counts.
- When dealing with minimum or maximum in a fixed-size window, updating counts dynamically is efficient.
- Always consider edge cases like small strings, `k` larger than string length, or all `'B'`/`'W'` strings.

---

**Happy Revising!**
