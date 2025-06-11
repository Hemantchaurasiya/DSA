# Min Stack

---

## Problem Overview
Design a stack data structure that supports the following operations in constant time:

- `push(x)`: Push element `x` onto the stack.
- `pop()`: Remove the element on top of the stack.
- `top()`: Get the top element.
- `getMin()`: Retrieve the minimum element in the stack.

The challenge is to perform all operations efficiently, especially tracking the minimum element.

---

## Approach 1: Naive Method — Store Min with Each Element

### **Approach Name:**  
**"Naive Approach: Store Min Along with Each Element"**

---

### **Core Idea:**
- When pushing an element, store the current element along with the minimum value up to that point.
- Use a stack of pairs `(value, currentMin)`.

---

### **Algorithm:**
1. **Push(x):**  
   - Determine the current minimum: `min(x, top's min)` if stack isn't empty.
   - Push `(x, min)` onto the stack.
2. **Pop():**  
   - Pop the top pair.
3. **Top():**  
   - Return the `value` part of the top pair.
4. **GetMin():**  
   - Return the `currentMin` part of the top pair.

---

### **Java Code:**
```java
import java.util.Stack;

public class MinStack {
    private Stack<int[]> stack;

    public MinStack() {
        stack = new Stack<>();
    }

    public void push(int x) {
        if (stack.isEmpty()) {
            stack.push(new int[]{x, x});
        } else {
            int currentMin = Math.min(x, stack.peek()[1]);
            stack.push(new int[]{x, currentMin});
        }
    }

    public void pop() {
        stack.pop();
    }

    public int top() {
        return stack.peek()[0];
    }

    public int getMin() {
        return stack.peek()[1];
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - All operations (`push`, `pop`, `top`, `getMin`) are O(1).
- **Space Complexity:**  
  - O(n) for storing each element along with its current minimum.

---

## Approach 2: Using Auxiliary Stack to Track Minimums

### **Approach Name:**  
**"Optimized Approach with Auxiliary Min Stack"**

---

### **Core Idea:**
- Use two stacks:
  - `mainStack`: stores all elements.
  - `minStack`: stores the minimum element at each level.
- When pushing:
  - Push into `mainStack`.
  - Push the new element into `minStack` if it is less than or equal to the current minimum.
- When popping:
  - Pop from `mainStack`.
  - If the popped element is equal to the top of `minStack`, pop `minStack` as well.
- `getMin()` is always the top of `minStack`.

---

### **Algorithm:**
1. **Push(x):**  
   - Push `x` onto `mainStack`.
   - If `minStack` is empty or `x` <= top of `minStack`, push `x` onto `minStack`.
2. **Pop():**  
   - Pop from `mainStack`.
   - If popped element equals the top of `minStack`, pop from `minStack`.
3. **Top():**  
   - Return top of `mainStack`.
4. **GetMin():**  
   - Return top of `minStack`.

---

### **Java Code:**
```java
import java.util.Stack;

public class MinStack {
    private Stack<Integer> mainStack;
    private Stack<Integer> minStack;

    public MinStack() {
        mainStack = new Stack<>();
        minStack = new Stack<>();
    }

    public void push(int x) {
        mainStack.push(x);
        if (minStack.isEmpty() || x <= minStack.peek()) {
            minStack.push(x);
        }
    }

    public void pop() {
        if (mainStack.peek().equals(minStack.peek())) {
            minStack.pop();
        }
        mainStack.pop();
    }

    public int top() {
        return mainStack.peek();
    }

    public int getMin() {
        return minStack.peek();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - O(1) for all operations.
- **Space Complexity:**  
  - O(n) for maintaining two stacks.

---

## Approach 3: Single Stack with Lazy Min Tracking (Advanced)

### **Approach Name:**  
**"Single Stack with Lazy Min Tracking"**

---

### **Core Idea:**
- Use a single stack to store all elements.
- Keep track of the minimum separately.
- When pushing, push the element and update the current minimum if necessary.
- When popping, if the popped element is the minimum, restore the previous minimum.

### **Challenge:**
- Requires additional logic to store previous minima, often involves storing old minima on the stack.

---

### **Alternate Implementation:**
- Store `(value, prevMin)` pairs in the stack.
- When pushing, store the current value and the previous minimum.
- When popping, restore previous minimum.

*Note:* This is similar to Approach 1 but often involves more complex logic.

---

## **Dry Run Example**

Suppose we perform these operations:

```plaintext
push(5)
push(3)
push(7)
getMin() -> 3
pop()
getMin() -> 3
pop()
getMin() -> 5
```

### Using Approach 2 (Auxiliary Min Stack):

| Operation | mainStack            | minStack            | Comment                                        |
|------------|------------------------|---------------------|------------------------------------------------|
| push(5)    | [5]                    | [5]                 | Push 5, minStack empty, push 5                |
| push(3)    | [5, 3]                 | [5, 3]              | 3 <= 5, push 3 into minStack                |
| push(7)    | [5, 3, 7]              | [5, 3]              | 7 > 3, do not push into minStack             |
| getMin()   |                        | 3                   | Top of minStack is 3                        |
| pop()      | [5, 3]                 | [5]                 | Popped 7, no change to minStack             |
| getMin()   |                        | 3                   | Still 3                                   |
| pop()      | [5]                    | [5]                 | Popped 3, remove from minStack             |
| getMin()   |                        | 5                   | Now minimum is 5                          |

---

## **Summary Table**

| Approach                                              | Time Complexity                 | Space Complexity | Remarks                                           |
|--------------------------------------------------------|---------------------------------|------------------|--------------------------------------------------|
| Store min with each element (pair)                     | O(1) each operation             | O(n)             | Simple, clear, but uses extra space per element |
| Two stacks with auxiliary min stack                     | O(1) each operation             | O(n)             | Efficient and clear, widely used approach       |
| Single stack with lazy min tracking (advanced)         | O(1) amortized                  | O(n)             | More complex, suitable for advanced interviews  |

---

## **Final Tips for Interviews**
- The approach using two stacks with an auxiliary min stack (Approach 2) is the most common and efficient.
- Always think about how to keep track of the minimum without increasing complexity.
- Be clear about how the minimum updates during push/pop operations.
- Practice implementing both approaches to handle different interview scenarios.

---

**Happy coding!**
