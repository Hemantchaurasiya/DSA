# Valid Parentheses

## Problem Overview
Given a string containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['`, and `']'`, determine if the input string is **valid**. An input string is valid if:
- Open brackets are closed by the same type of brackets.
- Open brackets are closed in the correct order.
- Every closing bracket has a corresponding open bracket before it.

---

## Approach 1: Brute Force (Naive Check)

### Core Idea
Check pairs of brackets repeatedly until no more replacements can be made. If all brackets are removed, the string is valid.

### Algorithm
1. Loop through the string.
2. Repeatedly replace all instances of `"()"`, `"{}"`, and `"[]"` with an empty string.
3. Continue until no replacements are made in a full pass.
4. If the resulting string is empty, return `true`. Otherwise, `false`.

### Java Code
```java
public boolean isValid(String s) {
    String prev;
    do {
        prev = s;
        s = s.replace("()", "").replace("{}", "").replace("[]", "");
    } while (!s.equals(prev));
    return s.isEmpty();
}
```

### Complexity Analysis
- **Time Complexity:** *O(n³)* in the worst case, because each replace operation can take *O(n)* time and may be repeated *O(n)* times.
- **Space Complexity:** *O(n)* for string operations in Java.

### Dry Run
Input: `"([{}])"`

| Step | String             | Action                     | Result          |
|-------|---------------------|----------------------------|-----------------|
| 1     | `"([{}])"`          | Replace `"()"` — no effect  | `"([{}])"`      |
|       |                     | Replace `"{}"` — `"([{}])"` → `"([])"` | `"([])"`    |
| 2     | `"([])"`            | Replace `"()"` — `"([])"` → `"[]"` | `"[]"`      |
| 3     | `"[]"`              | Replace `"[]"` → `""` | `""`            |
| Final | `""`                | String empty, valid | **Return `true`** |

---

## Approach 2: Using a Stack (Better Solution)

### Core Idea
Use a stack to match opening and closing brackets in order:
- Push opening brackets onto the stack.
- When encountering a closing bracket, check if it matches the top of the stack.
- If it matches, pop from the stack; else, invalid.
- At the end, if the stack is empty, the string is valid.

### Algorithm
1. Initialize an empty stack.
2. Traverse the string:
   - If the character is an opening bracket, push it onto the stack.
   - If it is a closing bracket, check if the stack is empty:
     - If empty, invalid (return false).
     - Else, pop the top element and verify if it matches the closing bracket.
3. After processing all characters, if the stack is empty, return `true`; otherwise, `false`.

### Java Code
```java
import java.util.Stack;

public boolean isValid(String s) {
    Stack<Character> stack = new Stack<>();
    for (char c : s.toCharArray()) {
        if (c == '(' || c == '{' || c == '[') {
            stack.push(c);
        } else {
            if (stack.isEmpty()) return false;
            char top = stack.pop();
            if ((c == ')' && top != '(') ||
                (c == '}' && top != '{') ||
                (c == ']' && top != '[')) {
                return false;
            }
        }
    }
    return stack.isEmpty();
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*, since each character is processed once.
- **Space Complexity:** *O(n)* in the worst case for the stack (e.g., all opening brackets).

### Dry Run
Input: `"([{}])"`

| Step | Character | Stack             | Action                                              | Stack after action |
|-------|------------|-------------------|-----------------------------------------------------|--------------------|
| 1     | `'('`      | `['(']`          | Push `'('`                                          | `['(']`            |
| 2     | `'['`      | `['(', '[']`      | Push `'['`                                          | `['(', '[']`      |
| 3     | `'{'`      | `['(', '[', '{']` | Push `'{'`                                          | `['(', '[', '{']` |
| 4     | `'}'`      | `['(', '[', '{']` | Pop `'{'`, check match `'{'` with `'}'` → match    | `['(', '[']`      |
| 5     | `']'`      | `['(', '[']`      | Pop `'['`, check match `'['` with `']'` → match    | `['(']`          |
| 6     | `')'`      | `['(']`           | Pop `'('`, check match `'('` with `')'` → match    | `[]`             |
| End   |            | Empty             | Stack empty → valid                               | **Return `true`** |

---

## Approach 3: Optimized Solution (Using Stack with HashMap)

### Core Idea
Enhance the stack approach using a HashMap to store matching pairs for quick lookup.

### Algorithm
1. Create a HashMap with closing brackets as keys and their matching opening brackets as values.
2. Use a stack to process characters:
   - Push opening brackets.
   - When encountering a closing bracket, check if the top of the stack matches the corresponding opening bracket using the HashMap.
3. If mismatched or stack empty when closing bracket appears, return false.
4. If after complete traversal, stack is empty, return true.

### Java Code
```java
import java.util.HashMap;
import java.util.Stack;

public boolean isValid(String s) {
    HashMap<Character, Character> map = new HashMap<>();
    map.put(')', '(');
    map.put('}', '{');
    map.put(']', '[');
    
    Stack<Character> stack = new Stack<>();
    
    for (char c : s.toCharArray()) {
        if (map.containsKey(c)) {
            if (stack.isEmpty() || stack.pop() != map.get(c)) {
                return false;
            }
        } else {
            stack.push(c);
        }
    }
    return stack.isEmpty();
}
```

### Complexity Analysis
- **Time Complexity:** *O(n)*, processing each character once.
- **Space Complexity:** *O(n)* for the stack and HashMap.

---

## Summary Table

| Approach                   | Time Complexity | Space Complexity | Notes                                              |
|----------------------------|-----------------|------------------|----------------------------------------------------|
| Brute Force                | O(n³)           | O(n)             | Not efficient, suitable for small inputs          |
| Stack (Basic)              | O(n)            | O(n)             | Efficient, straightforward implementation          |
| Stack + HashMap (Optimized) | O(n)            | O(n)             | Most readable, scalable for large inputs        |

---

## Final Tips for Interviews
- Always validate edge cases: empty string, strings with only one character, invalid sequences.
- Use stacks for order-sensitive matching problems.
- HashMaps can simplify matching logic and improve readability.
- Focus on clean, readable code with proper variable naming.

---

**Happy Coding!**
