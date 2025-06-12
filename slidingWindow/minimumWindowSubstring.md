# Minimum Window Substring

The **Minimum Window Substring** problem involves finding the **smallest substring** of a given string `s` that contains **all the characters** (including duplicates) of another string `t`. If no such substring exists, return an empty string.

---

## 1. Brute Force Approach

### **Core Idea:**
Generate all possible substrings of `s` and check if each contains all characters of `t`. Track the smallest valid substring.

### **Algorithm:**
1. Generate all substrings of `s` using two nested loops:
   - Outer loop: start index `i`.
   - Inner loop: end index `j`.
2. For each substring `s[i..j]`, check if it contains all characters of `t`:
   - Use a frequency map for `t`.
   - Use another frequency map for the current substring.
   - Verify if all characters in `t` are present with required counts.
3. Update the minimum length and record the substring when a valid window is found.

### **Java Code:**
```java
public String minWindowBruteForce(String s, String t) {
    int minLen = Integer.MAX_VALUE;
    String result = "";
    for (int i = 0; i < s.length(); i++) {
        for (int j = i; j < s.length(); j++) {
            String window = s.substring(i, j + 1);
            if (containsAllChars(window, t)) {
                if (window.length() < minLen) {
                    minLen = window.length();
                    result = window;
                }
            }
        }
    }
    return result;
}

private boolean containsAllChars(String window, String t) {
    Map<Character, Integer> freqWindow = new HashMap<>();
    Map<Character, Integer> freqT = new HashMap<>();
    for (char c : t.toCharArray()) {
        freqT.put(c, freqT.getOrDefault(c, 0) + 1);
    }
    for (char c : window.toCharArray()) {
        freqWindow.put(c, freqWindow.getOrDefault(c, 0) + 1);
    }
    for (char c : freqT.keySet()) {
        if (freqWindow.getOrDefault(c, 0) < freqT.get(c))
            return false;
    }
    return true;
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n^3)
  - Generating all substrings: O(n^2)
  - Checking character counts in each substring: O(n)
- **Space Complexity:** O(m), where `m` is the size of character set (for the frequency maps).

---

## 2. Better Solution: Sliding Window with Frequency Maps

### **Core Idea:**
Use a **sliding window** to dynamically check substrings, expanding the window until it contains all required characters, then contracting to find the minimal window.

### **Algorithm:**
1. Count the frequency of each character in `t` using a map (`need`).
2. Maintain two pointers: `left` and `right` to define the window.
3. Use a map (`window`) to track character frequencies in the current window.
4. Expand `right`:
   - Include `s[right]` in the window.
   - If the character is needed (exists in `need`) and its count matches, increment `formed`.
5. When `formed` equals the total number of unique characters in `t`, try to shrink the window from the left:
   - Update the minimum window if current window is smaller.
   - Remove `s[left]` from the window, move `left` forward.
   - Update `formed` if needed.
6. Continue until `right` reaches the end.

### **Java Code:**
```java
public String minWindowSlidingMap(String s, String t) {
    if (s.length() < t.length()) return "";

    Map<Character, Integer> need = new HashMap<>();
    for (char c : t.toCharArray()) {
        need.put(c, need.getOrDefault(c, 0) + 1);
    }

    Map<Character, Integer> window = new HashMap<>();
    int left = 0, right = 0, formed = 0;
    int minLen = Integer.MAX_VALUE;
    int minLeft = 0;

    while (right < s.length()) {
        char c = s.charAt(right);
        window.put(c, window.getOrDefault(c, 0) + 1);

        if (need.containsKey(c) && window.get(c).intValue() == need.get(c).intValue()) {
            formed++;
        }

        while (left <= right && formed == need.size()) {
            // Update minimum window
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                minLeft = left;
            }
            // Shrink window
            char leftChar = s.charAt(left);
            window.put(leftChar, window.get(leftChar) - 1);
            if (need.containsKey(leftChar) && window.get(leftChar).intValue() < need.get(leftChar).intValue()) {
                formed--;
            }
            left++;
        }
        right++;
    }

    return minLen == Integer.MAX_VALUE ? "" : s.substring(minLeft, minLeft + minLen);
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n)
  - Each character is visited at most twice (once when expanding `right`, once when moving `left`).
- **Space Complexity:** O(m + n)
  - `need` and `window` maps store character counts, where `m` is size of character set.

---

## 3. Most Optimal Solution: Sliding Window with Frequency Map & Two Pointers

### **Core Idea:**
Use a sliding window with precise control over character counts, moving `right` to include necessary characters and moving `left` to minimize the window when all conditions are met.

