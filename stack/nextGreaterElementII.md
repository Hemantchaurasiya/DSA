# Next Greater Element II

---

## Problem Overview
Given a **circular array** `nums`, find the **Next Greater Element** for each element:
- The **Next Greater Element** of `nums[i]` is the first element **to the right** of `i` in the circular array that is greater than `nums[i]`.
- Because the array is circular, after reaching the end, you wrap around to the beginning.
- If no such element exists, output `-1`.

**Example:**
- Input: `[1, 2, 1]`
- Output: `[2, -1, 2]`
- Explanation:
  - For `1` (index 0): next greater is `2`.
  - For `2` (index 1): no greater element after it in circular manner → `-1`.
  - For `1` (index 2): next greater is `2` (wrapping around).

---

## Approach 1: Brute Force

### **Core Idea**
For each element, scan the array circularly to find the next greater element.

### **Algorithm**
1. For each element `nums[i]`:
   - Loop through the array starting from `i+1` to `i + n` (wrapping around via modulo):
     - If an element greater than `nums[i]` is found, record it.
     - If no greater element is found after a full pass, record `-1`.

### **Java Code**
```java
public int[] nextGreaterElements(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];
    for (int i = 0; i < n; i++) {
        result[i] = -1;
        for (int j = 1; j < n; j++) {
            int idx = (i + j) % n;
            if (nums[idx] > nums[i]) {
                result[i] = nums[idx];
                break;
            }
        }
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n^2)*, for each element, possibly scanning the entire array.
- **Space Complexity:** *O(n)*, for the result array.

### **Dry Run**
Input: `[1, 2, 1]`

| i | Starting element | Inner loop: check indices | Result for `i` | Explanation                          |
|---|--------------------|------------------------------|----------------|--------------------------------------|
| 0 | 1                  | check index 1 (2) → 2 > 1? yes → 2 | 2            | Next greater for 1 is 2             |
| 1 | 2                  | check index 2 (1) → 1 > 2? no; check index 0 (1) → 1 > 2? no → -1 | -1           | No greater element                   |
| 2 | 1                  | check index 0 (1) → 1 > 1? no; check index 1 (2) → 2 > 1? yes → 2 | 2            | Next greater for 1 is 2             |

---

## Approach 2: Using Monotonic Stack (Most Efficient)

### **Core Idea**
Use a **monotonically decreasing stack** to process the array twice (simulate circularity):
- Traverse the array twice from the end to the beginning.
- Maintain a stack of elements for which we haven't yet found the next greater element.
- For each element:
  - Pop elements from the stack that are less than or equal to the current element.
  - If the stack is not empty, the top is the next greater element.
  - Push the current element onto the stack.
- Use a result array initialized with `-1` for default, and update during traversal.

### **Algorithm**
1. Initialize an empty stack.
2. Initialize a result array with `-1`.
3. Loop from `2 * n - 1` down to `0` (simulate circular array):
   - While the stack is not empty and the top is less than or equal to the current element, pop from the stack.
   - If the current index is within `0` to `n-1`, update `result[i]`:
     - If the stack top exists, that is the next greater element; otherwise, `-1`.
   - Push the current element onto the stack.
4. Return the `result` array.

### **Java Code**
```java
public int[] nextGreaterElements(int[] nums) {
    int n = nums.length;
    int[] result = new int[n];
    Arrays.fill(result, -1);
    Deque<Integer> stack = new ArrayDeque<>();
    
    // Traverse twice for circular array
    for (int i = 2 * n - 1; i >= 0; i--) {
        int current = nums[i % n];
        while (!stack.isEmpty() && stack.peek() <= current) {
            stack.pop();
        }
        if (i < n) {
            result[i] = stack.isEmpty() ? -1 : stack.peek();
        }
        stack.push(current);
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, each element pushed and popped at most once.
- **Space Complexity:** *O(n)*, for the stack and result array.

### **Dry Run**
Input: `[1, 2, 1]`

| i (reverse) | `nums[i % n]` | Stack before | Pop elements | Stack after | Update result | Explanation                       |
|--------------|--------------|--------------|--------------|--------------|----------------|----------------------------------|
| 5            | 1            | []           | -            | [1]          | -              | First pass, push `1`            |
| 4            | 2            | [1]          | pop 1 (≤ 2)  | []           | result[2]=1    | Pop `1`, push `2`, update index 2 |
| 3            | 1            | [2]          | pop 2? no     | [2, 1]       | result[1]=-1  | Push `1`, no update needed      |
| 2            | 1            | [2, 1]       | pop 1 (≤ 1)? no, pop 2? no | [2, 1, 1] | result[0]=2 | For index 0, next greater is 2 |

Result: `[2, -1, 2]`

---

## **Summary Table**

| Approach                        | Time Complexity | Space Complexity | Comments                                              |
|--------------------------------|----------------|------------------|--------------------------------------------------------|
| Brute Force                   | O(n^2)        | O(n)             | Simple but inefficient for large inputs              |
| Monotonic Stack (Optimal)     | O(n)          | O(n)             | Efficient, handles circular array with a single pass |

---

## **Final Tips for Interviews**
- For circular array problems, consider traversing twice to simulate wrap-around.
- Use a monotonic stack for optimal solutions, especially for "Next Greater" type problems.
- Remember to initialize result arrays with `-1` to handle cases where no greater element exists.
- Practice similar problems like "Next Greater Element II," "Next Smaller Element," and circular variations.

---

**Happy coding!**
