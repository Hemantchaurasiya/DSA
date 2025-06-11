# Evaluate Reverse Polish Notation

---

## Problem Overview
Given an array of tokens representing an expression in Reverse Polish Notation (RPN), evaluate the expression and return its value.

**Example:**
- Input: `["2", "1", "+", "3", "*"]`
- Output: `9`

**Explanation:**
- `2 1 +` evaluates to `3`
- `3 *` multiplies by `3`
- Result is `9`.

---

## Approach 1: Using a Stack (Optimal and Standard Solution)

### **Core Idea**
- RPN expressions are naturally suited for stack-based evaluation.
- Push operands (numbers) onto the stack.
- When an operator is encountered, pop the required number of operands, perform the operation, and push the result back.
- The final value on the stack after processing all tokens is the result.

### **Algorithm**
1. Initialize an empty stack.
2. Iterate through each token:
   - If the token is a number, convert it to integer and push onto the stack.
   - If the token is an operator (`+`, `-`, `*`, `/`):
     - Pop two operands from the stack (`b` and `a`; note `b` is popped first, then `a`).
     - Perform the operation: `a op b`.
     - Push the result back onto the stack.
3. After processing all tokens, the stack will contain only one element: the final result.
4. Return that element.

### **Java Code**
```java
import java.util.Stack;

public class EvaluateRPN {
    public int evalRPN(String[] tokens) {
        Stack<Integer> stack = new Stack<>();
        for (String token : tokens) {
            if (isOperator(token)) {
                int b = stack.pop();
                int a = stack.pop();
                int result = performOperation(a, b, token);
                stack.push(result);
            } else {
                stack.push(Integer.parseInt(token));
            }
        }
        return stack.pop();
    }

    private boolean isOperator(String token) {
        return token.equals("+") || token.equals("-") || token.equals("*") || token.equals("/");
    }

    private int performOperation(int a, int b, String operator) {
        switch (operator) {
            case "+": return a + b;
            case "-": return a - b;
            case "*": return a * b;
            case "/": return a / b; // assume b != 0
            default: throw new IllegalArgumentException("Invalid operator");
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — each token is processed once.
- **Space Complexity:** *O(n)* — in the worst case, all tokens are pushed onto the stack.

### **Dry Run**
Input: `["2", "1", "+", "3", "*"]`

| Step | Token  | Stack Contents             | Operation | Result / Stack After Operation                     |
|-------|---------|----------------------------|-----------|---------------------------------------------------|
| 1     | "2"     | [2]                        |           | Push 2                                            |
| 2     | "1"     | [2, 1]                     |           | Push 1                                            |
| 3     | "+"     | [3]                        | Pop 1, 2; 2+1=3 | Push 3                                           |
| 4     | "3"     | [3, 3]                     |           | Push 3                                            |
| 5     | "*"     | [9]                        | Pop 3, 3; 3*3=9 | Push 9                                           |

Result: `9`.

---

## Approach 2: Recursive Evaluation (Less Common)

### **Core Idea**
- Recursively evaluate the expression by processing tokens from the end.
- When encountering an operator, evaluate the right and left operands recursively.
- This approach is less practical for RPN but demonstrates recursion.

### **Algorithm**
- Use a pointer from the end of tokens.
- If token is a number, return it.
- If token is an operator:
  - Recursively evaluate the right operand.
  - Recursively evaluate the left operand.
  - Perform the operation and return the result.

### **Note**
- This approach is more naturally suited for infix expressions with parentheses and operator precedence, not RPN.

---

## **Summary Table**

| Approach                         | Time Complexity | Space Complexity | Comments                                              |
|----------------------------------|----------------|------------------|-------------------------------------------------------|
| Stack-Based Evaluation           | O(n)           | O(n)             | Standard, efficient, straightforward implementation   |
| Recursive Evaluation (less common)| O(n)           | O(n) due to recursion stack | Less practical for RPN, mainly for educational purposes |

---

## **Final Tips for Interviews**
- Use a stack for clarity and efficiency.
- Carefully handle integer division, especially in languages where integer division truncates.
- Remember to check for empty stack before popping.
- Practice evaluating different operators and complex RPN expressions.

---

**Happy coding!**