### **Algorithm:**
1. Count character frequencies in `t` using `need`.
2. Maintain two pointers `left` and `right`.
3. Use a variable `formed` to track how many characters currently meet the required frequency.
4. Expand `right`:
   - Include `s[right]` in the window.
   - Update counts and `formed` accordingly.
5. When all characters are satisfied (`formed == need.size()`):
   - Update answer if current window is smaller.
   - Contract from `left`:
     - Update counts, decrease `formed` if counts drop below needed.
6. Continue until `right` reaches the end of `s`.

### **Java Code:**
```java
public String minWindowOptimized(String s, String t) {
    if (s.length() < t.length()) return "";

    Map<Character, Integer> need = new HashMap<>();
    for (char c : t.toCharArray()) {
        need.put(c, need.getOrDefault(c, 0) + 1);
    }

    Map<Character, Integer> window = new HashMap<>();
    int left = 0, right = 0;
    int formed = 0;
    int minLen = Integer.MAX_VALUE;
    int minLeft = 0;

    for (right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        window.put(c, window.getOrDefault(c, 0) + 1);

        if (need.containsKey(c) && window.get(c).intValue() == need.get(c).intValue()) {
            formed++;
        }

        while (left <= right && formed == need.size()) {
            if (right - left + 1 < minLen) {
                minLen = right - left + 1;
                minLeft = left;
            }
            char leftChar = s.charAt(left);
            window.put(leftChar, window.get(leftChar) - 1);
            if (need.containsKey(leftChar) && window.get(leftChar).intValue() < need.get(leftChar).intValue()) {
                formed--;
            }
            left++;
        }
    }

    return minLen == Integer.MAX_VALUE ? "" : s.substring(minLeft, minLeft + minLen);
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n)
- **Space Complexity:** O(m + n)

---

## **Dry Run Example**

### Input:
```
s = "ADOBECODEBANC"
t = "ABC"
```

### Step-by-step (using the optimized approach):

| Step | `right` | `s[right]` | `window` Map | `need` Map | `formed` | `left` | `minLen` | Comment |
|-------|----------|------------|--------------|------------|----------|--------|----------|---------|
| 0     | 0        | 'A'        | {'A': 1}     | {'A': 1, 'B': 1, 'C': 1} | 0 | 0 | ∞ | 'A' needed, not yet formed |
| 1     | 1        | 'D'        | {'A': 1, 'D': 1} | same | 0 | 0 | ∞ | 'D' not needed |
| 2     | 2        | 'O'        | {'A': 1, 'D': 1, 'O': 1} | same | 0 | 0 | ∞ | 'O' not needed |
| 3     | 3        | 'B'        | {'A': 1, 'D': 1, 'O': 1, 'B': 1} | {'A': 1, 'B': 1, 'C': 1} | 1 | 0 | ∞ | 'B' needed, += 1, now `formed`=1 |
| 4     | 4        | 'E'        | ... | ... | 1 | 0 | ∞ | Not all characters yet |
| 5     | 5        | 'C'        | ... | ... | 2 | 0 | ∞ | 'C' needed, `formed`=2 |
| 6     | 6        | 'O'        | ... | ... | 2 | 0 | ∞ | Already have 'O' in window |
| 7     | 7        | 'D'        | ... | ... | 2 | 0 | ∞ | 'D' in window |
| 8     | 8        | 'E'        | ... | ... | 2 | 0 | ∞ | 'E' not needed |
| 9     | 9        | 'B'        | ... | ... | 2 | 0 | ∞ | 'B' count increases, no change in `formed` |
| 10    | 10       | 'A'        | ... | ... | 2 | 0 | ∞ | Same |
| 11    | 11       | 'N'        | ... | ... | 2 | 0 | ∞ | Not needed |
| 12    | 12       | 'C'        | ... | ... | 3 | 0 | 12 | Now have 'A', 'B', 'C' in window, `formed`=3 |

- When `formed`=3, window is from 0 to 12 ("ADOBECODEBANC"), currently the whole string.
- Try shrinking from left:
  - Move `left` forward, updating counts.
  - When `left` moves past 'A', the window becomes smaller.
  - Continue until the smallest window containing all characters is found.

**Final answer:** `"BANC"` with length 4.

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Use Case |
|------------|-----------------|------------------|----------|
| Brute Force | O(n^3) | O(m) | Small strings, brute-force validation |
| Sliding Window with HashMaps | O(n) | O(m + n) | Efficient, suitable for large inputs with repeated characters |
| Optimized Sliding Window | O(n) | O(m + n) | Most efficient, handles duplicates and minimal window expansion |

---

This guide provides a clear understanding of the problem, multiple approaches, and their complexities, along with a dry run for better intuition. Use this as a quick revision sheet or for interview preparation!
