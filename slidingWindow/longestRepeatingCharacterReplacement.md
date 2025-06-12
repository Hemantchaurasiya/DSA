# Longest Repeating Character Replacement

This problem involves finding the length of the longest substring where you can replace at most **k** characters to make all characters in the substring identical.

---

## 1. Brute Force Approach

### **Core Idea:**
Enumerate all substrings, and for each, determine the minimum replacements needed to make all characters identical. Keep track of the maximum length where replacements ≤ k.

### **Algorithm:**
1. Generate all possible substrings of the input string.
2. For each substring:
   - Count the frequency of each character.
   - Determine the character with the maximum frequency.
   - Calculate the number of replacements needed: `substring length - max frequency`.
3. If replacements needed ≤ k, update the maximum length.

### **Java Code:**
```java
public int characterReplacement(String s, int k) {
    int maxLen = 0;
    for (int i = 0; i < s.length(); i++) {
        for (int j = i; j < s.length(); j++) {
            String substring = s.substring(i, j + 1);
            int maxFreq = getMaxFrequency(substring);
            int replacements = substring.length() - maxFreq;
            if (replacements <= k) {
                maxLen = Math.max(maxLen, j - i + 1);
            }
        }
    }
    return maxLen;
}

private int getMaxFrequency(String s) {
    int[] count = new int[26];
    int max = 0;
    for (char c : s.toCharArray()) {
        count[c - 'A']++;
        max = Math.max(max, count[c - 'A']);
    }
    return max;
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n^3)**  
  - Generating substrings: O(n^2)  
  - Counting frequencies for each substring: O(n)  
  - Total: O(n^3)

- **Space Complexity:** **O(1)** (fixed size frequency array)

---

## 2. Better Solution (Sliding Window with Frequency Map)

### **Core Idea:**
Use a sliding window to efficiently find the longest valid window (substring) where the number of replacements needed ≤ k. Keep track of character frequencies within the window.

### **Algorithm:**
1. Initialize two pointers: `start = 0`, `end = 0`.
2. Maintain a frequency array/map for characters within the window.
3. Track the count of the most frequent character in the current window (`maxCount`).
4. Expand `end`:
   - Increment the count of the new character.
   - Update `maxCount` if needed.
5. Check if the window size minus `maxCount` exceeds `k`:
   - If yes, shrink the window from the start, decrementing the count of the character at `start`.
   - Move `start` forward.
6. Update max length after each expansion.

### **Java Code:**
```java
public int characterReplacement(String s, int k) {
    int[] count = new int[26];
    int start = 0, maxCount = 0, maxLength = 0;

    for (int end = 0; end < s.length(); end++) {
        count[s.charAt(end) - 'A']++;
        maxCount = Math.max(maxCount, count[s.charAt(end) - 'A']);

        while (end - start + 1 - maxCount > k) {
            count[s.charAt(start) - 'A']--;
            start++;
        }
        maxLength = Math.max(maxLength, end - start + 1);
    }
    return maxLength;
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n)**  
  - Each character is visited at most twice.
- **Space Complexity:** **O(1)** (fixed size frequency array)

---

## 3. Most Optimal Solution (Sliding Window with Character Count & Max Frequency Tracking)

### **Core Idea:**
Leverage the sliding window and keep track of the maximum frequency of any character in the window to decide whether to expand or shrink the window.

### **Algorithm:**
1. Use two pointers: `start` and `end`.
2. Maintain a character count array.
3. Keep track of the `maxFreq` (most frequent character count in the current window).
4. Expand `end`:
   - Increment the count of the current character.
   - Update `maxFreq` if the count of current character exceeds it.
5. If window size minus `maxFreq` > `k`, shrink from `start`:
   - Decrement the count of the character at `start`.
   - Move `start`.
6. Update maximum length after each iteration.

### **Java Code:**
```java
public int characterReplacement(String s, int k) {
    int[] count = new int[26];
    int start = 0, maxFreq = 0, maxLength = 0;

    for (int end = 0; end < s.length(); end++) {
        count[s.charAt(end) - 'A']++;
        maxFreq = Math.max(maxFreq, count[s.charAt(end) - 'A']);

        if (end - start + 1 - maxFreq > k) {
            count[s.charAt(start) - 'A']--;
            start++;
        }
        maxLength = Math.max(maxLength, end - start + 1);
    }
    return maxLength;
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n)**  
  - Each character processed at most twice.
- **Space Complexity:** **O(1)** (fixed size frequency array)

---

## **Dry Run Example**

### Input:
`"AABABBA"`, `k = 1`

| Step | `end` | Char | Count Array | `maxFreq` | `start` | Window Size | Condition (window size - maxFreq) | Action | Max Length |
|-------|--------|-------|--------------|-----------|---------|--------------|-----------------------------------|--------|------------|
| 0     | 0      | 'A'   | {'A': 1}     | 1         | 0       | 1            | 1 - 1 = 0 ≤ 1                     | expand | 1          |
| 1     | 1      | 'A'   | {'A': 2}     | 2         | 0       | 2            | 2 - 2 = 0 ≤ 1                     | expand | 2          |
| 2     | 2      | 'B'   | {'A': 2, 'B': 1} | 2     | 0       | 3            | 3 - 2 = 1 ≤ 1                     | expand | 3          |
| 3     | 3      | 'A'   | {'A': 3, 'B': 1} | 3     | 0       | 4            | 4 - 3 = 1 ≤ 1                     | expand | 4          |
| 4     | 4      | 'B'   | {'A': 3, 'B': 2} | 3     | 0       | 5            | 5 - 3 = 2 > 1 (shrink)          | shrink from start | 4 |
|       |        |       | after shrinking: {'A': 2, 'B': 2} | | 1 | 4 | 4 - 2 = 2 > 1 | shrink again | 4 |
|       |        |       | after shrinking: {'A': 2, 'B': 1} | | 2 | 3 | 3 - 2 = 1 ≤ 1 | expand | 4 |
| 5     | 5      | 'A'   | {'A': 3, 'B': 1} | 3 | 2 | 4 | 4 - 3 = 1 ≤ 1 | expand | 4 |
| 6     | 6      | 'A'   | {'A': 4, 'B': 1} | 4 | 2 | 5 | 5 - 4 = 1 ≤ 1 | expand | 5 |

**Maximum length:** 4 or 5 depending on the window. The longest valid window with at most 1 replacement is `"AABABB"` (length 5).

---

## **Summary**
| Approach | Time Complexity | Space Complexity | Key Idea |
|------------|------------------|------------------|-----------|
| Brute Force | O(n^3) | O(1) | Check all substrings and count replacements |
| Sliding Window with Counting | O(n) | O(1) | Expand/shrink window based on replacements needed |
| Sliding Window with Max Frequency Tracking | O(n) | O(1) | Track max frequency in window to optimize expansion/shrink |

---

## **Final Tips for Interviews:**
- Start with brute-force to establish correctness.
- Use sliding window techniques to optimize to linear time.
- Keep track of the maximum frequency of characters within the window for efficiency.
- Remember to update the window boundaries carefully based on the allowed `k` replacements.

---

**Happy revising!**
