# Longest Substring Without Repeating Characters

This problem asks us to find the length of the **longest substring** in a given string that contains **all unique characters** (no repeats). It is a classic problem to practice **sliding window** techniques and hash-based data structures.

---

## 1. Brute Force Approach

### Core Idea:
Check **all possible substrings** and determine if they have all unique characters. Keep track of the maximum length among valid substrings.

### Algorithm:
1. Generate all substrings using two nested loops:
   - Outer loop: start index `i`.
   - Inner loop: end index `j`.
2. For each substring `s[i..j]`, check if it has all unique characters:
   - Use a set to track characters in the current substring.
   - If a duplicate is found, break early.
3. Update the maximum length if the current substring is valid.

### Java Code:
```java
public int lengthOfLongestSubstringBruteForce(String s) {
    int maxLength = 0;
    for (int i = 0; i < s.length(); i++) {
        Set<Character> set = new HashSet<>();
        for (int j = i; j < s.length(); j++) {
            if (set.contains(s.charAt(j))) {
                break;
            } else {
                set.add(s.charAt(j));
                maxLength = Math.max(maxLength, j - i + 1);
            }
        }
    }
    return maxLength;
}
```

### Complexity Analysis:
- **Time Complexity:** O(n^3)
  - Generating all substrings: O(n^2)
  - Checking for duplicates in each substring: O(n)
- **Space Complexity:** O(min(n, m))
  - `m`: size of character set (e.g., 26 for lowercase, 128 for ASCII)
  - Uses a set to track characters per substring.

---

## 2. Better Solution: Sliding Window with HashSet

### Core Idea:
Use a **sliding window** to keep track of the current substring with unique characters, expanding the window when possible and shrinking when duplicates are found.

### Algorithm:
1. Initialize two pointers: `left = 0`, `right = 0`.
2. Use a `HashSet` to store characters in the current window.
3. Expand `right` pointer:
   - If `s[right]` is not in set, add it and update max length.
   - If it is in set, move `left` forward, removing `s[left]` from set until the duplicate is removed.
4. Continue until `right` reaches the end.

### Java Code:
```java
public int lengthOfLongestSubstringSlidingWindow(String s) {
    Set<Character> set = new HashSet<>();
    int maxLength = 0, left = 0, right = 0;
    while (right < s.length()) {
        if (!set.contains(s.charAt(right))) {
            set.add(s.charAt(right));
            maxLength = Math.max(maxLength, right - left + 1);
            right++;
        } else {
            set.remove(s.charAt(left));
            left++;
        }
    }
    return maxLength;
}
```

### Complexity Analysis:
- **Time Complexity:** O(n)
  - Each character is visited at most twice (once when expanding `right`, once when moving `left`).
- **Space Complexity:** O(min(n, m))
  - HashSet stores at most all characters in the current window.

---

## 3. Most Optimal Solution: Sliding Window with HashMap (Optimized)

### Core Idea:
Instead of moving `left` one step at a time, jump directly to the position after the last occurrence of a repeated character, reducing unnecessary steps.

### Algorithm:
1. Use a `HashMap<Character, Integer>` to store the last index of each character.
2. Initialize `left = 0`, `maxLength = 0`.
3. Iterate with index `right`:
   - If `s[right]` has appeared before and its last index is >= `left`, update `left` to `lastIndex + 1`.
   - Update the last index of `s[right]` in the map.
   - Update `maxLength` with the current window size.

### Java Code:
```java
public int lengthOfLongestSubstringOptimized(String s) {
    Map<Character, Integer> lastIndexMap = new HashMap<>();
    int maxLength = 0, left = 0;
    for (int right = 0; right < s.length(); right++) {
        char currentChar = s.charAt(right);
        if (lastIndexMap.containsKey(currentChar) && lastIndexMap.get(currentChar) >= left) {
            left = lastIndexMap.get(currentChar) + 1;
        }
        lastIndexMap.put(currentChar, right);
        maxLength = Math.max(maxLength, right - left + 1);
    }
    return maxLength;
}
```

### Complexity Analysis:
- **Time Complexity:** O(n)
  - Each character is processed once, with direct jumps for duplicates.
- **Space Complexity:** O(min(n, m))
  - HashMap stores last indices of characters.

---

## **Dry Run Example**

### Input:
```
s = "abcabcbb"
```

### Using the optimized approach:

| Step | `right` | `currentChar` | `lastIndexMap`             | `left` | `maxLength` | Explanation                                                    |
|--------|---------|--------------|----------------------------|--------|--------------|----------------------------------------------------------------|
| 0      | 0       | 'a'          | {'a': 0}                   | 0      | 1            | 'a' not seen before, window: "a"                              |
| 1      | 1       | 'b'          | {'a': 0, 'b': 1}           | 0      | 2            | 'b' not seen, window: "ab"                                    |
| 2      | 2       | 'c'          | {'a': 0, 'b': 1, 'c': 2}   | 0      | 3            | 'c' not seen, window: "abc"                                   |
| 3      | 3       | 'a'          | {'a': 3, 'b': 1, 'c': 2}   | 1      | 3            | 'a' seen at index 0, move `left` to 1, update 'a' index to 3   |
| 4      | 4       | 'b'          | {'a': 3, 'b': 4, 'c': 2}   | 2      | 3            | 'b' seen at 1, move `left` to 2, update 'b' index to 4       |
| 5      | 5       | 'c'          | {'a': 3, 'b': 4, 'c': 5}   | 3      | 3            | 'c' seen at 2, move `left` to 3, update 'c' index to 5     |
| 6      | 6       | 'b'          | {'a': 3, 'b': 6, 'c': 5}   | 4      | 3            | 'b' at 4, move `left` to 5, update 'b' index to 6        |
| 7      | 7       | 'b'          | {'a': 3, 'b': 7, 'c': 5}   | 6      | 2            | 'b' at 6, move `left` to 7, window: "b" only          |

**Result:** The maximum length found is **3** for substring "abc".

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Use Case |
|------------|-----------------|------------------|----------|
| Brute Force | O(n^3) | O(min(n,m)) | Small input strings, brute-force testing |
| Sliding Window with HashSet | O(n) | O(min(n,m)) | Efficient, suitable for large inputs |
| Sliding Window with HashMap | O(n) | O(min(n,m)) | Optimal, handles character repetitions efficiently |

---

This comprehensive guide should serve as a quick revision resource and interview prep for the **Longest Substring Without Repeating Characters** problem.
