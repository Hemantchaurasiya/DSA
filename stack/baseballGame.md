# Baseball Game

---

## Problem Overview
You're given a list of operations representing a baseball game scoring system. Operations can be:
- An integer `x`: Record a new score `x`.
- `"+"`: Record a new score that is the sum of the previous two scores.
- `"D"`: Record a new score that is double the previous score.
- `"C"`: Remove the last recorded score.

Your task is to process all operations and return the sum of the scores after processing all operations.

---

## Approach 1: Brute Force with List Operations

### **Core Idea**
- Use a list (or stack) to keep track of valid scores.
- For each operation:
  - If it's a number, add it to the list.
  - If `"+"`, sum the last two scores and add.
  - If `"D"`, double the last score and add.
  - If `"C"`, remove the last score.
- Sum all remaining scores at the end.

### **Algorithm**
1. Initialize an empty list (or stack).
2. Iterate over each operation:
   - If operation is an integer, parse and add to list.
   - If `"+"`, sum last two scores and add.
   - If `"D"`, double last score and add.
   - If `"C"`, remove last score.
3. Sum all scores in the list and return.

### **Java Code**
```java
import java.util.Stack;

public class BaseballGame {
    public int calPoints(String[] ops) {
        Stack<Integer> stack = new Stack<>();
        for (String op : ops) {
            if (op.equals("+")) {
                int top = stack.pop();
                int newTop = top + stack.peek();
                stack.push(top);
                stack.push(newTop);
            } else if (op.equals("D")) {
                stack.push(2 * stack.peek());
            } else if (op.equals("C")) {
                stack.pop();
            } else {
                // It's a number
                stack.push(Integer.parseInt(op));
            }
        }
        int sum = 0;
        for (int score : stack) {
            sum += score;
        }
        return sum;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — each operation processed once.
- **Space Complexity:** *O(n)* — in the worst case, all scores stored in stack.

### **Dry Run**
Input: `["5", "2", "C", "D", "+"]`

| Step | Operation | Stack (before) | Action | Stack (after) | Explanation                     |
|-------|------------|----------------|---------|--------------|---------------------------------|
| 1     | "5"        | []             | push 5  | [5]          | Add score 5                     |
| 2     | "2"        | [5]            | push 2  | [5, 2]       | Add score 2                     |
| 3     | "C"        | [5, 2]         | pop 2   | [5]          | Remove last score               |
| 4     | "D"        | [5]            | push 10 | [5, 10]      | Double last score (5*2=10)    |
| 5     | "+"        | [5, 10]        | push 15 | [5, 10, 15]  | Sum last two scores (10+5=15) |

Final sum: 5 + 10 + 15 = **30**

---

## Approach 2: Optimized Stack-Based Solution

### **Core Idea**
- Use a stack to manage scores for constant-time access to previous scores.
- Handle operations as they come, updating the stack accordingly.
- Summation is straightforward after processing.

### **Algorithm**
Same as above, but emphasizing clarity and efficiency:
1. Initialize a stack.
2. Process each operation:
   - For number: push.
   - For `"+"`: pop last two, sum, push back the previous and the sum.
   - For `"D"`: push double of last.
   - For `"C"`: pop last.
3. Sum all elements at the end.

### **Java Code**
```java
import java.util.Stack;

public class BaseballGame {
    public int calPoints(String[] ops) {
        Stack<Integer> stack = new Stack<>();
        for (String op : ops) {
            switch (op) {
                case "+":
                    int last = stack.pop();
                    int secondLast = stack.peek();
                    int sum = last + secondLast;
                    stack.push(last);
                    stack.push(sum);
                    break;
                case "D":
                    stack.push(2 * stack.peek());
                    break;
                case "C":
                    stack.pop();
                    break;
                default:
                    stack.push(Integer.parseInt(op));
            }
        }
        int total = 0;
        for (int score : stack) {
            total += score;
        }
        return total;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, each operation processed once.
- **Space Complexity:** *O(n)*, in worst case, all scores stored.

---

## **Summary Table**

| Approach                        | Time Complexity | Space Complexity | Comments                                               |
|---------------------------------|----------------|------------------|--------------------------------------------------------|
| List-based Simulation           | O(n)           | O(n)             | Straightforward, easy to implement                     |
| Stack-based Solution            | O(n)           | O(n)             | Most efficient, handles all operations gracefully    |

---

## **Final Tips for Interviews**
- Use a stack for clarity and efficiency.
- Carefully handle `"+"` by temporarily popping or peeking.
- Remember to parse integers for number operations.
- Test with various sequences of operations to ensure correctness.
- Think through edge cases like empty stack, only `"C"` operations, or only number inputs.

---

**Happy coding!**
