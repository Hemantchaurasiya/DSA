# Decode String

---

## Problem Overview
Given an encoded string, return its decoded form. The encoding rule is: `k[encoded_string]`, where the `encoded_string` inside the brackets is repeated `k` times. The input string contains digits, alphabets, and brackets.

**Example:**
- Input: `"3[a2[c]]"`
- Output: `"accaccacc"`

**Explanation:**
- `a2[c]` becomes `acc`.
- `3[acc]` becomes `accaccacc`.

---

## Approach 1: Brute Force (Using Stack and Recursion)

### **Core Idea**
- Use a stack to handle nested brackets.
- When encountering a `'['`, push the current string and number onto the stack.
- When encountering a `']'`, pop from the stack to get the previous string and number, then decode the current segment accordingly.
- Build the decoded string step-by-step.

### **Algorithm**
1. Initialize a stack to store pairs of strings and numbers.
2. Maintain a current string `currStr` and current number `currNum`.
3. Iterate through each character in the input:
   - If the character is a digit, update `currNum`.
   - If the character is a letter, append it to `currStr`.
   - If `'['`:
     - Push `(currStr, currNum)` onto the stack.
     - Reset `currStr` and `currNum` for new segment.
   - If `']'`:
     - Pop `(prevStr, num)` from stack.
     - Update `currStr = prevStr + currStr repeated `num` times`.
4. After processing all characters, `currStr` contains the decoded string.

### **Java Code**
```java
import java.util.Stack;

public class DecodeString {
    public String decodeString(String s) {
        Stack<String> strStack = new Stack<>();
        Stack<Integer> numStack = new Stack<>();
        StringBuilder currStr = new StringBuilder();
        int currNum = 0;

        for (char c : s.toCharArray()) {
            if (Character.isDigit(c)) {
                currNum = currNum * 10 + (c - '0');
            } else if (c == '[') {
                strStack.push(currStr.toString());
                numStack.push(currNum);
                currStr = new StringBuilder();
                currNum = 0;
            } else if (c == ']') {
                String prevStr = strStack.pop();
                int repeatCount = numStack.pop();
                StringBuilder temp = new StringBuilder(prevStr);
                for (int i = 0; i < repeatCount; i++) {
                    temp.append(currStr);
                }
                currStr = temp;
            } else {
                currStr.append(c);
            }
        }
        return currStr.toString();
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — each character is processed once.
- **Space Complexity:** *O(n)* — stack space for nested structures.

### **Dry Run**
Input: `"3[a2[c]]"`

| Step | Char | Stack Content | `currStr` | `currNum` | Explanation                                |
|-------|-------|----------------|-----------|-----------|--------------------------------------------|
| 1     | '3'   |                | ""        | 3         | Digit read, `currNum=3`                   |
| 2     | '['   | push("",3)     | ""        | 0         | Push current string & number, reset      |
| 3     | 'a'   |                | "a"       | 0         | Append 'a'                                |
| 4     | '2'   |                | "a"       | 2         | Digit read                                |
| 5     | '['   | push("a",2)    | ""        | 0         | Push "a" and 2, reset                    |
| 6     | 'c'   |                | "c"       | 0         | Append 'c'                                |
| 7     | ']'   | pop("a",2)     | "a" + "c" * 2 = "acc" | | Decode inner, update `currStr`             |
| 8     | ']'   | pop("",3)      | "acc" * 3 = "accaccacc" | | Decode outer, final string               |

Result: `"accaccacc"`.

---

## Approach 2: Recursive Parsing with Helper Function

### **Core Idea**
- Use recursion to handle nested brackets.
- Parse the string character by character.
- When encountering `'['`, recursively decode the substring.
- When encountering `']'`, return the decoded substring to the caller.
- Concatenate decoded parts as recursion unwinds.

### **Algorithm**
1. Use an index pointer passed by reference or class variable.
2. Define a helper function:
   - Initialize a `StringBuilder` for current segment.
   - Initialize a number `k` for repeat counts.
   - Loop through the characters:
     - If digit, build `k`.
     - If letter, append to `StringBuilder`.
     - If `'['`, recursively decode, then append the result `k` times.
     - If `']'`, break and return the current decoded string.
3. Call the helper function from the main function and return the result.

### **Java Code**
```java
public class DecodeString {
    private int index = 0;

    public String decodeString(String s) {
        return helper(s);
    }

    private String helper(String s) {
        StringBuilder result = new StringBuilder();
        int k = 0;

        while (index < s.length()) {
            char c = s.charAt(index);
            if (Character.isDigit(c)) {
                k = k * 10 + (c - '0');
                index++;
            } else if (c == '[') {
                index++; // skip '['
                String decodedString = helper(s);
                for (int i = 0; i < k; i++) {
                    result.append(decodedString);
                }
                k = 0;
            } else if (c == ']') {
                index++; // skip ']'
                break;
            } else {
                result.append(c);
                index++;
            }
        }
        return result.toString();
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — each character processed once.
- **Space Complexity:** *O(n)* — recursion stack and string builder.

### **Dry Run**
Input: `"3[a2[c]]"`

- Call `helper(s)`:
  - Read `'3'`: `k=3`.
  - Read `'['`: recurse.
    - Inside recursion:
      - `'a'`: append `'a'`.
      - `'2'`: `k=2`.
      - `'['`: recurse.
        - Inside recursion:
          - `'c'`: append `'c'`.
        - `']'`: return `'c'`.
      - Append `'c'` twice: `'a' + 'cc' = 'acc'`.
      - `'']'`: return `'acc'`.
  - Append `'acc'` three times: `'accaccacc'`.
- End.

---

## **Summary Table**

| Approach                         | Time Complexity | Space Complexity | Comments                                              |
|----------------------------------|----------------|------------------|-------------------------------------------------------|
| Stack-Based Iterative            | O(n)           | O(n)             | Common, straightforward, handles nested structures   |
| Recursive Parsing                | O(n)           | O(n) due to recursion stack | Elegant but can be less efficient with very deep nesting |

---

## **Final Tips for Interviews**
- Always handle nested brackets carefully.
- Use stack or recursion based on your comfort.
- Watch out for multi-digit numbers.
- Test with various nested levels to ensure correctness.
- Remember to reset temporary variables appropriately during parsing.

---

**Happy coding!**
