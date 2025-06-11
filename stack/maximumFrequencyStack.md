# Maximum Frequency Stack

---

## Problem Overview
Design a data structure that supports the following operations:

- `push(x)`: Push an element `x` onto the stack.
- `pop()`: Remove and return the most frequent element. If multiple elements have the same highest frequency, the most recently pushed among them should be removed and returned.

This structure behaves like a stack with an emphasis on the **frequency** of elements.

---

## Approach 1: Brute Force (Naive)

### **Approach Name:**  
**"Naive Approach"**

---

### **Core Idea:**
- On each `pop()`, scan the entire stack to find the element with the highest frequency.
- If multiple elements have the same highest frequency, choose the most recent one.
- This approach is inefficient as it requires scanning the entire data structure for each pop.

---

### **Algorithm:**
1. Maintain a list (or stack) of all pushed elements.
2. To `pop()`:
   - Count the frequency of each element in the list.
   - Identify the element with the highest frequency.
   - Among those, pick the most recent (i.e., last pushed).
   - Remove that element from the list and return it.
3. To `push(x)`:
   - Append `x` to the list.

---

### **Java Code:**
```java
import java.util.ArrayList;
import java.util.List;

public class MaxFreqStackNaive {
    private List<Integer> stack;

    public MaxFreqStackNaive() {
        stack = new ArrayList<>();
    }

    public void push(int x) {
        stack.add(x);
    }

    public int pop() {
        if (stack.isEmpty()) return -1;

        // Count frequencies
        int maxFreq = 0;
        int candidate = -1;
        for (int i = stack.size() - 1; i >= 0; i--) {
            int current = stack.get(i);
            int freq = 0;
            for (int num : stack) {
                if (num == current) freq++;
            }
            if (freq > maxFreq || (freq == maxFreq && i > stack.indexOf(candidate))) {
                maxFreq = freq;
                candidate = current;
            }
        }
        // Remove the candidate (most recent among highest frequency)
        for (int i = stack.size() - 1; i >= 0; i--) {
            if (stack.get(i) == candidate) {
                stack.remove(i);
                break;
            }
        }
        return candidate;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - `push()`: O(1).  
  - `pop()`: O(n^2) in worst case (due to counting frequencies and scanning for most recent element).
- **Space Complexity:**  
  - O(n), for storing all elements.

---

## Approach 2: Optimized with Frequency Map and Grouping (Most Efficient)

### **Approach Name:**  
**"Optimized Approach Using Frequency Map and Grouping"**

---

### **Core Idea:**
- To efficiently get the most frequent element, maintain:
  - A `freqMap`: element -> frequency.
  - A `groupMap`: frequency -> stack of elements with that frequency.
  - Track the maximum frequency (`maxFreq`) at all times.
- When pushing:
  - Update the element's frequency.
  - Push the element into the stack corresponding to its new frequency.
  - Update `maxFreq`.
- When popping:
  - Pop the top element from the stack corresponding to `maxFreq`.
  - Decrement its frequency.
  - If the stack for `maxFreq` becomes empty, decrease `maxFreq`.

This approach ensures O(1) for both `push` and `pop`.

---

### **Algorithm:**
1. **push(x):**
   - Increment `freqMap[x]`.
   - Push `x` into `groupMap[freqMap[x]]`.
   - Update `maxFreq` if necessary.
2. **pop():**
   - Pop element from `groupMap[maxFreq]`.
   - Decrement its frequency in `freqMap`.
   - If `groupMap[maxFreq]` becomes empty, decrease `maxFreq`.
   - Return the popped element.

---

### **Java Code:**
```java
import java.util.HashMap;
import java.util.Stack;

public class MaxFreqStack {
    private HashMap<Integer, Integer> freqMap;
    private HashMap<Integer, Stack<Integer>> groupMap;
    private int maxFreq;

    public MaxFreqStack() {
        freqMap = new HashMap<>();
        groupMap = new HashMap<>();
        maxFreq = 0;
    }

    public void push(int x) {
        int freq = freqMap.getOrDefault(x, 0) + 1;
        freqMap.put(x, freq);
        groupMap.putIfAbsent(freq, new Stack<>());
        groupMap.get(freq).push(x);
        if (freq > maxFreq) {
            maxFreq = freq;
        }
    }

    public int pop() {
        Stack<Integer> stack = groupMap.get(maxFreq);
        int x = stack.pop();
        freqMap.put(x, freqMap.get(x) - 1);
        if (stack.isEmpty()) {
            maxFreq--;
        }
        return x;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - `push()`: O(1).  
  - `pop()`: O(1).  
- **Space Complexity:**  
  - O(n), for storing all elements, their frequencies, and grouping.

---

## **Dry Run Example**

Suppose we perform the following operations:

```plaintext
push(5)
push(7)
push(5)
push(7)
push(4)
push(5)
pop() -> 5
pop() -> 7
pop() -> 5
pop() -> 4
```

### Step-by-step:

| Operation | freqMap                    | groupMap (top of stacks)        | maxFreq | Output |
|------------|----------------------------|--------------------------------|----------|---------|
| push(5)    | {5:1}                      | {1: [5]}                       | 1        |         |
| push(7)    | {5:1,7:1}                  | {1: [5,7]}                     | 1        |         |
| push(5)    | {5:2,7:1}                  | {1: [5,7], 2: [5]}             | 2        |         |
| push(7)    | {5:2,7:2}                  | {1: [5,7], 2: [5,7]}           | 2        |         |
| push(4)    | {5:2,7:2,4:1}              | {1: [5,7,4], 2: [5,7]}         | 2        |         |
| push(5)    | {5:3,7:2,4:1}              | {1: [5,7,4], 2: [5,7], 3: [5]} | 3        |         |
| pop()      | Remove from maxFreq=3 stack| {5:2,7:2,4:1}                   | 2        | 5       |
| pop()      | Remove from maxFreq=2 stack| {5:2,7:1}                       | 2        | 7       |
| pop()      | Remove from maxFreq=2 stack| {5:1}                           | 1        | 5       |
| pop()      | Remove from maxFreq=1 stack| {}                              | 0        | 4       |

---

## **Summary Table**

| Approach                                              | Time Complexity                     | Space Complexity | Remarks                                              |
|--------------------------------------------------------|-------------------------------------|------------------|------------------------------------------------------|
| Naive scanning of entire stack for each pop           | O(n^2)                              | O(n)             | Not suitable for large data; inefficient            |
| HashMap + Grouping (Optimal)                            | O(1) for push and pop               | O(n)             | Most efficient and widely used approach             |

---

## **Final Tips for Interviews**
- Use the grouping method with `HashMap<Integer, Stack<Integer>>` for optimal performance.
- Keep track of `maxFreq` to avoid scanning for the highest frequency.
- Practice implementing both `push()` and `pop()` carefully to handle the grouping and frequency updates efficiently.

---

**Happy coding!**
