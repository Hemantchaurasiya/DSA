# Permutation in String

The **Permutation in String** problem asks: **Given two strings `s1` and `s2`, determine if `s2` contains any permutation of `s1` as a substring.** In other words, check if a substring of `s2` is an anagram of `s1`.

---

## 1. Brute Force Approach

### **Core Idea:**
Generate all substrings of `s2` with length equal to `s1`, and check if any of them is a permutation (anagram) of `s1`.

### **Algorithm:**
1. Calculate the length `len1` = length of `s1`.
2. Loop through `s2` from index `i = 0` to `i <= s2.length() - len1`.
3. For each substring `s2.substring(i, i + len1)`:
   - Check if it is an anagram of `s1`.
   - To verify, count character frequencies for both strings and compare.
4. If any match is found, return `true`.

### **Java Code:**
```java
public boolean checkInclusionBruteForce(String s1, String s2) {
    int len1 = s1.length();
    int len2 = s2.length();
    if (len1 > len2) return false;

    for (int i = 0; i <= len2 - len1; i++) {
        String window = s2.substring(i, i + len1);
        if (isAnagram(s1, window)) {
            return true;
        }
    }
    return false;
}

private boolean isAnagram(String s1, String s2) {
    int[] count = new int[26];
    for (int i = 0; i < s1.length(); i++) {
        count[s1.charAt(i) - 'a']++;
        count[s2.charAt(i) - 'a']--;
    }
    for (int c : count) {
        if (c != 0) return false;
    }
    return true;
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n * m), where `n` = length of `s2`, `m` = length of `s1`.
  - For each substring, counting characters takes O(m).
- **Space Complexity:** O(1), fixed-size frequency array of size 26.

---

## 2. Better Solution: Sliding Window with Frequency Arrays

### **Core Idea:**
Use a **fixed-size sliding window** of length `s1` in `s2`, updating character counts as the window moves. Check if the current window is an anagram of `s1`.

### **Algorithm:**
1. Create frequency arrays `countS1` and `countWindow` for `s1` and the current window in `s2`.
2. Initialize the first window in `s2` (first `s1.length()` characters).
3. Compare the frequency arrays; if they match, return `true`.
4. Slide the window forward:
   - Remove the count of the character leaving the window.
   - Add the count of the new character entering.
   - After each slide, compare the frequency arrays.
5. If any window matches, return `true`.

### **Java Code:**
```java
public boolean checkInclusionSlidingWindow(String s1, String s2) {
    int len1 = s1.length(), len2 = s2.length();
    if (len1 > len2) return false;

    int[] countS1 = new int[26];
    int[] countWindow = new int[26];

    for (int i = 0; i < len1; i++) {
        countS1[s1.charAt(i) - 'a']++;
        countWindow[s2.charAt(i) - 'a']++;
    }

    if (Arrays.equals(countS1, countWindow)) return true;

    for (int i = len1; i < len2; i++) {
        countWindow[s2.charAt(i) - 'a']++;
        countWindow[s2.charAt(i - len1) - 'a']--;
        if (Arrays.equals(countS1, countWindow)) return true;
    }
    return false;
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n), where `n` = length of `s2`.
  - Each slide updates counts in O(1) (fixed size array), and comparison of arrays is O(1) since size is fixed (26).
- **Space Complexity:** O(1), fixed-size arrays.

---

## 3. Most Optimal Solution: Sliding Window with Hash Map and Match Count

### **Core Idea:**
Utilize a **hash map** to track character frequencies for `s1` and manage a `matchCount` to quickly determine when the current window is a permutation.

### **Algorithm:**
1. Create a frequency map `need` for `s1`.
2. Maintain two pointers: `left` and `right`.
3. Use a variable `matchCount` to track how many characters meet the required frequency.
4. Expand `right`:
   - Increment the count of `s2[right]` in a `window` map.
   - If the character's count in `window` matches `need`, increment `matchCount`.
5. Shrink from `left` when:
   - `matchCount` equals the total number of unique characters in `need`.
   - Update the minimum window length and starting index.
   - Decrement the count for `s2[left]`.
   - If counts no longer match, decrement `matchCount`.
6. Continue until `right` reaches the end.

### **Java Code:**
```java
public boolean checkInclusionOptimized(String s1, String s2) {
    if (s1.length() > s2.length()) return false;

    Map<Character, Integer> need = new HashMap<>();
    for (char c : s1.toCharArray()) {
        need.put(c, need.getOrDefault(c, 0) + 1);
    }

    Map<Character, Integer> window = new HashMap<>();
    int left = 0, right = 0, matchCount = 0;
    int required = need.size();

    while (right < s2.length()) {
        char c = s2.charAt(right);
        window.put(c, window.getOrDefault(c, 0) + 1);

        if (need.containsKey(c) && window.get(c).intValue() == need.get(c).intValue()) {
            matchCount++;
        }

        while (matchCount == required) {
            // Check if current window is a permutation
            if (right - left + 1 == s1.length()) {
                return true;
            }
            // Shrink window
            char leftChar = s2.charAt(left);
            window.put(leftChar, window.get(leftChar) - 1);
            if (need.containsKey(leftChar) && window.get(leftChar).intValue() < need.get(leftChar).intValue()) {
                matchCount--;
            }
            left++;
        }
        right++;
    }
    return false;
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n), traverses `s2` once.
- **Space Complexity:** O(m), where `m` is the size of character set.

---

## **Dry Run Example**

### Input:
```
s1 = "ab"
s2 = "eidbaooo"
```

### Using the most optimal approach:

| Step | `right` | `s2[right]` | `window` Map | `need` Map | `matchCount` | `left` | Comments |
|-------|----------|--------------|--------------|------------|--------------|--------|----------|
| 0     | 0        | 'e'          | {'e':1}     | {'a':1,'b':1} | 0 | 0 | Not in `need` |
| 1     | 1        | 'i'          | {'e':1,'i':1} | same | 0 | 0 | Not in `need` |
| 2     | 2        | 'd'          | {'e':1,'i':1,'d':1} | same | 0 | 0 | Not in `need` |
| 3     | 3        | 'b'          | {'e':1,'i':1,'d':1,'b':1} | {'a':1,'b':1} | 0 | 0 | 'b' in `need`, but count not matching yet |
| 4     | 4        | 'a'          | {'e':1,'i':1,'d':1,'b':1,'a':1} | {'a':1,'b':1} | 1 | 0 | 'a' count matches need, `matchCount`=1 |
| 5     | 5        | 'o'          | ... | ... | 1 | 0 | Not in `need` |
| 6     | 6        | 'o'          | ... | ... | 1 | 0 | Not in `need` |
| 7     | 7        | 'o'          | ... | ... | 1 | 0 | Not in `need` |

- When we reach `right=4`, `matchCount=1` (for 'a') but need both 'a' and 'b' in the window.

- When `right=4`, window length = 5, but `s1` length=2, so continue expanding.

- When the window contains both 'a' and 'b' with matching counts, and size equals `s1.length()`, return `true`.

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Use Case |
|------------|-----------------|------------------|----------|
| Brute Force | O(n * m) | O(1) | Small strings, brute-force validation |
| Sliding Window with Arrays | O(n) | O(1) | Large strings with ASCII characters |
| Sliding Window with Hash Map & Match Count | O(n) | O(m) | Efficient for varied character sets |

---

This detailed revision covers multiple approaches, their logic, code, and dry run to help you understand and quickly recall the **Permutation in String** problem. Use this as a quick reference for interviews or revision!
