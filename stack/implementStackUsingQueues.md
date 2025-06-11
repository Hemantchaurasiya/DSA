# Implement Stack Using Queues

---

## Problem Overview
Implement a stack (LIFO - Last In First Out) using only queue operations (`push`, `pop`, `peek`, `empty`). The goal is to simulate stack behavior with queues.

---

## Approach 1: Naive Method — Push Operation Costly

### **Approach Name:**  
**"Push O(n), Pop O(1)" Method**

### **Core Idea:**
- Use a single queue.
- Make the `push()` operation costly by rotating elements so that the newest element is always at the front, enabling `pop()` and `top()` to be O(1).

### **Algorithm:**
1. When pushing a new element:
   - Enqueue the new element into the queue.
   - Rotate all elements behind it to the back:
     - For `size-1` times, dequeue from the front and enqueue at the back.
   - This ensures the newest element is always at the front.
2. Pop:
   - Dequeue from the front (which is the last inserted element).
3. Top:
   - Peek at the front element.
4. Empty:
   - Check if queue is empty.

### **Java Code:**
```java
import java.util.LinkedList;
import java.util.Queue;

public class StackUsingQueues {
    private Queue<Integer> queue;

    public StackUsingQueues() {
        queue = new LinkedList<>();
    }

    // Push operation (costly)
    public void push(int x) {
        queue.offer(x);
        int size = queue.size();
        // Rotate the queue to bring new element to front
        for (int i = 0; i < size - 1; i++) {
            queue.offer(queue.poll());
        }
    }

    // Pop operation
    public int pop() {
        return queue.poll();
    }

    // Top operation
    public int top() {
        return queue.peek();
    }

    // Check if empty
    public boolean empty() {
        return queue.isEmpty();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**
  - `push()`: O(n) due to rotation
  - `pop()`: O(1)
  - `top()`: O(1)
- **Space Complexity:** O(n) for storing elements in the queue.

---

## Approach 2: Costly `Pop` Operation — Push O(1), Pop O(n)

### **Approach Name:**  
**"Pop O(n), Push O(1)" Method**

### **Core Idea:**
- Use a single queue.
- Perform `push()` in O(1).
- Make `pop()` costly by rotating elements so that the last inserted element is always at the back, and during `pop()`, move all elements except the last to the back.

### **Algorithm:**
1. When pushing:
   - Enqueue element directly.
2. When popping:
   - Rotate all elements except the last:
     - For `size-1` times, dequeue from front and enqueue at the back.
   - Dequeue the last element (which is the top of the stack).
3. `top()`:
   - Similar to `pop()`, but after reaching the last element, enqueue it back to keep it in the queue.
4. `empty()`:
   - Check if queue is empty.

### **Java Code:**
```java
import java.util.LinkedList;
import java.util.Queue;

public class StackUsingQueues {
    private Queue<Integer> queue;

    public StackUsingQueues() {
        queue = new LinkedList<>();
    }

    // Push operation O(1)
    public void push(int x) {
        queue.offer(x);
    }

    // Pop operation O(n)
    public int pop() {
        int size = queue.size();
        // Rotate all elements except the last
        for (int i = 0; i < size - 1; i++) {
            queue.offer(queue.poll());
        }
        return queue.poll(); // Last inserted element
    }

    // Top operation
    public int top() {
        int size = queue.size();
        int topElement = -1;
        for (int i = 0; i < size; i++) {
            topElement = queue.poll();
            queue.offer(topElement);
        }
        return topElement;
    }

    // Check if empty
    public boolean empty() {
        return queue.isEmpty();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**
  - `push()`: O(1)
  - `pop()`: O(n)
  - `top()`: O(n)
- **Space Complexity:** O(n)

---

## Approach 3: Using Two Queues — Making Operations Efficient

### **Approach Name:**  
**"Using Two Queues" Method**

### **Core Idea:**
- Maintain two queues.
- For `push()`, enqueue into the empty queue, then transfer all elements from the other queue to maintain order.
- This way, `pop()` and `top()` are always at the front of the queue.

### **Algorithm:**
1. Keep two queues: `q1` and `q2`.
2. For `push(x)`:
   - Enqueue `x` into `q2`.
   - Transfer all elements from `q1` to `q2`.
   - Swap `q1` and `q2`.
3. For `pop()`:
   - Dequeue from `q1`.
4. For `top()`:
   - Peek at `q1`.
5. Check empty with `q1.isEmpty()`.

### **Java Code:**
```java
import java.util.LinkedList;
import java.util.Queue;

public class StackUsingQueues {
    private Queue<Integer> q1;
    private Queue<Integer> q2;

    public StackUsingQueues() {
        q1 = new LinkedList<>();
        q2 = new LinkedList<>();
    }

    // Push operation
    public void push(int x) {
        q2.offer(x);
        while (!q1.isEmpty()) {
            q2.offer(q1.poll());
        }
        // Swap q1 and q2
        Queue<Integer> temp = q1;
        q1 = q2;
        q2 = temp;
    }

    // Pop operation
    public int pop() {
        return q1.poll();
    }

    // Top operation
    public int top() {
        return q1.peek();
    }

    // Check if empty
    public boolean empty() {
        return q1.isEmpty();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**
  - `push()`: O(n) (due to transfer)
  - `pop()`: O(1)
  - `top()`: O(1)
- **Space Complexity:** O(n)

---

## **Dry Run Example**

Let's consider pushing elements 1, 2, 3 and then popping:

### Using Approach 3 (Two Queues):

| Step | Operations                          | Queues State                            | Comment                                  |
|-------|-------------------------------------|----------------------------------------|------------------------------------------|
| Push 1 | `push(1)`                         | q1 = [1], q2 = []                     | Enqueue into q2, swap q1 and q2        |
| Push 2 | `push(2)`                         | q1 = [2, 1], q2 = []                  | Transfer all from q1 to q2, swap     |
| Push 3 | `push(3)`                         | q1 = [3, 2, 1], q2 = []               | Transfer all, swap                    |
| Pop    | `pop()`                           | q1 = [2, 1],                          | Dequeue from q1                        |
| Top    | `top()`                           | 2                                    | Peek at q1                             |

---

## **Summary Table**

| Approach                                | Time Complexity                        | Space Complexity | Remarks                                              |
|----------------------------------------|----------------------------------------|------------------|-----------------------------------------------------|
| Push costly, Pop cheap (Approach 1)   | Push: O(n), Pop: O(1)                | O(n)             | Good for frequent `pop()` operations               |
| Pop costly, Push cheap (Approach 2)   | Push: O(1), Pop: O(n)                | O(n)             | Good for frequent `push()` operations              |
| Two queues (Approach 3)                 | Push: O(n), Pop: O(1), Top: O(1)     | O(n)             | Maintains efficient `pop()` and `top()`           |

---

## **Final Tips for Interviews**
- Understand the trade-offs between different approaches.
- Use two queues if you want `pop()` and `top()` to be O(1), even if `push()` becomes O(n).
- For space efficiency, prefer approaches with minimal auxiliary data structures.
- Remember queue operations: `offer()` (enqueue), `poll()` (dequeue), `peek()` (front element).

---

**Happy coding!**
