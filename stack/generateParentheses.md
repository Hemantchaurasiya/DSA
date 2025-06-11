# Generate Parentheses

---

## Problem Overview
Given an integer `n`, generate all combinations of well-formed parentheses consisting of `n` pairs of parentheses.

**Example:**
- Input: `n = 3`
- Output: `["((()))", "(()())", "(())()", "()(())", "()()()"]`

---

## Approach 1: Brute Force (Generate All and Filter)

### **Core Idea**
Generate all possible sequences of `'('` and `')'` of length `2n`, then filter out only those that are valid parentheses strings.

### **Algorithm**
1. Generate all strings of length `2n` consisting of `'('` and `')'`.
2. For each generated string:
   - Check if it is valid (using a separate validation function).
3. Collect all valid strings into the result list.

### **Java Code**
```java
import java.util.*;

public List<String> generateParenthesis(int n) {
    List<String> result = new ArrayList<>();
    generateAll(new char[2 * n], 0, result);
    return result;
}

private void generateAll(char[] current, int pos, List<String> result) {
    if (pos == current.length) {
        if (isValid(current)) {
            result.add(new String(current));
        }
        return;
    }
    current[pos] = '(';
    generateAll(current, pos + 1, result);
    current[pos] = ')';
    generateAll(current, pos + 1, result);
}

private boolean isValid(char[] current) {
    int balance = 0;
    for (char c : current) {
        if (c == '(') balance++;
        else balance--;
        if (balance < 0) return false;
    }
    return balance == 0;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(2^(2n) * n)* because generating all sequences is `2^(2n)` and validating each takes `O(n)`.
- **Space Complexity:** *O(2n)* for the recursion call stack and temporary storage.

### **Dry Run**
For `n=2`, generate all sequences of length 4:
- `"((<<"`, `"(())"`, `"()()"`, `"())"`, etc.
- Validate each to find valid strings like `"(())," "())("`, etc.
- Only keep the valid ones.

---

## Approach 2: Backtracking (Most Efficient and Common)

### **Core Idea**
Build valid parentheses combinations using recursion:
- At each step, decide whether to add `'('` or `')'`.
- Only add `'('` if we haven't used all `n` left parentheses.
- Only add `')'` if it won't lead to an invalid sequence (i.e., if there are unmatched `'('`).

### **Algorithm**
1. Maintain two counters:
   - `openCount` for number of `'('` used.
   - `closeCount` for number of `')'` used.
2. Recursively build the sequence:
   - If `openCount < n`, add `'('`.
   - If `closeCount < openCount`, add `')'`.
3. When both `openCount` and `closeCount` reach `n`, add the sequence to the result.

### **Java Code**
```java
public List<String> generateParenthesis(int n) {
    List<String> result = new ArrayList<>();
    backtrack(result, new StringBuilder(), 0, 0, n);
    return result;
}

private void backtrack(List<String> result, StringBuilder current, int open, int close, int n) {
    if (current.length() == 2 * n) {
        result.add(current.toString());
        return;
    }
    if (open < n) {
        current.append('(');
        backtrack(result, current, open + 1, close, n);
        current.deleteCharAt(current.length() - 1);
    }
    if (close < open) {
        current.append(')');
        backtrack(result, current, open, close + 1, n);
        current.deleteCharAt(current.length() - 1);
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(4^n / √n)*, which is the catalan number, representing the total number of valid combinations.
- **Space Complexity:** *O(n)* for the recursion stack and string builder.

### **Dry Run**
Input: `n=3`

- Start with empty string `""`.
- Add `'('` (open=1, close=0).
- Recursively add `'('` (open=2, close=0).
- Recursively add `'('` (open=3, close=0).
- Now can't add `'('` (since open==n). Add `')'` (open=3, close=1).
- Continue adding `')'` until sequences are valid:
  - `"((("` → `"((()"` → `"((()))"`.
  - Backtrack and explore other options to generate all combinations.

---

## **Summary Table**

| Approach                  | Time Complexity                     | Space Complexity | Comments                                              |
|---------------------------|-------------------------------------|------------------|--------------------------------------------------------|
| Brute Force               | O(2^(2n) * n)                       | O(2n)            | Generate all sequences, inefficient for large n     |
| Backtracking (Optimal)     | O(4^n / √n) (Catalan number)        | O(n)             | Efficient, widely used, avoids generating invalid sequences |

---

## **Final Tips for Interviews**
- Use backtracking for the most efficient and elegant solution.
- Ensure to prune invalid sequences early by maintaining counts of `'('` and `')'`.
- Practice generating all valid combinations for small `n` to understand the pattern.
- Remember the relation to Catalan numbers for the total number of valid parenthesis combinations.

---

**Happy coding!**
