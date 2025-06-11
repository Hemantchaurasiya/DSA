# Splitting a String Into Descending Consecutive Values

---

## Problem Statement
Given a string `s` consisting of digits, determine if it is possible to split the string into a sequence of **descending, consecutive integers** (each number differs by exactly 1 from the previous), with no leading zeros in any number.

**Example:**

```plaintext
Input: "050043"
Output: true
Explanation: "05" -> 5, "004" -> 4, "3" -> 3, all are descending and consecutive.
```

---

## Approach 1: Brute Force

### Core Idea
Try every possible first number and recursively check if the remaining string can form a sequence of descending, consecutive numbers.

### Algorithm
1. Loop through all possible initial numbers by choosing different substrings from the start.
2. For each initial number:
   - If it has leading zeros, skip.
   - Recursively try to verify if the remaining string can be split into a sequence where each subsequent number is exactly 1 less than the previous.
3. Use a recursive helper function to:
   - Take the previous number.
   - Check the next segment of the string for the number that is `prev - 1`.
   - Continue until the entire string is consumed successfully.

### Recursion Tree Diagram
```plaintext
Start with first number N
  |
  ├── Check next substring for N-1
  |     |
  |     ├── If matches, recurse with N-1 and remaining string
  |     └── Else, backtrack
  └── Try different first number
```

### Java Code
```java
public class Solution {
    public boolean splitString(String s) {
        int n = s.length();
        for (int i = 1; i <= n / 2; i++) {
            String firstNumStr = s.substring(0, i);
            if (firstNumStr.startsWith("0")) continue; // leading zero check
            long firstNum = Long.parseLong(firstNumStr);
            if (dfs(s.substring(i), firstNum)) {
                return true;
            }
        }
        return false;
    }

    private boolean dfs(String s, long prev) {
        if (s.isEmpty()) return true;

        for (int i = 1; i <= s.length(); i++) {
            String currStr = s.substring(0, i);
            if (currStr.startsWith("0")) continue; // leading zero check
            long currNum = Long.parseLong(currStr);
            if (currNum == prev - 1) {
                if (dfs(s.substring(i), currNum)) return true;
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N^3)  
  For each possible starting number, recursively checking subsequent substrings involves nested loops and substring operations, leading to cubic complexity in the worst case.
- **Space Complexity:** O(N)  
  Due to recursion stack and substring operations.

### Dry Run Example
- Input: `"050043"`
- First number options: `"0"`, `"05"` (skip `"05"` due to leading zero), `"050"`, etc.
- Check `"0"`:
  - Next substring: `"50043"`; try to find `-1` (which is `-1`), no match.
- Check `"05"` (skip due to leading zero).
- Check `"050"` (skip).
- Check `"0500"` (skip).
- Next, choose `"5"` as starting point:
  - Remaining: `"0043"`.
  - Leading zero? Yes, skip.
- Eventually, find sequence `"5" -> "4" -> "3"` if substrings match.

---

## Approach 2: Improved Backtracking (with Pruning)

### Core Idea
Reduce unnecessary checks by:
- Avoiding substrings with leading zeros.
- Early pruning when the next number doesn't match the expected sequence.
- Limit the search space by considering only valid starting points.

### Algorithm
- Similar to brute-force but:
  - Generate the first number.
  - Recurse only if the remaining string can potentially form the sequence.
- Use memoization if needed (not critical here).

### Java Code
The code remains similar but with some pruning:
```java
// Same as above, with comments emphasizing pruning
```

### Complexity Analysis
- Same as brute-force, but pruning can improve average case performance.

---

## Approach 3: Most Optimal Solution — String Manipulation + Pattern Matching

### Core Idea
Instead of trying all substrings blindly:
- Use the property that the sequence is strictly descending and each number differs by exactly 1.
- For each candidate starting number:
  - Generate the next expected number by subtracting 1.
  - Check if the remaining string starts with this number.
  - Repeat until the string is exhausted or mismatch occurs.

### Algorithm
1. Loop over possible first numbers:
   - For each, generate the next expected number.
2. Iteratively check if the remaining string matches the expected number:
   - Use `startsWith()` for efficient matching.
   - Remove the matched prefix.
   - Update expected number.
3. If the entire string is matched successfully, return true.

### Java Code
```java
public class Solution {
    public boolean splitString(String s) {
        int n = s.length();
        for (int i = 1; i <= n / 2; i++) {
            String firstNumStr = s.substring(0, i);
            if (firstNumStr.startsWith("0")) continue; // leading zero check
            long firstNum = Long.parseLong(firstNumStr);
            long prevNum = firstNum;
            String remaining = s.substring(i);
            boolean valid = true;

            while (!remaining.isEmpty()) {
                long nextNum = prevNum - 1;
                String nextStr = String.valueOf(nextNum);
                if (remaining.startsWith(nextStr)) {
                    remaining = remaining.substring(nextStr.length());
                    prevNum = nextNum;
                } else {
                    valid = false;
                    break;
                }
            }

            if (valid) return true;
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N²)  
  For each starting number, in the worst case, we compare substrings with decreasing length. Since each check is O(length of remaining string), total is quadratic.
- **Space Complexity:** O(1) or O(N) due to string slicing, but generally constant extra space.

### Dry Run Example
- Input: `"050043"`
- Starting with `"0"`:
  - Next expected: `-1` → `" -1"` (no match, move on)
- Starting with `"05"` (skip leading zero)
- Starting with `"050"` (skip)
- Starting with `"5"`:
  - remaining: `"0043"`
  - next expected: `4`
  - does `"0043"` start with `"4"`? No, because `"0043"` starts with `"0"`.
- Starting with `"50"`:
  - remaining: `"043"`, expected: `49`
  - `"043"` does not start with `"49"`.
- Starting with `"50"` (skip leading zero)
- Starting with `"050"` (skip)
- Next, `5`:
  - remaining: `"0043"`; check `"4"`? No.
- **No valid sequence found**; return false unless the sequence is explicitly valid.

---

## Summary

| Aspect | Brute Force | Improved Backtracking | Efficient String Matching |
|---------|--------------|------------------------|--------------------------|
| **Core Idea** | Try all possible first splits | Prune early based on pattern | Generate sequence iteratively with pattern matching |
| **Time Complexity** | O(N^3) | O(N^3) | O(N^2) |
| **Space Complexity** | O(N) | O(N) | O(1) |

---

## Final Tips for Interview
- Always handle leading zeros.
- Consider edge cases: empty string, single digit, large numbers.
- Use string operations efficiently (`startsWith`, substring).
- Think about early pruning to reduce unnecessary recursion.

---

This comprehensive guide should help you understand multiple approaches and optimize your solution for the problem **Splitting a String Into Descending Consecutive Values**.
