# Implement Queue Using Stacks

---

## Problem Overview
Implement a queue (FIFO - First In First Out) data structure using only stack operations (`push`, `pop`, `top`, `empty`). The goal is to simulate queue behavior with stacks.

---

## Approach 1: Using Two Stacks — Costly `Enqueue` or `Dequeue`

### **Approach Name:**  
**"Costly Enqueue or Dequeue Method"**

---

### **Core Idea:**
- Use two stacks, `stack1` and `stack2`.
- To maintain queue order, either:
  - Make `enqueue()` O(n) by transferring elements between stacks, or
  - Make `dequeue()` O(n).

---

### **Method 1: Costly Enqueue (Push Operation)**
- Push new elements into `stack2`.
- Transfer all elements from `stack1` to `stack2` to reverse order.
- Swap the stacks so that `stack1` always has queue order with the front at the top.

**Steps:**
1. Push the new element into `stack2`.
2. Transfer all elements from `stack1` to `stack2`.
3. Swap references of `stack1` and `stack2`.
4. For `dequeue()`, simply pop from `stack1`.

---

### **Java Code:**
```java
import java.util.Stack;

public class QueueUsingStacks {
    private Stack<Integer> stack1;
    private Stack<Integer> stack2;

    public QueueUsingStacks() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }

    // Enqueue operation (costly)
    public void enqueue(int x) {
        // Push into stack2
        stack2.push(x);
        // Transfer all elements from stack1 to stack2
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
        // Swap stack1 and stack2
        Stack<Integer> temp = stack1;
        stack1 = stack2;
        stack2 = temp;
    }

    // Dequeue operation
    public int dequeue() {
        return stack1.pop();
    }

    // Front element
    public int front() {
        return stack1.peek();
    }

    // Check if queue is empty
    public boolean isEmpty() {
        return stack1.isEmpty();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**
  - `enqueue()`: O(n) due to transferring elements
  - `dequeue()`: O(1)
  - `front()`: O(1)
- **Space Complexity:** O(n) for storing elements in stacks.

---

## Approach 2: Using Two Stacks — Costly `Dequeue` (Push is O(1))

### **Approach Name:**  
**"Costly Dequeue Method"**

---

### **Core Idea:**
- Use two stacks: `stack1` (for enqueue) and `stack2` (for dequeue).
- Enqueue operation is O(1): just push into `stack1`.
- Dequeue operation is O(n): transfer elements from `stack1` to `stack2` when needed, then pop from `stack2`.

---

### **Algorithm:**
1. For `enqueue(x)`:
   - Push `x` into `stack1`.
2. For `dequeue()`:
   - If `stack2` is empty, transfer all elements from `stack1` to `stack2`.
   - Pop from `stack2`.
3. For `front()`:
   - If `stack2` is empty, transfer elements from `stack1`.
   - Peek `stack2`.
4. `isEmpty()` checks if both stacks are empty.

---

### **Java Code:**
```java
import java.util.Stack;

public class QueueUsingStacks {
    private Stack<Integer> stack1;
    private Stack<Integer> stack2;

    public QueueUsingStacks() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }

    // Enqueue operation (O(1))
    public void enqueue(int x) {
        stack1.push(x);
    }

    // Dequeue operation (O(n))
    public int dequeue() {
        if (stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }
        return stack2.pop();
    }

    // Front element
    public int front() {
        if (stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }
        return stack2.peek();
    }

    // Check if queue is empty
    public boolean isEmpty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**
  - `enqueue()`: O(1)
  - `dequeue()`: O(n) in worst case (when transferring)
  - Amortized: O(1) for each operation over many calls
- **Space Complexity:** O(n)

---

## Approach 3: Single Stack with Lazy Transfer (Optimal for Amortized Operations)

### **Approach Name:**  
**"Single Stack with Lazy Transfer"**

---

### **Core Idea:**
- Use only one stack.
- To `enqueue`, push directly (O(1)).
- To `dequeue`, transfer elements to an auxiliary variable or use recursive techniques to get the front element, or perform transfer only when necessary to achieve amortized O(1).

*Note:* This approach often involves recursion or clever lazy evaluation, but the two-stack approach is generally more straightforward and efficient.

---

## **Dry Run Example**

### Using the "Two Stack Costly Dequeue" Approach:

**Operations:**
- `enqueue(1)`
- `enqueue(2)`
- `enqueue(3)`
- `dequeue()` -> should return 1
- `front()` -> should return 2

| Step | Operation                  | stack1             | stack2 | Comments                                         |
|-------|----------------------------|---------------------|---------|--------------------------------------------------|
|       | Enqueue 1                  | [1]                 | []      | Push into stack1                                |
|       | Enqueue 2                  | [1, 2]              | []      | Push into stack1                                |
|       | Enqueue 3                  | [1, 2, 3]           | []      | Push into stack1                                |
|       | Dequeue                    | Transfer to stack2  | [3, 2, 1] | Transfer all from stack1 to stack2             |
|       |                            |                     | [3, 2, 1] | Pop from stack2 (returns 1)                     |
|       | Front                      | stack2: [3, 2]      | [3]     | Transfer if needed, peek at top of stack2      |

---

## **Summary Table**

| Approach                                  | Time Complexity                        | Space Complexity | Remarks                                              |
|-------------------------------------------|----------------------------------------|------------------|-----------------------------------------------------|
| Costly Enqueue (push O(n)) / Dequeue O(1) | Enqueue: O(n), Dequeue: O(1)          | O(n)             | Best for frequent dequeue operations               |
| Costly Dequeue (push O(1)) / Dequeue O(n) | Enqueue: O(1), Dequeue: O(n)          | O(n)             | Best for frequent enqueue operations               |
| Amortized using two stacks (balanced)     | O(1) amortized per operation          | O(n)             | Efficient overall, preferred for practical use     |

---

## **Final Tips for Interviews**
- Understand the trade-offs between the different approaches.
- The two-stack method with lazy transfer is typically the most straightforward and efficient.
- Use clear variable naming and comments to explain the logic.
- Remember that amortized analysis can help justify the efficiency of certain methods.

---

**Happy coding!**
