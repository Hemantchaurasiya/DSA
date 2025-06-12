# Merge Strings Alternately

---

## Problem Statement:
Given two strings `word1` and `word2`, merge them alternately character by character. If one string is longer, append the remaining characters of that string at the end.

**Example:**
- Input: `word1 = "abc"`, `word2 = "12345"`
- Output: `"a1b2c345"`

---

## Approach 1: Brute Force (Simple Iteration)

### **Core Idea:**
Iterate through both strings simultaneously, appending characters from each string in turn, until one or both strings are exhausted.

### **Algorithm:**
1. Initialize an empty `StringBuilder` to build the result.
2. Use two pointers, `i` for `word1` and `j` for `word2`.
3. Loop until both pointers reach the end of their respective strings:
   - Append `word1.charAt(i)` if `i < word1.length()`.
   - Append `word2.charAt(j)` if `j < word2.length()`.
   - Increment `i` and `j` accordingly.
4. After the loop, append remaining characters from either string if any.

### **Java Code:**
```java
public String mergeAlternately(String word1, String word2) {
    StringBuilder result = new StringBuilder();
    int i = 0, j = 0;
    while (i < word1.length() || j < word2.length()) {
        if (i < word1.length()) {
            result.append(word1.charAt(i++));
        }
        if (j < word2.length()) {
            result.append(word2.charAt(j++));
        }
    }
    return result.toString();
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(max(n, m)), where `n` and `m` are the lengths of `word1` and `word2`. Each character is processed once.
- **Space Complexity:** O(n + m) for the output string builder.

### **Dry Run:**
- Input: `word1 = "abc"`, `word2 = "12345"`
- Step-by-step:
  - `i=0`, `j=0`: Append `'a'`, `'1'`
  - `i=1`, `j=1`: Append `'b'`, `'2'`
  - `i=2`, `j=2`: Append `'c'`, `'3'`
  - `i=3`, `j=3`: `word1` exhausted, append remaining `'4'`, `'5'`
- Final string: `"a1b2c345"`

---

## Approach 2: Two-Pointer with String Builders (More Efficient, Cleaner)

### **Core Idea:**
Use two pointers to traverse each string and build the result efficiently, handling remaining characters seamlessly.

### **Algorithm:**
1. Initialize a `StringBuilder`.
2. Use a loop with index `i` for `word1` and `j` for `word2`.
3. In each iteration:
   - If `i < word1.length()`, append `word1.charAt(i++)`.
   - If `j < word2.length()`, append `word2.charAt(j++)`.
4. Continue until both strings are fully traversed.
5. Return the built string.

### **Java Code:**
```java
public String mergeAlternately(String word1, String word2) {
    StringBuilder merged = new StringBuilder();
    int i = 0, j = 0;
    while (i < word1.length() || j < word2.length()) {
        if (i < word1.length()) {
            merged.append(word1.charAt(i++));
        }
        if (j < word2.length()) {
            merged.append(word2.charAt(j++));
        }
    }
    return merged.toString();
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n + m)
- **Space Complexity:** O(n + m)

### **Dry Run:**
- Input: `word1 = "abc"`, `word2 = "12345"`
- Process:
  - Append `'a'`, `'1'`
  - Append `'b'`, `'2'`
  - Append `'c'`, `'3'`
  - `word1` exhausted, append `'4'`, `'5'`
- Result: `"a1b2c345"`

---

## Summary & Best Practices:
- This problem is straightforward; the key is to iterate through both strings simultaneously.
- Use a `StringBuilder` for efficient string concatenation.
- Handle different string lengths gracefully by checking bounds.
- This approach runs in linear time relative to the total length of the strings.

---

## Final Tips:
- Always consider edge cases where one string is empty.
- Remember that appending remaining characters after the main loop simplifies implementation.
- Practice similar string merging problems to improve string manipulation skills.

Happy coding!
