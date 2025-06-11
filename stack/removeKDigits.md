# Remove K Digits

---

## Problem Overview
Given a non-negative integer as a string `num` and an integer `k`, remove **k** digits from the number so that the new number is the smallest possible. The relative order of the remaining digits must be preserved.

**Example:**
- Input: `num = "1432219"`, `k = 3`
- Output: `"1219"`

**Explanation:** Removing digits `'4'`, `'3'`, and `'2'` yields the smallest number `"1219"`.

---

## Approach 1: Brute Force (Try All Combinations)

### **Core Idea**
- Generate all possible numbers by removing `k` digits.
- Return the minimum among all generated numbers.

### **Algorithm**
1. Generate all combinations of the string `num` by removing `k` digits.
2. For each combination, convert to number and keep track of the smallest.
3. Return the smallest number as a string.

### **Note:**
- This approach is **not feasible** for large inputs due to exponential complexity.

### **Complexity Analysis**
- **Time Complexity:** *O(C(n, k) * n)* — exponential in `k`, impractical for large inputs.
- **Space Complexity:** *O(n)* for storing combinations.

---

## Approach 2: Greedy with Monotonic Stack (Optimal)

### **Core Idea**
- To get the smallest number, remove digits that are larger than the next digit, as they contribute to a bigger number.
- Use a **stack** to maintain a monotonically increasing sequence of digits:
  - When a new digit is smaller than the top of the stack, pop from the stack and decrement `k`.
  - Push the current digit onto the stack if `k` > 0 or if it maintains the monotonic property.
- After processing all digits, if `k > 0`, remove from the end.

### **Algorithm**
1. Initialize an empty stack.
2. Iterate through each digit in `num`:
   - While `k > 0` and the stack is not empty, and the top of the stack is greater than the current digit:
     - Pop the top element.
     - Decrement `k`.
   - Push the current digit onto the stack.
3. If `k > 0`, remove `k` digits from the end of the stack.
4. Remove leading zeros from the resulting number.
5. Return the number as a string; if empty, return `"0"`.

### **Java Code**
```java
public String removeKdigits(String num, int k) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char digit : num.toCharArray()) {
        while (k > 0 && !stack.isEmpty() && stack.peekLast() > digit) {
            stack.pollLast();
            k--;
        }
        stack.offerLast(digit);
    }

    // Remove remaining digits from the end if k > 0
    while (k > 0 && !stack.isEmpty()) {
        stack.pollLast();
        k--;
    }

    // Remove leading zeros
    StringBuilder result = new StringBuilder();
    boolean leadingZero = true;
    for (char ch : stack) {
        if (leadingZero && ch == '0') continue;
        leadingZero = false;
        result.append(ch);
    }

    return result.length() == 0 ? "0" : result.toString();
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — each digit is pushed and popped at most once.
- **Space Complexity:** *O(n)* — for the stack to store digits.

### **Dry Run**
Input: `num = "1432219"`, `k = 3`

| Step | Stack (Before) | Current Digit | Action | Stack (After) | `k` | Explanation                                          |
|-------|----------------|----------------|---------|--------------|-----|------------------------------------------------------|
| 1     | []             | '1'            | push    | ['1']        | 3   | Initialize with '1'                                  |
| 2     | ['1']          | '4'            | push    | ['1','4']    | 3   | '4' > '1', no removal                                |
| 3     | ['1','4']      | '3'            | pop '4', decrement k=2 | ['1'] | 2 | '4' > '3', pop '4'                                 |
|       |                |                | push '3'| ['1','3']    |     | '3' < '4', stop popping                              |
| 4     | ['1','3']      | '2'            | pop '3', k=1 | ['1'] | 1 | '3' > '2', pop '3'                                 |
|       |                |                | push '2'| ['1','2'] |     | '2' < '3'                                          |
| 5     | ['1','2']      | '2'            | push    | ['1','2','2']| 1 | Equal digits, push                                |
| 6     | ['1','2','2']  | '1'            | pop '2', k=0 | ['1','2'] | 0 | '2' > '1', pop '2'                                 |
|       |                |                | push '1'| ['1','2','1']| | Push '1'                                          |
| 7     | ['1','2','1']  | '9'            | push    | ['1','2','1','9']| | No popping since k=0                            |

- Final stack: `['1', '2', '1', '9']` -> number `"1219"`.

---

## **Summary Table**

| Approach                                  | Time Complexity | Space Complexity | Comments                                              |
|-------------------------------------------|----------------|------------------|-------------------------------------------------------|
| Brute Force                              | O(C(n, k) * n) | O(n)             | Exponential, impractical for large inputs           |
| Greedy Monotonic Stack (Optimal)         | O(n)          | O(n)             | Efficient, linear time solution                      |

---

## **Final Tips for Interviews**
- The greedy stack approach is the most optimal and widely used.
- Focus on understanding how removing larger digits when a smaller digit appears minimizes the overall number.
- Remember to handle edge cases:
  - Leading zeros after removal.
  - When `k` equals the length of `num`.
  - When `num` contains only zeros or is already minimal.

---

**Happy coding!**
