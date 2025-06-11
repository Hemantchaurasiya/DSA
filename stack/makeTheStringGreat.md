# Make The String Great

---

## Problem Overview
Given a string `s`, repeatedly remove **adjacent** pairs of characters that are the same letter but in **different cases** (e.g., `'a'` and `'A'`) until no more such pairs exist. The goal is to return the **"great"** string after all such removals.

**Example:**
- Input: `"leEeetcode"`
- Output: `"leetcode"`

**Explanation:** Removing `'e'` and `'E'` at positions 2 and 3 results in `"leetcode"`.

---

## Approach 1: Brute Force (Repeated Passes)

### **Core Idea**
- Continuously scan the string for adjacent pairs that are the same letter but in different cases.
- Remove such pairs.
- Repeat this process until no more such pairs exist.

### **Algorithm**
1. Convert the string into a list for easier modification.
2. Loop:
   - Scan through the list for adjacent pairs that are the same letter in different cases.
   - If found, remove them.
   - Repeat until no removals occur in a full pass.
3. Convert the list back to a string and return.

### **Java Code**
```java
public String makeGood(String s) {
    boolean removed;
    List<Character> list = new ArrayList<>();
    for (char c : s.toCharArray()) list.add(c);

    do {
        removed = false;
        for (int i = 0; i < list.size() - 1; i++) {
            if (isDifferentCaseSameLetter(list.get(i), list.get(i + 1))) {
                list.remove(i);
                list.remove(i);
                removed = true;
                break;  // restart scan after removal
            }
        }
    } while (removed);

    StringBuilder result = new StringBuilder();
    for (char c : list) result.append(c);
    return result.toString();
}

private boolean isDifferentCaseSameLetter(char a, char b) {
    return Character.toLowerCase(a) == Character.toLowerCase(b) && a != b;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n^2)* — in the worst case, each pass can remove only one pair, leading to multiple passes.
- **Space Complexity:** *O(n)* — for the list storage.

### **Dry Run**
Input: `"leEeetcode"`

- Pass 1:
  - Detect `'e'` and `'E'`, remove both.
  - String becomes `"leetcode"`.
- No more pairs to remove.
- Result: `"leetcode"`.

---

## Approach 2: Stack (Optimal)

### **Core Idea**
- Use a **stack** to process characters in linear time.
- For each character:
  - If the top of the stack is the same letter but in different case, pop it (removing the pair).
  - Else, push the current character.
- The remaining characters in the stack form the "great" string.

### **Algorithm**
1. Initialize an empty stack.
2. Iterate through each character in `s`:
   - If the stack is not empty and the top of the stack is the same letter in different case, pop from the stack.
   - Else, push the current character.
3. Convert the stack to a string and return.

### **Java Code**
```java
public String makeGood(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char c : s.toCharArray()) {
        if (!stack.isEmpty() && isDifferentCaseSameLetter(stack.peekLast(), c)) {
            stack.pollLast();
        } else {
            stack.offerLast(c);
        }
    }

    StringBuilder result = new StringBuilder();
    for (char c : stack) result.append(c);
    return result.toString();
}

private boolean isDifferentCaseSameLetter(char a, char b) {
    return Character.toLowerCase(a) == Character.toLowerCase(b) && a != b;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — each character is pushed and popped at most once.
- **Space Complexity:** *O(n)* — for the stack storage.

### **Dry Run**
Input: `"leEeetcode"`

- Process each character:
  - `'l'` -> push `'l'`.
  - `'e'` -> push `'e'`.
  - `'E'` -> top `'e'`, `'E'` is opposite case of `'e'`, pop `'e'`.
  - `'e'` -> push `'e'`.
  - `'t'` -> push `'t'`.
  - `'c'` -> push `'c'`.
  - `'o'` -> push `'o'`.
  - `'d'` -> push `'d'`.
  - `'e'` -> push `'e'`.
- Final stack: `'l', 'e', 't', 'c', 'o', 'd', 'e'` -> `"letcode"`.

---

## **Summary Table**

| Approach                                | Time Complexity | Space Complexity | Comments                                              |
|-----------------------------------------|----------------|------------------|-------------------------------------------------------|
| Naive Repeated Passes                 | O(n^2)        | O(n)             | Inefficient for large strings                        |
| Stack (Optimal)                         | O(n)          | O(n)             | Linear time, efficient, suitable for large inputs |

---

## **Final Tips for Interviews**
- The **stack approach** is the most optimal and concise.
- Focus on understanding how adjacent characters are popped when they are the same letter in different cases.
- Remember to handle case sensitivity carefully with `Character.toLowerCase()` and comparison.
- Edge cases:
  - Empty string.
  - String with all same case characters.
  - String with alternating case characters.

---

**Happy coding!**
