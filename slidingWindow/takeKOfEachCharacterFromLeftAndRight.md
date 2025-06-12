# Take K of Each Character From Left and Right

This comprehensive guide covers multiple approaches to solve the problem of selecting **K** occurrences of each character (typically `'a'`, `'b'`, `'c'`) from both the **left** and **right** sides of a string or array. It progresses from brute-force to optimal solutions, structured for quick revision and interview preparation.

---

## Problem Summary
Given a string `s` consisting of characters `'a'`, `'b'`, and `'c'`, and an integer `K`, find the number of ways to choose **K** occurrences of each character from the **left** and **right** sides such that the total selected characters form valid substrings with the specified counts.

*Note:* The exact problem statement can vary; here, the focus is on counting the number of valid configurations where **K** characters of each type are taken from the start and end.

---

## Approach 1: Brute Force

### **Core Idea:**
Iterate over all possible splits in the string, try all combinations of picking `K` characters from the left and right sides, and verify if the counts satisfy the condition.

---

### **Algorithm:**
1. For each possible split position `i`:
   - Take the first `i` characters as the **left part**.
   - Take the last `i` characters as the **right part**.
2. Count the number of `'a'`, `'b'`, `'c'` in both parts.
3. Check if both parts contain at least `K` of each character.
4. If yes, count this as a valid configuration.
5. Sum all such counts over all possible splits.

---

