# Valid Anagram

## Problem Statement
Given two strings `s` and `t`, determine if `t` is an anagram of `s`.  
*An anagram* is a word or phrase formed by rearranging the letters of a different word or phrase, typically using all the original letters exactly once.

---

## Approach 1: Brute Force

### Core Idea
Check if **both strings have the same characters with the same frequency** by comparing character counts directly.

### Algorithm
1. Convert both strings to character arrays.
2. For each string, count the frequency of each character using a nested loop or a frequency array.
3. Compare the frequency counts for both strings.
4. If they match, return `true`; otherwise, `false`.

### Java Code
```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;

    for (int i = 0; i < s.length(); i++) {
        char c = s.charAt(i);
        int countInS = 0, countInT = 0;

        // Count occurrences of c in s
        for (int j = 0; j < s.length(); j++) {
            if (s.charAt(j) == c) countInS++;
        }

        // Count occurrences of c in t
        for (int j = 0; j < t.length(); j++) {
            if (t.charAt(j) == c) countInT++;
        }

        if (countInS != countInT) return false;
    }
    return true;
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(n^2)**, since for each character in `s`, we scan the entire strings to count occurrences.
- **Space Complexity:**  
  **O(1)**, no extra space (assuming fixed character set, e.g., ASCII).

### Dry Run
| s = "aab", t = "aba" |  
|-------------------------|  
| i=0, c='a'             |  
| Count in s: 2          |  
| Count in t: 2          |  
| Continue for other characters |  
| Final check passes, return `true` |

---

## Approach 2: Sorting

### Core Idea
Two strings are anagrams if **sorting both strings results in the same string**.

### Algorithm
1. Convert both strings to character arrays.
2. Sort both arrays.
3. Convert them back to strings or compare directly.
4. If sorted strings are equal, return `true`; otherwise, `false`.

### Java Code
```java
import java.util.Arrays;

public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;

    char[] sArr = s.toCharArray();
    char[] tArr = t.toCharArray();

    Arrays.sort(sArr);
    Arrays.sort(tArr);

    return Arrays.equals(sArr, tArr);
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(n log n)**, due to sorting (where `n` is string length).
- **Space Complexity:**  
  **O(n)**, for the character arrays used during sorting.

### Dry Run
| s = "listen", t = "silent" |  
|----------------------------|  
| Sorted s: "eilnst"        |  
| Sorted t: "eilnst"        |  
| Both equal, return `true` |

---

## Approach 3: Hash Map / Frequency Count (Most Optimal Solution)

### Core Idea
Count the frequency of each character in `s`. Then, decrement the count for each character in `t`.  
If all counts are zero at the end, strings are anagrams.

### Algorithm
1. Check if lengths differ; if yes, return `false`.
2. Initialize a hash map or an integer array (for fixed charset, e.g., ASCII).
3. Traverse `s`, increment the count for each character.
4. Traverse `t`, decrement the count for each character.
5. Verify if all counts are zero.
6. Return `true` if all counts are zero; otherwise, `false`.

### Java Code
```java
public boolean isAnagram(String s, String t) {
    if (s.length() != t.length()) return false;

    int[] charCounts = new int[26]; // assuming only lowercase letters

    for (int i = 0; i < s.length(); i++) {
        charCounts[s.charAt(i) - 'a']++;
    }

    for (int i = 0; i < t.length(); i++) {
        charCounts[t.charAt(i) - 'a']--;
    }

    for (int count : charCounts) {
        if (count != 0) return false;
    }
    return true;
}
```

*Note:* For general ASCII or Unicode, use a `HashMap<Character, Integer>`.

### Complexity Analysis
- **Time Complexity:**  
  **O(n)**, since we traverse both strings once.
- **Space Complexity:**  
  **O(1)**, for fixed character set (e.g., 26 for lowercase). For larger charset, **O(k)** where `k` is character range.

### Dry Run
| s = "race", t = "care" |  
|-------------------------|  
| charCounts initially: all zeros |  
| After processing s: [r=1, a=1, c=1, e=1] |  
| After processing t: decrement counts |  
| r=0, a=0, c=0, e=0 |  
| All zero, return `true` |

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Best Use Case |
|------------|--------------|-------------------|------------------|--------------|
| Brute Force | Compare character counts via nested loops | O(n^2) | O(1) | Small strings, educational purpose |
| Sorting | Sort both strings and compare | O(n log n) | O(n) | General use, moderate string sizes |
| HashMap / Frequency Array | Count characters in `s` and `t`, compare counts | O(n) | O(1) / O(k) | Large strings, optimal performance |

---

## Final Notes
- For most interview scenarios, the **Hash Map / Frequency Array** approach is preferred due to its linear time complexity.
- Always check length first for early termination.
- Choose data structures based on constraints (fixed charset vs. unicode).

---

**Happy Coding!**
