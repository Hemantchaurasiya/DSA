# Isomorphic Strings

This comprehensive guide covers the problem of determining whether two strings are **isomorphic**, meaning characters in one string can be replaced to get the other string, maintaining the order and no two characters can map to the same character unless they are the same in the original string. We will explore various approaches from naive to optimal, including detailed explanations, code, and dry runs.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Naive Character Mapping Check**

### **Core Idea:**  
For each character in `s`, try to map it to the corresponding character in `t`. Maintain a mapping and ensure no conflicts. Since this is straightforward, but can be inefficient in naive implementations, it highlights the importance of proper data structures.

### **Algorithm:**  
1. Initialize two hash maps:
   - `mapST` to store character mappings from `s` to `t`.
   - `mapTS` to store character mappings from `t` to `s` (to ensure one-to-one mapping).
2. Loop through each index `i`:
   - If `s.charAt(i)` is already mapped:
     - Check if it maps to `t.charAt(i)`; if not, return `false`.
   - If `t.charAt(i)` is already mapped:
     - Check if it maps from `s.charAt(i)`; if not, return `false`.
   - Else, create a new mapping in both maps.
3. If all characters follow consistent mapping, return `true`.

### **Java Code:**

```java
import java.util.*;

class Solution {
    public boolean isIsomorphic(String s, String t) {
        if (s.length() != t.length()) return false;

        Map<Character, Character> mapST = new HashMap<>();
        Map<Character, Character> mapTS = new HashMap<>();

        for (int i = 0; i < s.length(); i++) {
            char c1 = s.charAt(i);
            char c2 = t.charAt(i);

            if (mapST.containsKey(c1) && mapST.get(c1) != c2)
                return false;

            if (mapTS.containsKey(c2) && mapTS.get(c2) != c1)
                return false;

            mapST.put(c1, c2);
            mapTS.put(c2, c1);
        }
        return true;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)**, where `n` is the length of the strings, since each character is processed once.

- **Space Complexity:**  
  **O(1)** or **O(k)**, where `k` is the size of the character set (constant for ASCII). Two hash maps are used.

### **Dry Run:**

Input: `"egg"` and `"add"`

- i=0: 'e'→'a' in mapST, 'a'→'e' in mapTS
- i=1: 'g'→'d' in mapST, 'd'→'g' in mapTS
- i=2: 'g' already mapped to 'd', matches current 'd' → OK
- Return `true`

---

## 2. Slightly Better Solution: Using Arrays for Mappings

### **Approach Name:**  
**Use Fixed-size Arrays for Character Mappings**

### **Core Idea:**  
Since characters are limited (e.g., ASCII), use arrays instead of hash maps for faster access.

### **Algorithm:**  
1. Create two integer arrays of size 256 (for ASCII), initialize with `-1`.
2. Loop through each index `i`:
   - If `arrS[s.charAt(i)]` is `-1`, assign `arrS[s.charAt(i)] = t.charAt(i)`.
   - Else, check if `arrS[s.charAt(i)]` equals `t.charAt(i)`; if not, return `false`.
   - Repeat similarly for the reverse mapping.
3. Return `true` if all checks pass.

### **Java Code:**

```java
class Solution {
    public boolean isIsomorphic(String s, String t) {
        if (s.length() != t.length()) return false;
        int[] arrS = new int[256];
        int[] arrT = new int[256];
        Arrays.fill(arrS, -1);
        Arrays.fill(arrT, -1);

        for (int i = 0; i < s.length(); i++) {
            int c1 = s.charAt(i);
            int c2 = t.charAt(i);

            if (arrS[c1] == -1 && arrT[c2] == -1) {
                arrS[c1] = c2;
                arrT[c2] = c1;
            } else {
                if (arrS[c1] != c2 || arrT[c2] != c1)
                    return false;
            }
        }
        return true;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)**, processing each character once.

- **Space Complexity:**  
  **O(1)**, fixed size arrays for ASCII.

### **Dry Run:**

Input: `"foo"` and `"bar"`

- i=0: 'f'→'b' in arrS, arrT
- i=1: 'o'→'a' in arrS, arrT
- i=2: 'o' mapped to 'a', current char 'r' ≠ 'a' → return false

---

## 3. Most Optimal Solution: Using Pattern Hashing

### **Approach Name:**  
**Map Strings to Pattern Codes and Compare**

### **Core Idea:**  
Transform both strings into their pattern representations and compare these patterns. If both patterns are identical, the strings are isomorphic.

### **Algorithm:**  
1. Define a helper function to generate a pattern string:
   - Use a hash map to map each character to a number (or index).
   - For each character, if not mapped, assign a new number.
   - Append the number to a pattern string.
2. Generate pattern strings for both `s` and `t`.
3. If pattern strings are equal, return `true`; else, `false`.

### **Java Code:**

```java
import java.util.*;

class Solution {
    public boolean isIsomorphic(String s, String t) {
        return getPattern(s).equals(getPattern(t));
    }

    private String getPattern(String str) {
        Map<Character, Integer> map = new HashMap<>();
        StringBuilder pattern = new StringBuilder();
        int index = 0;
        for (char c : str.toCharArray()) {
            if (!map.containsKey(c))
                map.put(c, index++);
            pattern.append(map.get(c)).append("#");
        }
        return pattern.toString();
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** for pattern generation per string, total O(2n) = O(n).

- **Space Complexity:**  
  **O(k)** for the hash maps where `k` is unique characters, which is small.

### **Dry Run:**

Input: `"egg"` and `"add"`

- Pattern for `"egg"`:
  - 'e' → 0
  - 'g' → 1
  - 'g' → 1
  - Pattern: `"0#1#1#"`

- Pattern for `"add"`:
  - 'a' → 0
  - 'd' → 1
  - 'd' → 1
  - Pattern: `"0#1#1#"`

Since both patterns are equal, return `true`.

---

## **Summary Table**

| Approach | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|---------------------|-------------------|--------------------|--------|
| Naive Character Map | HashMaps | O(n) | O(1) | Straightforward, clear logic |
| Arrays for Mappings | Fixed size arrays | O(n) | O(1) | Faster for ASCII characters |
| Pattern Hashing | HashMaps + StringBuilder | O(n) | O(k) | Elegant, concise, pattern-based |

---

## **Final Tips for Interviews:**
- Use pattern hashing to quickly compare string structures.
- When character sets are limited, arrays improve speed.
- Always ensure one-to-one mapping, not just one-to-many.
- Practice edge cases: strings of different lengths, empty strings, repeated characters.

---

This completes the detailed revision for **Isomorphic Strings**. Master these approaches to confidently handle similar pattern and mapping problems!
