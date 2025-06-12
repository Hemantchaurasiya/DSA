# Word Pattern

This guide covers the problem of verifying if a given string follows the same pattern as a specified sequence of characters. We explore various solutions from naive to optimal, with detailed explanations, code snippets, and dry runs for effective interview preparation.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Naive Pattern Matching with String Matching**

### **Core Idea:**  
Check whether the pattern of the string `s` matches the pattern of the string `t` by trying all possible mappings. Since this approach is inefficient, it mainly serves as an initial understanding step.

### **Algorithm:**  
1. For each position `i` in `s` and `t`, compare the characters.
2. Maintain two hash maps:
   - One mapping characters in `s` to characters in `t`.
   - Another mapping characters in `t` to characters in `s`.
3. For each pair:
   - If the current character in `s` is already mapped, verify it maps to the current character in `t`.
   - If the current character in `t` is already mapped, verify it maps to the current character in `s`.
   - If not mapped, create new mappings.
4. If any conflict arises, return `false`.
5. If the entire strings are processed without conflict, return `true`.

### **Java Code:**

```java
import java.util.*;

class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (pattern.length() != words.length) return false;

        Map<Character, String> mapCharToWord = new HashMap<>();
        Map<String, Character> mapWordToChar = new HashMap<>();

        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            String word = words[i];

            if (mapCharToWord.containsKey(c) && !mapCharToWord.get(c).equals(word))
                return false;
            if (mapWordToChar.containsKey(word) && mapWordToChar.get(word) != c)
                return false;

            mapCharToWord.put(c, word);
            mapWordToChar.put(word, c);
        }
        return true;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)**, where `n` is the length of the pattern/string, as each character and word is processed once.

- **Space Complexity:**  
  **O(k)**, where `k` is the number of unique characters/words (bounded by input size).

### **Dry Run:**

Input: `pattern = "abba"`, `s = "dog cat cat dog"`

- i=0: 'a' → "dog", "dog" → 'a' maps to "dog", "dog" maps to 'a'
- i=1: 'b' → "cat", "cat" → new mapping
- i=2: 'b' → "cat", matches previous 'b' → OK
- i=3: 'a' → "dog", matches previous 'a' → OK
- Return `true`

---

## 2. Better Solution: Using Arrays for Mappings

### **Approach Name:**  
**Use Fixed-Size Arrays for Character to Word and Word to Character Mappings**

### **Core Idea:**  
Since the pattern involves characters, and the words are strings, using hash maps is flexible but can be optimized with arrays if character set size is small.

However, because words are involved, hash maps are more suitable. But for the pattern characters, arrays can be used to optimize.

### **Algorithm:**  
1. Use two hash maps:
   - Character → String
   - String → Character
2. For each position:
   - Check for existing mappings.
   - Create new mappings if none exist.
   - Return `false` if conflicts occur.
3. Return `true` if all mappings are consistent.

### **Java Code:**

```java
import java.util.*;

class Solution {
    public boolean wordPattern(String pattern, String s) {
        String[] words = s.split(" ");
        if (pattern.length() != words.length) return false;

        Map<Character, String> charToWord = new HashMap<>();
        Map<String, Character> wordToChar = new HashMap<>();

        for (int i = 0; i < pattern.length(); i++) {
            char c = pattern.charAt(i);
            String word = words[i];

            if (charToWord.containsKey(c) && !charToWord.get(c).equals(word))
                return false;
            if (wordToChar.containsKey(word) && wordToChar.get(word) != c)
                return false;

            charToWord.put(c, word);
            wordToChar.put(word, c);
        }
        return true;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)**, processing each character and word once.

- **Space Complexity:**  
  **O(k)**, for mappings, where `k` is the number of unique characters/words.

### **Dry Run:**

Input: `pattern = "abba"`, `s = "dog cat cat dog"`

- Similar to previous dry run, confirming mappings are consistent.

---

## 3. Most Optimal Solution: Pattern Hashing

### **Approach Name:**  
**Transform Strings into Pattern Codes and Compare**

### **Core Idea:**  
Convert both the pattern string and the string `s` into pattern representations. If both patterns are identical, the strings follow the same pattern.

### **Algorithm:**  
1. Define a helper function to generate a pattern code:
   - Use a hash map to assign a unique number to each new character or word.
   - Append these numbers to form a pattern string.
2. Generate pattern codes for `pattern` and `s`.
3. Compare the pattern codes:
   - If they are equal, return `true`.
   - Else, return `false`.

### **Java Code:**

```java
import java.util.*;

class Solution {
    public boolean wordPattern(String pattern, String s) {
        return getPattern(pattern).equals(getPattern(s));
    }

    private String getPattern(String str) {
        Map<String, Integer> map = new HashMap<>();
        StringBuilder patternCode = new StringBuilder();
        int index = 0;
        String[] tokens = str.split(" ");

        for (String token : tokens) {
            if (!map.containsKey(token))
                map.put(token, index++);
            patternCode.append(map.get(token)).append("#");
        }
        return patternCode.toString();
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)**, generating patterns for both strings.

- **Space Complexity:**  
  **O(k)**, for the hash map of unique tokens, where `k` is number of unique tokens.

### **Dry Run:**

Input: `pattern = "abba"`, `s = "dog cat cat dog"`

- Pattern code for `"abba"`:
  - 'a' → 0
  - 'b' → 1
  - 'b' → 1
  - 'a' → 0
  - Pattern: `"0#1#1#0#"`

- Pattern code for `"dog cat cat dog"`:
  - "dog" → 0
  - "cat" → 1
  - "cat" → 1
  - "dog" → 0
  - Pattern: `"0#1#1#0#"`

Both patterns are equal → return `true`.

---

## **Summary Table**

| Approach | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|---------------------|-------------------|--------------------|--------|
| Naive HashMaps | HashMaps for character-word mapping | O(n) | O(k) | Clear logic but less optimized |
| Arrays + HashMaps | Fixed arrays for characters, hash maps for words | O(n) | O(k) | Slight optimization |
| Pattern Hashing | Generate pattern codes and compare | O(n) | O(k) | Elegant, pattern-based comparison |

---

## **Final Tips for Interviews:**
- Use pattern hashing for concise, pattern-based verification.
- When dealing with string-to-string mapping, hash maps are most flexible.
- Always handle edge cases such as empty strings, single-character patterns, and unequal lengths.
- Practice pattern transformations for similar problems involving sequences.

---

This completes the detailed revision for **Word Pattern**. Master these approaches to confidently solve pattern matching and string transformation problems in interviews!