### **Java Code:**
```java
public int takeKFromLeftAndRight(String s, int K) {
    int n = s.length();
    int totalWays = 0;

    for (int i = 0; i <= n; i++) {
        int[] leftCount = new int[3];
        int[] rightCount = new int[3];

        // Count in the left part
        for (int j = 0; j < i; j++) {
            if (s.charAt(j) == 'a') leftCount[0]++;
            else if (s.charAt(j) == 'b') leftCount[1]++;
            else if (s.charAt(j) == 'c') leftCount[2]++;
        }

        // Count in the right part
        for (int j = n - 1; j >= n - i && j >= 0; j--) {
            if (s.charAt(j) == 'a') rightCount[0]++;
            else if (s.charAt(j) == 'b') rightCount[1]++;
            else if (s.charAt(j) == 'c') rightCount[2]++;
        }

        boolean valid = true;
        for (int c = 0; c < 3; c++) {
            if (leftCount[c] < K || rightCount[c] < K) {
                valid = false;
                break;
            }
        }

        if (valid) totalWays++;
    }
    return totalWays;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^2)** — For each split, counting characters involves traversals of parts of the string.

- **Space Complexity:**  
  **O(1)** — Fixed-size counters for `'a'`, `'b'`, `'c'`.

---

### **Dry Run:**

**Input:**
```plaintext
s = "abcabc", K = 1
```

- For `i=0`: no characters from left or right, invalid.
- For `i=1`: left="a", right="c" → counts of `'a'` and `'c'` are 1, `'b'` is 0 → invalid.
- For `i=3`: left="abc", right="abc" → counts of all characters ≥ 1 → valid → totalWays=1.
- Continue for other splits...

---

## Approach 2: Prefix and Suffix Counts (Efficient Counting)

### **Core Idea:**
Precompute prefix counts of `'a'`, `'b'`, `'c'` and suffix counts, then iterate over all split points to determine how many valid combinations exist.

### **Algorithm:**
1. Create prefix count arrays for `'a'`, `'b'`, and `'c'`.
2. Create suffix count arrays similarly.
3. For each split point `i`, check:
   - How many characters in the prefix have at least `K` occurrences.
   - How many characters in the suffix have at least `K` occurrences.
4. Multiply the number of valid prefix options by suffix options to get total configurations.

---

### **Java Code:**
```java
public int takeKFromLeftAndRight(String s, int K) {
    int n = s.length();
    int[] prefixA = new int[n + 1], prefixB = new int[n + 1], prefixC = new int[n + 1];
    int[] suffixA = new int[n + 1], suffixB = new int[n + 1], suffixC = new int[n + 1];

    // Build prefix counts
    for (int i = 0; i < n; i++) {
        prefixA[i + 1] = prefixA[i] + (s.charAt(i) == 'a' ? 1 : 0);
        prefixB[i + 1] = prefixB[i] + (s.charAt(i) == 'b' ? 1 : 0);
        prefixC[i + 1] = prefixC[i] + (s.charAt(i) == 'c' ? 1 : 0);
    }

    // Build suffix counts
    for (int i = n - 1; i >= 0; i--) {
        suffixA[i] = suffixA[i + 1] + (s.charAt(i) == 'a' ? 1 : 0);
        suffixB[i] = suffixB[i + 1] + (s.charAt(i) == 'b' ? 1 : 0);
        suffixC[i] = suffixC[i + 1] + (s.charAt(i) == 'c' ? 1 : 0);
    }

    int totalWays = 0;
    for (int i = 0; i <= n; i++) {
        int prefixCount = 0;
        int suffixCount = 0;

        // Check prefix side
        if (prefixA[i] >= K) prefixCount++;
        if (prefixB[i] >= K) prefixCount++;
        if (prefixC[i] >= K) prefixCount++;

        // Check suffix side
        if (suffixA[i] >= K) suffixCount++;
        if (suffixB[i] >= K) suffixCount++;
        if (suffixC[i] >= K) suffixCount++;

        // Count matching configurations
        totalWays += prefixCount * suffixCount;
    }

    return totalWays;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** — Preprocessing prefix and suffix counts takes linear time, and iteration over split points is linear.

- **Space Complexity:**  
  **O(n)** — Arrays for prefix and suffix counts.

---

### **Dry Run:**

**Input:**
```plaintext
s = "abcabc", K=1
```

- Prefix counts at each index:
  - i=0: a=1, b=0, c=0
  - i=1: a=1, b=1, c=0
  - i=2: a=1, b=1, c=1
  - i=3: a=2, b=1, c=1
  - ...
- Suffix counts similarly.
- For each split, check how many characters meet the K condition, then multiply.

---

## Approach 3: Sliding Window (Most Efficient)

### **Core Idea:**
Use a sliding window to find the minimal substring that contains **at least K** of each character, then count the number of valid starting points.

### **Algorithm:**
1. Use two pointers (`left` and `right`) to maintain a window.
2. Count occurrences of `'a'`, `'b'`, `'c'` within the window.
3. Expand `right` until all characters have at least `K`.
4. For each valid window, move `left` forward to find the minimal window containing all K characters.
5. The number of valid substrings starting at `left` and ending at or after `right` can be calculated, summing over all positions.

---

### **Java Code:**
```java
public int takeKFromLeftAndRight(String s, int K) {
    int n = s.length();
    int[] count = new int[3];
    int left = 0, result = 0;

    for (int right = 0; right < n; right++) {
        count[s.charAt(right) - 'a']++;

        while (count[0] >= K && count[1] >= K && count[2] >= K) {
            // All characters meet the requirement
            // Count the number of starting points for the current window
            result += n - right; // All substrings ending at or after right
            count[s.charAt(left) - 'a']--;
            left++;
        }
    }
    return result;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** — Each character is visited at most twice (expanding and contracting window).

- **Space Complexity:**  
  **O(1)** — Fixed size count array.

---

### **Dry Run:**

**Input:**
```plaintext
s = "abcabc", K=1
```

- Expand `right` until all `'a'`, `'b'`, `'c'` are at least 1.
- Count valid substrings for each window, then shrink from the left.

---

## Summary of Approaches:

| Approach | Time Complexity | Space Complexity | Notes |
|------------|-------------------|--------------------|--------|
| Brute Force | **O(n^2)** | **O(1)** | Simple but inefficient. |
| Prefix/Suffix Counts | **O(n)** | **O(n)** | Efficient, uses precomputations. |
| Sliding Window | **O(n)** | **O(1)** | Most optimal, dynamic approach. |

---

## Final Tips:
- Use the sliding window approach for the best performance.
- Precompute prefix/suffix counts for faster calculations if needed.
- Validate with different values of `K` and string configurations.
- Understand the problem constraints to choose the optimal method.

---

This guide provides a structured overview for understanding, implementing, and optimizing solutions for **Take K of Each Character From Left and Right**, preparing you confidently for interviews.
