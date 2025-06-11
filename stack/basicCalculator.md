# Basic Calculator

---

## Problem Overview
Implement a basic calculator to evaluate a simple expression string containing non-negative integers, `+`, `-`, `(`, and `)`. The expression may include spaces. The goal is to compute the integer result of the expression.

**Example:**
- Input: `"1 + (2 - (2 + 3)) + 4"`
- Output: `2`

---

## Approach 1: Recursive Parsing (Naive)

### **Core Idea**
- Recursively evaluate expressions, handling parentheses by recursive calls.
- When encountering `'('`, recursively evaluate the sub-expression until `')'`.
- Use a helper function to parse tokens and compute the result.

### **Algorithm**
1. Use a global index pointer to traverse the string.
2. Implement a recursive function:
   - Initialize `result` and `sign`.
   - Loop through the string:
     - If digit, parse the full number and add/subtract based on `sign`.
     - If `'+'`, set `sign = 1`.
     - If `'-'`, set `sign = -1`.
     - If `'('`, recursively evaluate the sub-expression.
     - If `')'`, return the current `result`.
3. Return the final result after parsing.

### **Java Code**
```java
public class BasicCalculator {
    int index = 0;
    public int calculate(String s) {
        return helper(s);
    }

    private int helper(String s) {
        int result = 0;
        int sign = 1;
        int num = 0;

        while (index < s.length()) {
            char c = s.charAt(index);
            if (Character.isDigit(c)) {
                num = num * 10 + (c - '0');
            } else if (c == '+') {
                result += sign * num;
                num = 0;
                sign = 1;
            } else if (c == '-') {
                result += sign * num;
                num = 0;
                sign = -1;
            } else if (c == '(') {
                index++; // skip '('
                int nested = helper(s);
                result += sign * nested;
            } else if (c == ')') {
                result += sign * num;
                index++; // skip ')'
                return result;
            }
            index++;
        }
        result += sign * num;
        return result;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — each character is processed once.
- **Space Complexity:** *O(n)* — stack space for recursion calls in worst case (deep parentheses).

### **Dry Run**
Expression: `"1 + (2 - (2 + 3)) + 4"`

| Step | Pointer | Current Char | Action/State | Result | Explanation |
|-------|----------|----------------|--------------|---------|--------------|
| 0     | 0        | '1'            | parse number | result=1 | Read '1' |
| 1     | 1        | ' '            | skip | result=1 | skip spaces |
| 2     | 2        | '+'            | sign=1 | result=1 | '+' sign |
| 3     | 3        | ' '            | skip | result=1 | skip spaces |
| 4     | 4        | '('            | recurse | evaluate `(2 - (2 + 3))` | recurse |
| 5     | 5        | '2'            | parse number | nested result=2 | inside parentheses |
| 6     | 6        | '-'            | result+=2, sign=-1 | result=2, sign=-1 | set sign to -1 |
| 7     | 7        | '('            | recurse | evaluate `(2 + 3)` | recurse again |
| 8     | 8        | '2'            | parse number | nested nested=2 | inside inner parentheses |
| 9     | 9        | '+'            | result+=2, sign=1 | nested nested=2 | plus sign |
| 10    | 10       | '3'            | parse number | nested nested=3 | parse 3 |
| 11    | 11       | ')'            | return 2+3=5 | inner recurse returns 5 | end inner parentheses |
| 12    | 12       | ')'            | return 2-5=-3 | outer recurse returns -3 | end outer parentheses |
| 13    | 13       | '+'            | result+=(-3) => 1 + (-3) = -2 | result=-2 | after parentheses |
| 14    | 14       | ' '            | skip | result=-2 | skip spaces |
| 15    | 15       | '+'            | sign=1 | result=-2 | plus sign |
| 16    | 16       | ' '            | skip | result=-2 | skip spaces |
| 17    | 17       | '4'            | parse number | result=-2+4=2 | add 4 |

Final result: `2`.

---

## Approach 2: Using Stack (Optimized)

### **Core Idea**
- Use a stack to evaluate the expression in a single pass.
- Push numbers and signs onto the stack.
- When encountering `'('`, push a marker or reset context.
- When encountering `')'`, pop until the marker or compute the sub-expression.

### **Algorithm**
1. Initialize a stack, `num` to store current number, `sign` to 1, and `result` to 0.
2. Loop through each character:
   - If digit, build the current number.
   - If `'+'`, add previous number to result, reset `num`, and set `sign=1`.
   - If `'-'`, add previous number to result, reset `num`, and set `sign=-1`.
   - If `'('`, push current `result` and `sign` onto stack, reset `result=0`, `sign=1`.
   - If `')'`, add last number to `result`, pop sign and previous result from stack, compute combined value.
3. After loop, add last number to `result`.
4. Return `result`.

### **Java Code**
```java
public int calculate(String s) {
    Stack<Integer> stack = new Stack<>();
    int result = 0;
    int num = 0;
    int sign = 1;

    for (char c : s.toCharArray()) {
        if (Character.isDigit(c)) {
            num = num * 10 + (c - '0');
        } else if (c == '+') {
            result += sign * num;
            num = 0;
            sign = 1;
        } else if (c == '-') {
            result += sign * num;
            num = 0;
            sign = -1;
        } else if (c == '(') {
            stack.push(result);
            stack.push(sign);
            result = 0;
            sign = 1;
        } else if (c == ')') {
            result += sign * num;
            num = 0;
            int prevSign = stack.pop();
            int prevResult = stack.pop();
            result = prevResult + prevSign * result;
        }
    }
    result += sign * num;
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — single pass through string.
- **Space Complexity:** *O(n)* — stack for parentheses.

### **Dry Run**
Expression: `"1 + (2 - (2 + 3)) + 4"`

- Process:
  - `'1'`: num=1
  - `'+'`: result=0+1=1, sign=1
  - `'('`: push result=1, push sign=1; reset result=0
  - `'2'`: num=2
  - `'-'`: result=0+1*2=2, sign=-1, num=0
  - `'('`: push result=2, push sign=-1; reset result=0
  - `'2'`: num=2
  - `'+'`: result=0+1*2=2, sign=1, num=0
  - `'3'`: num=3
  - `')'`: result=2+1*3=5; pop sign=-1, result=1; result=1 + (-1)*5 = -4
  - `')'`: pop sign=1, result=1; result=1 + 1*(-4) = -3
  - `'+'`: result=1+(-3)=-2, sign=1
  - `'4'`: num=4
- End: result += sign*4 = -2+4=2

Final output: `2`.

---

## **Summary Table**

| Approach                         | Time Complexity | Space Complexity | Comments                                              |
|----------------------------------|----------------|------------------|-------------------------------------------------------|
| Recursive Parsing                | O(n)           | O(n)             | Clear but involves recursion stack                  |
| Stack-Based Single Pass Approach | O(n)           | O(n)             | Efficient, iterative, suitable for large input     |

---

## **Final Tips for Interviews**
- Understand how to handle parentheses and operator precedence.
- Use a stack to efficiently evaluate nested expressions.
- Keep track of current number, sign, and accumulated result.
- Handle spaces gracefully by skipping them.
- Edge cases:
  - Empty string.
  - String with only numbers.
  - Multiple nested parentheses.

---

**Happy coding!**
