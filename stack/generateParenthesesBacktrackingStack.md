# Generate Parentheses

---

## Problem Overview
Given `n` pairs of parentheses, generate all possible valid combinations of parentheses.

**Example:**
- Input: `n = 3`
- Output: `["((()))", "(()())", "(())()", "()(())", "()()()"]`

---

## Approach 1: Brute Force (Backtracking + Recursion)

### **Core Idea**
- Generate all possible strings of parentheses of length `2 * n`.
- Check if each generated string is valid (balanced parentheses).
- Collect all valid strings.

### **Algorithm**
1. Generate all strings of length `2 * n` comprised of `'('` and `')'`.
   - Use recursion to build strings character by character.
2. For each generated string, verify if it is valid:
   - Use a counter to ensure open parentheses `'('` are properly matched with closing ones `')'`.
   - Increment counter for `'('`, decrement for `')'`.
   - At the end, check if the counter is zero and never negative during iteration.
3. Collect valid strings into a result list.

### **Java Code**
```java
import java.util.ArrayList;
import java.util.List;

public class GenerateParentheses {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        backtrack(result, "", 0, 0, n);
        return result;
    }

    private void backtrack(List<String> result, String current, int open, int close, int max) {
        if (current.length() == max * 2) {
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

### **Complexity Analysis**
- **Time Complexity:** *O(4^n / sqrt(n))* (Catalan number) — the number of valid combinations grows catalanically.
- **Space Complexity:** *O(n)* for recursion stack and current string storage.

---

## Approach 2: Backtracking with Pruning (Most Efficient)

### **Core Idea**
- Use backtracking to build valid parentheses sequences.
- Maintain counts of open and closed parentheses.
- Only add `'('` if open count is less than `n`.
- Only add `')'` if close count is less than open count.
- This ensures only valid sequences are generated, avoiding invalid ones.

### **Algorithm**
1. Start with an empty string.
2. Maintain two counters: `openCount` and `closeCount`.
3. At each step:
   - If `openCount < n`, add `'('` and recurse.
   - If `closeCount < openCount`, add `')'` and recurse.
4. When both counters reach `n`, add the current string to the result list.

### **Java Code**
```java
import java.util.ArrayList;
import java.util.List;

public class GenerateParentheses {
    public List<String> generateParenthesis(int n) {
        List<String> result = new ArrayList<>();
        backtrack(result, "", 0, 0, n);
        return result;
    }

    private void backtrack(List<String> result, String current, int openCount, int closeCount, int max) {
        if (current.length() == max * 2) {
            result.add(current);
            return;
        }
        if (openCount < max) {
            backtrack(result, current + "(", openCount + 1, closeCount, max);
        }
        if (closeCount < openCount) {
            backtrack(result, current + ")", openCount, closeCount + 1, max);
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(4^n / sqrt(n))* (Catalan number), as only valid sequences are generated.
- **Space Complexity:** *O(n)* for recursion stack and current string building.

---

## **Dry Run Example**

**Input:** `n = 3`

**Step-by-step (Using Approach 2):**

| Step | Current String | openCount | closeCount | Action                                            | Resulting Calls                         |
|-------|------------------|-------------|--------------|--------------------------------------------------|----------------------------------------|
| 1     | ""               | 0           | 0            | add `'('` (openCount < 3)                        | `"("`, openCount=1, closeCount=0     |
| 2     | "("              | 1           | 0            | add `'('` (openCount < 3)                        | `"(("`, openCount=2, closeCount=0    |
| 3     | "(("             | 2           | 0            | add `'('` (openCount < 3)                        | `"((("`, openCount=3, closeCount=0   |
| 4     | "((("            | 3           | 0            | add `')'` (closeCount < openCount)               | `"((()"`, openCount=3, closeCount=1 |
| 5     | "((()"           | 3           | 1            | add `')'` (closeCount < openCount)               | `"((())"`, openCount=3, closeCount=2 |
| 6     | "((())"          | 3           | 2            | add `')'` (closeCount < openCount)               | `"((()))"`, openCount=3, closeCount=3 (valid, add to result) |

Backtrack and explore other possibilities similarly to generate all sequences.

---

## **Summary Table**

| Approach                         | Time Complexity             | Space Complexity | Comments                                             |
|----------------------------------|-----------------------------|------------------|------------------------------------------------------|
| Naive Brute Force (Generate all) | O(4^n) (exponential)        | O(n)             | Inefficient due to large unnecessary generation     |
| Backtracking with Pruning       | O(4^n / sqrt(n))            | O(n)             | Efficient, only generates valid sequences          |

---

## **Final Tips for Interviews**
- Use backtracking to generate only valid sequences, avoiding invalid partial strings.
- Keep track of open and close parentheses counts.
- Prune invalid branches early to optimize.
- Think recursively; backtracking naturally fits generating combinations.
- Test with small `n` to validate correctness.

---

**Happy coding!**
