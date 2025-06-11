# Generate Parentheses

---

## Problem Overview
Given **n**, generate all combinations of **well-formed parentheses** of length `2 * n`. For example, for `n=3`, the output is:
```
["((()))", "(()())", "(())()", "()(())", "()()()"]
```

---

## Approach 1: Brute Force

### Core Idea
Generate all possible sequences of parentheses of length `2 * n` and filter out the invalid ones. This approach explores the entire search space of size `2^(2n)`.

### Algorithm
1. Generate all sequences of '(' and ')' of length `2 * n`.
2. For each sequence, check if it is valid:
   - Initialize a counter to 0.
   - For each character:
     - Increment counter for '('.
     - Decrement counter for ')'.
     - If at any point counter < 0, sequence is invalid.
   - After processing all characters, if counter == 0, sequence is valid.
3. Collect all valid sequences.

### Java Code
```java
import java.util.*;

public class GenerateParentheses {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        generateAll(new char[2 * n], 0, result);
        return result;
    }

    private void generateAll(char[] current, int pos, List<String> result) {
        if (pos == current.length) {
            if (isValid(current))
                result.add(new String(current));
            return;
        }

        current[pos] = '(';
        generateAll(current, pos + 1, result);
        current[pos] = ')';
        generateAll(current, pos + 1, result);
    }

    private boolean isValid(char[] sequence) {
        int count = 0;
        for (char c : sequence) {
            if (c == '(') count++;
            else count--;
            if (count < 0) return false;
        }
        return count == 0;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  *O(2^{2n} * n)*, as generating all sequences (2^{2n}) and checking validity (O(n)).

- **Space Complexity:**  
  *O(2n)* for the recursion stack and sequence storage.

### Dry Run (n=2)
Sequence generation: all sequences of length 4 (`(())`, `()()`, etc.), filtering invalid ones.

---

## Approach 2: Backtracking (Better Solution)

### Core Idea
Build valid parentheses strings **incrementally**, ensuring at each step that:
- The number of '(' added is at most `n`.
- The number of ')' added is at most the number of '(' added.

This pruning reduces the search space significantly.

### Algorithm
1. Use recursion with parameters:
   - The current string being built.
   - The count of '(' used.
   - The count of ')' used.

2. At each step:
   - If `open < n`, add '(' and recurse.
   - If `close < open`, add ')' and recurse.
3. When the length reaches `2 * n`, add the current string to the result.

### Java Code
```java
public class GenerateParentheses {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        backtrack(result, "", 0, 0, n);
        return result;
    }

    private void backtrack(List<String> result, String current, int open, int close, int max) {
        if (current.length() == 2 * max) {
            result.add(current);
            return;
        }

        if (open < max) {
            backtrack(result, current + "(", open + 1, close, max);
        }
        if (close < open) {
            backtrack(result, current + ")", open, close + 1, max);
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  *O(4^n / √n)*, which is the nth Catalan number, as this generates all valid combinations without exploring invalid sequences.

- **Space Complexity:**  
  *O(n)* for the recursion stack and current string building.

### Dry Run (n=2)
- Start with `""`, open=0, close=0.
- Add `'('` (open=1, close=0).
- Add `'('` (open=2, close=0).
- Close one `'('` (open=2, close=1).
- Close last `'('` (open=2, close=2), add `" (())"`.
- Backtrack and add `'()('`, then close accordingly.
- Collect all valid sequences: `" (())"`, `"()()"`.

---

## Approach 3: Optimized Backtracking with StringBuilder

### Core Idea
Use `StringBuilder` for efficient string concatenation during recursion, reducing overhead.

### Algorithm
Same as Approach 2, but utilize `StringBuilder` to append/remove characters during backtracking.

### Java Code
```java
public class GenerateParentheses {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        StringBuilder current = new StringBuilder();
        backtrack(result, current, 0, 0, n);
        return result;
    }

    private void backtrack(List<String> result, StringBuilder current, int open, int close, int max) {
        if (current.length() == 2 * max) {
            result.add(current.toString());
            return;
        }

        if (open < max) {
            current.append('(');
            backtrack(result, current, open + 1, close, max);
            current.deleteCharAt(current.length() - 1); // backtrack
        }
        if (close < open) {
            current.append(')');
            backtrack(result, current, open, close + 1, max);
            current.deleteCharAt(current.length() - 1); // backtrack
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** Same as Approach 2, *O(4^n / √n)*.
- **Space Complexity:** *O(n)* for recursion stack and `StringBuilder`.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|-------------------------|-------------------------|--------|
| Brute Force | Generate all sequences, then filter valid | O(2^{2n} * n) | O(2n) | Inefficient for larger `n`. |
| Backtracking | Incrementally build valid sequences, pruning invalid paths | O(4^n / √n) | O(n) | Most common efficient approach. |
| Backtracking + StringBuilder | Same as above, with optimized string concatenation | O(4^n / √n) | O(n) | Slight performance benefits. |

---

## Final Tips for Interviews
- Use **backtracking** for cleaner, efficient solutions.
- Always **prune** invalid branches early.
- Keep track of the number of '(' and ')' used.
- Use `StringBuilder` for performance optimization if necessary.
- Understand the **Catalan number** relation for the count of valid parentheses.

---

This comprehensive guide should serve as a quick revision and an in-depth understanding of various approaches to **Generate Parentheses**.
