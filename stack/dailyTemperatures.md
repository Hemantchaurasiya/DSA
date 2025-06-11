# Daily Temperatures

---

## Problem Overview
Given an array `temperatures` representing daily temperatures, for each day, determine **how many days** you have to wait until a warmer temperature. If there is no future day with a warmer temperature, record `0`.

**Example:**
- Input: `[73, 74, 75, 71, 69, 72, 76, 73]`
- Output: `[1, 1, 4, 2, 1, 1, 0, 0]`
- Explanation:
  - For day 0 (73): next warmer is day 1 (74) → 1 day.
  - For day 2 (75): no warmer days ahead → 0.

---

## Approach 1: Brute Force

### **Core Idea**
For each day, scan forward to find the next day with a higher temperature.

### **Algorithm**
1. Loop through each element `i` in `temperatures`.
2. For each `i`, scan forward from `i+1` to the end:
   - If a temperature higher than `temperatures[i]` is found, record the difference `(j - i)`.
   - If no higher temperature is found, record `0`.

### **Java Code**
```java
public int[] dailyTemperatures(int[] temperatures) {
    int n = temperatures.length;
    int[] result = new int[n];
    for (int i = 0; i < n; i++) {
        result[i] = 0; // default if no warmer day found
        for (int j = i + 1; j < n; j++) {
            if (temperatures[j] > temperatures[i]) {
                result[i] = j - i;
                break;
            }
        }
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n^2)*, as for each element, we may scan the entire remaining array.
- **Space Complexity:** *O(n)* for the output array.

### **Dry Run**
Input: `[73, 74, 75, 71, 69, 72, 76, 73]`

| i | Temperature | Inner loop scans | Result | Explanation                                |
|---|--------------|------------------|---------|--------------------------------------------|
| 0 | 73           | finds 74 at j=1 → 1 | 1       | Wait 1 day for warmer temperature          |
| 1 | 74           | finds 75 at j=2 → 1 | 1       | Wait 1 day                                |
| 2 | 75           | no higher after j=3 to 7 | 0       | No warmer day ahead                        |
| 3 | 71           | finds 72 at j=5 → 2 | 2       | Wait 2 days                               |
| 4 | 69           | finds 72 at j=5 → 1 | 1       | Wait 1 day                                |
| 5 | 72           | finds 76 at j=6 → 1 | 1       | Wait 1 day                                |
| 6 | 76           | no warmer after j=7 | 0       | No warmer day ahead                        |
| 7 | 73           | no warmer after j=8 | 0       | No warmer day ahead                        |

---

## Approach 2: Using a Monotonic Stack (Optimal)

### **Core Idea**
Use a **stack** to keep track of indices of days with temperatures that haven't yet found a warmer future day:
- Traverse the array from **right to left**.
- While the top of the stack refers to a day with a temperature less than or equal to the current day's temperature, pop it.
- If the stack is not empty after popping, the top of the stack indicates the next day with a warmer temperature.
- Push the current day index onto the stack.
- This ensures we efficiently find the next warmer day for each element.

### **Algorithm**
1. Initialize an empty stack to store indices.
2. Create a result array filled with `0`.
3. Traverse the array from right to left:
   - While the stack is not empty and `temperatures[stack.peek()] <= temperatures[i]`, pop from the stack.
   - If the stack isn't empty, `result[i] = stack.peek() - i`.
   - Push `i` onto the stack.
4. Return the result array.

### **Java Code**
```java
public int[] dailyTemperatures(int[] temperatures) {
    int n = temperatures.length;
    int[] result = new int[n];
    Deque<Integer> stack = new ArrayDeque<>();
    
    for (int i = n - 1; i >= 0; i--) {
        while (!stack.isEmpty() && temperatures[stack.peek()] <= temperatures[i]) {
            stack.pop();
        }
        if (!stack.isEmpty()) {
            result[i] = stack.peek() - i;
        }
        stack.push(i);
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, each index is pushed and popped at most once.
- **Space Complexity:** *O(n)* for the stack and the result array.

### **Dry Run**
Input: `[73, 74, 75, 71, 69, 72, 76, 73]`

| i | Temperature | Stack before | Popped Elements | Stack after | Result at i | Explanation                          |
|---|--------------|----------------|-----------------|--------------|--------------|--------------------------------------|
| 7 | 73           | []             | -               | [7]        | 0            | Push index 7                        |
| 6 | 76           | [7]            | pop 7 (73 ≤ 76) | []           | 0            | Pop 7, push 6                     |
| 5 | 72           | [6]            | no pop          | [6,5]       | 6-5=1        | Next warmer at index 6 (76)        |
| 4 | 69           | [6,5]          | no pop          | [6,5,4]     | 0            | No warmer ahead, push 4           |
| 3 | 71           | [6,5,4]        | pop 4 (69 ≤ 71) | [6,5]       | 5-3=2        | Next warmer at index 5 (72)        |
| 2 | 75           | [6,5]          | pop 5 (72 ≤ 75), pop 6 (76 ≤ 75)? no | [2]     | 0            | No warmer ahead, push 2           |
| 1 | 74           | [2]            | no pop          | [2,1]       | 2-1=1        | Next warmer at index 2 (75)        |
| 0 | 73           | [2,1]          | no pop          | [2,1,0]     | 1-0=1        | Next warmer at index 1 (74)        |

Final result: `[1, 1, 0, 2, 1, 1, 0, 0]`

---

## **Summary Table**

| Approach                     | Time Complexity | Space Complexity | Comments                                              |
|------------------------------|----------------|------------------|--------------------------------------------------------|
| Brute Force                  | O(n^2)        | O(n)             | Simple but inefficient for large inputs              |
| Monotonic Stack (Optimal)    | O(n)          | O(n)             | Efficient, single pass solution for large inputs    |

---

## **Final Tips for Interviews**
- Use a **monotonically decreasing stack** to optimize for linear time.
- Think backwards when dealing with "next greater" or similar "future" problems.
- Initialize the result array with `0` to handle cases with no warmer days.
- Practice similar problems like "Next Greater Element," "Next Smaller Element," and variations with circular or linear arrays.

---

**Happy coding!**
