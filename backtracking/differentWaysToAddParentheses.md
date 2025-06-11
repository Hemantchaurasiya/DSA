# Different Ways to Add Parentheses

This problem involves computing all possible results from different ways of adding parentheses in a mathematical expression containing numbers and operators (`+`, `-`, `*`). It is a classic example of **divide and conquer**, **recursion**, and **memoization** techniques.

---

## 1. Brute Force Approach

### Core Idea:
Generate all possible parenthesizations of the expression and evaluate each one independently. This involves recursively splitting the expression at each operator and combining the results.

### Algorithm:
1. Parse the input string into tokens (numbers and operators).
2. For each operator in the expression:
   - Recursively compute all results for the left sub-expression.
   - Recursively compute all results for the right sub-expression.
   - Combine each result from the left with each from the right using the operator.
3. If the expression is a single number, return it as the only result.

### Recursion Tree:
```
Expression: "2*3-4*5"

                               "*"
                      /                     \
                 "2"                          "3-4*5"
                                             
    For "3-4*5":
    - Split at "-": left="3", right="4*5"
    - Compute results for "4*5"

Similarly, each operator splits the expression into smaller sub-expressions until base case (single number).
```

### Java Code:
```java
import java.util.*;

public class DifferentWaysToAddParentheses {
    public List<Integer> diffWaysToCompute(String input) {
        return compute(input);
    }

    private List<Integer> compute(String input) {
        List<Integer> results = new ArrayList<>();
        boolean isNumber = true;
        for (int i = 0; i < input.length(); i++) {
            char c = input.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                isNumber = false;
                String leftPart = input.substring(0, i);
                String rightPart = input.substring(i + 1);
                List<Integer> leftResults = compute(leftPart);
                List<Integer> rightResults = compute(rightPart);
                for (int l : leftResults) {
                    for (int r : rightResults) {
                        if (c == '+') results.add(l + r);
                        else if (c == '-') results.add(l - r);
                        else if (c == '*') results.add(l * r);
                    }
                }
            }
        }
        if (isNumber) {
            results.add(Integer.parseInt(input));
        }
        return results;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** *O(2^n)*  
  Because in the worst case, the expression can be split at each operator, leading to exponential recursive calls.
- **Space Complexity:** *O(n)*  
  Due to recursion stack and storing results.

### Dry Run:
Input: `"2*3-4*5"`

- Split at `'*'` (index 1):
  - Left: `"2"`, only one result: `[2]`
  - Right: `"3-4*5"`
    - Split at `'-'` (index 1):
      - Left: `"3"` → `[3]`
      - Right: `"4*5"`:
        - Split at `'*'` (index 1):
          - Left: `"4"` → `[4]`
          - Right: `"5"` → `[5]`
        - Results for `"4*5"`: `[4*5=20]`
    - Results for `"3-4*5"`: `[3-20 = -17]`
- Combine results for all splits.

---

## 2. Memoized (Top-Down) Approach

### Core Idea:
Use **memoization** to store results of sub-expressions to avoid recomputation, significantly reducing the exponential nature.

### Algorithm:
1. Use a HashMap to store the results for each sub-expression.
2. During recursion:
   - Check if the current sub-expression is in cache.
   - If yes, return cached results.
   - Otherwise, compute as in brute force, and store results in cache.
3. Return cached results when needed.

### Java Code:
```java
import java.util.*;

public class DifferentWaysToAddParentheses {
    private Map<String, List<Integer>> memo = new HashMap<>();

    public List<Integer> diffWaysToCompute(String input) {
        return compute(input);
    }

    private List<Integer> compute(String input) {
        if (memo.containsKey(input)) {
            return memo.get(input);
        }
        List<Integer> results = new ArrayList<>();
        boolean isNumber = true;
        for (int i = 0; i < input.length(); i++) {
            char c = input.charAt(i);
            if (c == '+' || c == '-' || c == '*') {
                isNumber = false;
                String leftPart = input.substring(0, i);
                String rightPart = input.substring(i + 1);
                List<Integer> leftResults = compute(leftPart);
                List<Integer> rightResults = compute(rightPart);
                for (int l : leftResults) {
                    for (int r : rightResults) {
                        if (c == '+') results.add(l + r);
                        else if (c == '-') results.add(l - r);
                        else if (c == '*') results.add(l * r);
                    }
                }
            }
        }
        if (isNumber) {
            results.add(Integer.parseInt(input));
        }
        memo.put(input, results);
        return results;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** *O(n^3)* in the worst case, due to overlapping subproblems and the cost of combining results.
- **Space Complexity:** *O(n^2)* for memoization storage and recursion stack.

---

## 3. Bottom-Up Dynamic Programming (Optimized)

### Core Idea:
Although less common for this problem, a bottom-up DP approach can be used to build solutions from smaller substrings to larger ones, storing intermediate results in a DP table.

### Algorithm:
1. Parse the expression into tokens.
2. Create a 2D array `dp[i][j]` to store all possible results for the sub-expression from token `i` to token `j`.
3. Fill the table for sub-expressions of increasing length:
   - For each sub-expression, split at each operator.
   - Combine results from left and right parts stored in `dp`.
4. The answer is in `dp[0][n-1]`.

### Note:
This approach is more complex to implement but follows similar logic to the recursive memoized solution.

---

## Summary

| Approach | Core Idea | Time Complexity | Space Complexity | Key Optimization |
|---|---|---|---|---|
| **Brute Force** | Recursively generate all parenthesizations | O(2^n) | O(n) | None |
| **Memoized (Top-Down)** | Cache results of sub-expressions | O(n^3) | O(n^2) | Memoization |
| **Bottom-Up DP** | Build solutions from smaller substrings | Similar to memoization | O(n^2) | Iterative approach |

---

## Final Notes:
- **Choose memoization** for optimal performance.
- **Handle base case** when the expression is just a number.
- **Split only at operators**, recursively compute results, and combine.
- **Use caching** to avoid recomputation of overlapping subproblems.

---

This comprehensive guide should help you understand, implement, and optimize the **Different Ways to Add Parentheses** problem efficiently during interviews.
