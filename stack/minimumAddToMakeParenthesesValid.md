# Minimum Add to Make Parentheses Valid

---

## Problem Overview
Given a string `S` consisting of `'('` and `')'`, determine the minimum number of parentheses that need to be added to make the string **valid** (balanced). A string is valid if:
- Every opening parenthesis `'('` has a corresponding closing parenthesis `')'`.
- The parentheses are properly matched and ordered.

---

## Approach 1: Brute Force (Counting Unmatched Parentheses)

### Core Idea
Count how many parentheses are unmatched as we traverse the string:
- For each `'('`, increment a counter.
- For each `')'`, if there's an unmatched `'('`, match it; otherwise, count it as needing an addition.

### Algorithm
1. Initialize two counters:
   - `openCount` for unmatched `'('`.
   - `closeCount` for unmatched `')'`.
2. Loop through each character in the string:
   - If `'('`, increment `openCount`.
   - If `')'`:
     - If `openCount > 0`, decrement `openCount` (match found).
     - Else, increment `closeCount` (unmatched `')'`).
3. The total number of parentheses to add = `openCount + closeCount`.

### Java Code
```java
public int minAddToMakeValid(String s) {
    int openCount = 0;
    int closeCount = 0;
    
    for (char c : s.toCharArray()) {
        if (c == '(') {
            openCount++;
        } else if (c == ')') {
            if (openCount > 0) {
                openCount--;
            } else {
                closeCount++;
            }
        }
    }
    return openCount + closeCount;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*, where `n` is the length of the string, as each character is processed once.
- **Space Complexity:** *O(1)*, as only a few counters are used.

### Dry Run
Input: `")(()"` 

| Step | Char | openCount | closeCount | Explanation                          |
|-------|--------|-----------|------------|--------------------------------------|
| 1     | `')'`  | 0         | 1          | No open parenthesis to match, increment closeCount |
| 2     | `'('`  | 1         | 1          | Increment openCount                |
| 3     | `'('`  | 2         | 1          | Increment openCount                |
| 4     | `')'`  | 1         | 1          | Match with previous `'('`, decrement openCount |

Remaining unmatched `'('` = 1, unmatched `')'` = 1, total = 2.

---

## Approach 2: Using Stack (Optional, Less Efficient)

### Core Idea
Use a stack to match parentheses:
- Push `'('`.
- When encountering `')'`, pop `'('` if available.
- Count the unmatched parentheses based on remaining stack elements and unmatched `')'`.

**Note:** This approach is less optimal for just counting needed additions but provides conceptual clarity.

---

## Approach 3: Optimized Counting (Most Efficient)

### Core Idea
Count the minimum number of parentheses to add by tracking unmatched `'('` and unmatched `')'` as we iterate once.

### Algorithm
- Initialize `balance` to 0 (tracks unmatched `'('`).
- Initialize `count` to 0 (total parentheses needed to add).
- Traverse the string:
  - For `'('`, increment `balance`.
  - For `')'`:
    - If `balance > 0`, decrement `balance` (match found).
    - Else, increment `count` (we need an `'('` to match this `')'`).

- At the end, `balance` indicates how many `'('` are unmatched, hence need closing parentheses.

- Total parentheses to add = `count + balance`.

### Java Code
```java
public int minAddToMakeValid(String s) {
    int balance = 0; // Tracks unmatched '('
    int count = 0;   // Tracks unmatched ')'
    
    for (char c : s.toCharArray()) {
        if (c == '(') {
            balance++;
        } else {
            if (balance > 0) {
                balance--;
            } else {
                count++;
            }
        }
    }
    return count + balance;
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*, single pass through string.
- **Space Complexity:** *O(1)*.

---

## Summary Table

| Approach                       | Time Complexity | Space Complexity | Comments                                               |
|--------------------------------|-----------------|------------------|--------------------------------------------------------|
| Brute Force Counting           | O(n)            | O(1)             | Simple counters, efficient for large inputs         |
| Stack-based (less optimal)     | O(n)            | O(n) (stack)     | Conceptually clear but more overhead                   |
| Counting with counters (Optimal)| O(n)            | O(1)             | Most concise, efficient, and preferred for interviews |

---

## Final Tips for Interviews
- Focus on the single-pass counting approach for optimal solutions.
- Maintain clarity: use descriptive variable names.
- Understand edge cases: empty string, all parentheses are already balanced, strings with only one parenthesis, etc.
- Remember that the problem is about **minimum** additions — so counting unmatched parentheses is key.

---

**Happy coding!**
