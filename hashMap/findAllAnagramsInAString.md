# Find All Anagrams in a String

The **Find All Anagrams in a String** problem asks: **Given a string `s` and a pattern `p`, find all starting indices in `s` where an anagram of `p` occurs.** Essentially, identify all substrings in `s` that are permutations (anagrams) of `p`.

---

## 1. Brute Force Approach

### **Core Idea:**
Generate all substrings of `s` with length equal to `p`, and check if each substring is an anagram of `p`.

### **Algorithm:**
1. For each index `i` in `s` from `0` to `s.length() - p.length()`:
   - Extract substring `s.substring(i, i + p.length())`.
   - Check if this substring is an anagram of `p`:
     - Use character frequency arrays or maps.
2. If it is an anagram, record index `i`.

### **Java Code:**
```java
public List<Integer> findAnagramsBruteForce(String s, String p) {
    List<Integer> result = new ArrayList<>();
    int lenS = s.length(), lenP = p.length();

    for (int i = 0; i <= lenS - lenP; i++) {
        String sub = s.substring(i, i + lenP);
        if (isAnagram(p, sub)) {
            result.add(i);
        }
    }
    return result;
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
- **Time Complexity:** O(n * m), where `n` = length of `s`, `m` = length of `p`.
  - For each substring, counting frequencies takes O(m).
- **Space Complexity:** O(1), fixed-size frequency array for 26 characters.

---

## 2. Better Solution: Sliding Window with Frequency Arrays

### **Core Idea:**
Use a sliding window of size `p.length()` in `s`, updating character counts as the window moves, and compare the frequency arrays to identify anagrams.

### **Algorithm:**
1. Create frequency arrays `need` for `p` and `window` for current window in `s`.
2. Initialize the first window in `s` of size `p.length()`.
3. If the two arrays match, record the start index.
4. Slide the window forward:
   - Remove the count of the character leaving the window.
   - Add the count of the new character entering the window.
   - Compare arrays; if they match, record the start index.

### **Java Code:**
```java
public List<Integer> findAnagramsSlidingWindow(String s, String p) {
    List<Integer> result = new ArrayList<>();
    int lenS = s.length(), lenP = p.length();
    if (lenP > lenS) return result;

    int[] need = new int[26];
    int[] window = new int[26];

    for (int i = 0; i < lenP; i++) {
        need[p.charAt(i) - 'a']++;
        window[s.charAt(i) - 'a']++;
    }

    if (Arrays.equals(need, window)) result.add(0);

    for (int i = lenP; i < lenS; i++) {
        // Remove char leaving window
        window[s.charAt(i - lenP) - 'a']--;
        // Add new char
        window[s.charAt(i) - 'a']++;
        if (Arrays.equals(need, window)) {
            result.add(i - lenP + 1);
        }
    }
    return result;
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n), since each character is processed once, and array comparison is O(1) (fixed size 26).
- **Space Complexity:** O(1), fixed size arrays.

---

## 3. Most Optimal Solution: Sliding Window with Hash Map and Match Count

### **Core Idea:**
Use a hash map for pattern `p` to track character counts. Expand the window in `s`, maintaining a count of how many characters match the required frequency. When all match, record start index, then shrink the window to find all valid starting points.

### **Algorithm:**
1. Build a `need` map for `p`.
2. Use two pointers `left` and `right` to represent the window.
3. Maintain a variable `matchCount` to count how many characters meet the required frequency.
4. Expand `right`:
   - Increment the count of `s[right]` in `window`.
   - If the count matches the `need` count, increment `matchCount`.
5. When `matchCount` equals the total number of unique characters in `need`:
   - If window size equals `p.length()`, record `left`.
   - Shrink from `left`:
     - Decrement the count of `s[left]`.
     - If count no longer matches `need`, decrement `matchCount`.
6. Continue until `right` reaches the end.

### **Java Code:**
```java
public List<Integer> findAnagrams(String s, String p) {
    List<Integer> result = new ArrayList<>();
    if (p.length() > s.length()) return result;

    Map<Character, Integer> need = new HashMap<>();
    for (char c : p.toCharArray()) {
        need.put(c, need.getOrDefault(c, 0) + 1);
    }

    Map<Character, Integer> window = new HashMap<>();
    int left = 0, right = 0, matchCount = 0;
    int required = need.size();

    while (right < s.length()) {
        char c = s.charAt(right);
        window.put(c, window.getOrDefault(c, 0) + 1);

        if (need.containsKey(c) && window.get(c).intValue() == need.get(c).intValue()) {
            matchCount++;
        }

        while (matchCount == required) {
            // Check if current window is a permutation
            if (right - left + 1 == p.length()) {
                result.add(left);
            }
            // Shrink window
            char leftChar = s.charAt(left);
            window.put(leftChar, window.get(leftChar) - 1);
            if (need.containsKey(leftChar) && window.get(leftChar).intValue() < need.get(leftChar).intValue()) {
                matchCount--;
            }
            left++;
        }
        right++;
    }
    return result;
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n), each character is processed at most twice.
- **Space Complexity:** O(m), where `m` is the size of the character set or pattern.

---

## **Dry Run Example**

### Input:
```
s = "cbaebabacd"
p = "abc"
```

### Step-by-step (using the most optimal approach):

| Step | `right` | `s[right]` | `window` Map | `need` Map | `matchCount` | `left` | Comment |
|-------|----------|--------------|--------------|------------|--------------|--------|---------|
| 0     | 0        | 'c'          | {'c':1}     | {'a':1,'b':1,'c':1} | 0 | 0 | 'c' matches need |
| 1     | 1        | 'b'          | {'c':1,'b':1} | same | 1 | 0 | 'b' matches need |
| 2     | 2        | 'a'          | {'c':1,'b':1,'a':1} | same | 2 | 0 | 'a' matches need |
| 3     | 3        | 'e'          | {'c':1,'b':1,'a':1,'e':1} | | 2 | 0 | 'e' not needed |
| 4     | 4        | 'b'          | ... | ... | 2 | 0 | 'b' count increased, but already matched |
| 5     | 5        | 'a'          | ... | ... | 2 | 0 | same |
| 6     | 6        | 'b'          | ... | ... | 2 | 0 | same |
| 7     | 7        | 'c'          | ... | ... | 3 | 0 | matchCount=3, window size = 8, check if size==3 |
|       |        |               |             |             |        |   | Not equal, shrink window |
|       |        |               |             |             |        |   | Move left, update counts and matchCount accordingly |
| ...   | ...      | ...          | ...         | ...         | ...    | ... | Continue until all valid starting indices are found |

- The window "cba" starting at index 0 is an anagram, so index 0 is recorded.
- Similarly, find other anagrams like "bac" at index 6.

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Use Case |
|------------|-----------------|------------------|----------|
| Brute Force | O(n * m) | O(1) | Small strings, brute-force validation |
| Sliding Window with Arrays | O(n) | O(1) | Large strings, ASCII characters |
| Sliding Window with Hash Map & Match Count | O(n) | O(m) | Efficient for varied character sets and large inputs |

---

This comprehensive guide covers multiple approaches, their logic, code, and dry runs for **Find All Anagrams in a String**. Use this as a quick revision or interview preparation resource!
