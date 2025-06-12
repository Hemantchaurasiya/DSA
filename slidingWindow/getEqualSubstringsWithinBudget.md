# Get Equal Substrings Within Budget

This problem involves finding the length of the longest substring in which we can change at most **k** characters to make all characters identical.

---

## 1. Brute Force Approach

### **Approach Name:**  
Brute Force

### **Core Idea:**  
Examine all substrings, and for each, count the number of characters that need to be changed to make the entire substring uniform. Keep track of the maximum length among valid substrings.

### **Algorithm:**  
1. Generate all possible substrings.
2. For each substring:
   - Count the frequency of each character.
   - Find the character with the maximum frequency.
   - Calculate the number of changes needed: `substring length - max frequency`.
   - If changes ≤ `k`, update the maximum length.
3. Return the maximum length found.

### **Java Code:**
```java
public int characterReplacement(String s, int k) {
    int maxLen = 0;
    int n = s.length();

    for (int start = 0; start < n; start++) {
        for (int end = start; end < n; end++) {
            String substring = s.substring(start, end + 1);
            int maxFreq = getMaxFrequency(substring);
            int changesNeeded = substring.length() - maxFreq;
            if (changesNeeded <= k) {
                maxLen = Math.max(maxLen, end - start + 1);
            }
        }
    }
    return maxLen;
}

private int getMaxFrequency(String s) {
    int[] count = new int[26];
    int maxCount = 0;
    for (char c : s.toCharArray()) {
        count[c - 'A']++;
        maxCount = Math.max(maxCount, count[c - 'A']);
    }
    return maxCount;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n^3)**  
  - Generating all substrings: O(n^2)  
  - Counting frequencies for each substring: O(n)  
- **Space Complexity:** **O(1)** (fixed size frequency array)

---

## 2. Better Solution (Sliding Window with Character Counts)

### **Approach Name:**  
Sliding Window with Frequency Map

### **Core Idea:**  
Use a sliding window to find the longest valid substring where the number of characters needing change is ≤ `k`. Keep track of character frequencies within the window, expanding and shrinking as needed.

### **Algorithm:**  
1. Initialize `left = 0` and `maxCount = 0`.
2. Use a HashMap (or array) to store character counts.
3. Iterate `right` over the string:
   - Increment the count of the current character.
   - Update `maxCount` to be the maximum frequency among characters in the window.
   - If window size - `maxCount` > `k`, shrink from the left:
     - Decrement the count of the character at `left`.
     - Move `left` forward.
4. Track the maximum window size throughout.

### **Java Code:**
```java
public int characterReplacement(String s, int k) {
    int[] count = new int[26];
    int left = 0, maxCount = 0, maxLen = 0;

    for (int right = 0; right < s.length(); right++) {
        count[s.charAt(right) - 'A']++;
        maxCount = Math.max(maxCount, count[s.charAt(right) - 'A']);

        while (right - left + 1 - maxCount > k) {
            count[s.charAt(left) - 'A']--;
            left++;
        }
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**  
  - Each character processed at most twice (once when expanding, once when shrinking).  
- **Space Complexity:** **O(1)** (fixed size array of 26 for characters)

---

## 3. Most Optimal Solution (Optimized Sliding Window with Max Frequency Tracking)

### **Approach Name:**  
Sliding Window with Dynamic Max Frequency

### **Core Idea:**  
Track the maximum frequency of any character in the current window while expanding. If the window becomes invalid (more than `k` changes needed), shrink from the left. This approach is similar to the previous, but emphasizes updating `maxCount` efficiently.

### **Algorithm:**  
1. Initialize `left = 0`, `maxCount = 0`.
2. Use a count array for character frequencies.
3. Iterate `right` over the string:
   - Increment the count of the current character.
   - Update `maxCount` to be the maximum frequency in the window.
   - If window size - `maxCount` > `k`, shrink from the left:
     - Decrement the count of the character at `left`.
     - Increment `left`.
4. Continue updating maximum length during iteration.

### **Java Code:**
```java
public int characterReplacement(String s, int k) {
    int[] count = new int[26];
    int left = 0, maxCount = 0, maxLen = 0;

    for (int right = 0; right < s.length(); right++) {
        count[s.charAt(right) - 'A']++;
        maxCount = Math.max(maxCount, count[s.charAt(right) - 'A']);

        if (right - left + 1 - maxCount > k) {
            count[s.charAt(left) - 'A']--;
            left++;
        }
        maxLen = Math.max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**  
- **Space Complexity:** **O(1)**

---

## **Dry Run Example**

### Input:
`"AABABBA"`, `k=2`

| Step | `right` | Char | Count Array | `maxCount` | `left` | Window Length | Condition (window size - maxCount) | Action | Max Length |
|-------|----------|-------|--------------|------------|--------|--------------|-------------------------------------|--------|------------|
| 0     | 0        | 'A'   | {A:1}       | 1          | 0      | 1            | 1 - 1 = 0 ≤ 2                      | expand | 1          |
| 1     | 1        | 'A'   | {A:2}       | 2          | 0      | 2            | 2 - 2 = 0 ≤ 2                      | expand | 2          |
| 2     | 2        | 'B'   | {A:2, B:1}  | 2          | 0      | 3            | 3 - 2 = 1 ≤ 2                      | expand | 3          |
| 3     | 3        | 'A'   | {A:3, B:1}  | 3          | 0      | 4            | 4 - 3 = 1 ≤ 2                      | expand | 4          |
| 4     | 4        | 'B'   | {A:3, B:2}  | 3          | 0      | 5            | 5 - 3 = 2 ≤ 2                      | expand | 5          |
| 5     | 5        | 'B'   | {A:3, B:3}  | 3          | 0      | 6            | 6 - 3 = 3 > 2 → shrink from left | shrink | 5 (max) |
|       |          |       | {A:2, B:3}  |            | 1      | 5            | 5 - 3 = 2 ≤ 2                      | continue | 5     |
| 6     | 6        | 'A'   | {A:3, B:3}  | 3          | 2      | 5            | 5 - 3 = 2 ≤ 2                      | expand | 5          |

**Maximum length of valid substring:** 5

---

## **Summary**

| Approach | Time Complexity | Space Complexity | Key Idea |
|------------|------------------|------------------|------------|
| Brute Force | O(n^3) | O(1) | Check all substrings, count changes needed |
| Sliding Window with Counts | O(n) | O(1) | Use sliding window with character counts to find max length with ≤k changes |
| Optimized Sliding Window | O(n) | O(1) | Track max frequency dynamically, shrink window when invalid |

---

## **Final Tips for Interviews:**
- Start with the brute-force to understand the problem.
- Use sliding window with character counts for efficiency.
- Keep track of the maximum frequency in the current window to minimize recalculations.
- Always verify the window's validity (i.e., changes needed ≤ `k`) during expansion/shrinking.

---

**Happy Revising!**
