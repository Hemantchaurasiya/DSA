# Largest Rectangle in Histogram

---

## Problem Overview
Given an array `heights` representing the heights of histogram bars, find the **area of the largest rectangle** that can be formed within the histogram.

**Example:**
- Input: `[2, 1, 5, 6, 2, 3]`
- Output: `10`

**Explanation:**
- The largest rectangle has height `2` and width `5` (covering bars with heights `[2, 1, 5, 6, 2]`), or the rectangle with height `5` and width `2` (bars 3 and 4). The maximum area is `10`.

---

## Approach 1: Brute Force

### **Core Idea**
For each bar, compute the largest rectangle with the bar as the smallest height:
- Expand to the left until a bar shorter than current is encountered.
- Expand to the right similarly.
- Calculate the area for each bar and keep track of the maximum.

### **Algorithm**
1. Loop through each bar `i`.
2. For each `i`, expand leftward until a shorter bar is found.
3. Expand rightward similarly.
4. Calculate the area: `(right - left - 1) * height[i]`.
5. Track the maximum area across all bars.

### **Java Code**
```java
public int largestRectangleArea(int[] heights) {
    int maxArea = 0;
    int n = heights.length;
    for (int i = 0; i < n; i++) {
        int left = i, right = i;
        while (left > 0 && heights[left - 1] >= heights[i]) {
            left--;
        }
        while (right < n - 1 && heights[right + 1] >= heights[i]) {
            right++;
        }
        int area = (right - left + 1) * heights[i];
        maxArea = Math.max(maxArea, area);
    }
    return maxArea;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n^2)*, as for each bar, expanding left and right can take up to O(n).
- **Space Complexity:** *O(1)*, as no extra significant space is used.

### **Dry Run**
Input: `[2, 1, 5, 6, 2, 3]`

| i | Height | Left expansion | Right expansion | Area | Max Area | Explanation                     |
|---|---------|------------------|-------------------|-------|------------|---------------------------------|
| 0 | 2       | stops at left 0  | extends to 0     | 2*1=2 | 2          | Only bar 0                     |
| 1 | 1       | extends to 0     | extends to 5    | 6*1=6 | 6          | Entire histogram                |
| 2 | 5       | stops at 2     | stops at 3     | 2*5=10 | 10       | Bars 2 and 3, heights 5 and 6  |
| 3 | 6       | stops at 3     | stops at 3     | 1*6=6 | 10       | Single bar 6                     |
| 4 | 2       | stops at 4     | stops at 5     | 2*2=4 | 10       | Bars 4 and 5                   |
| 5 | 3       | stops at 5     | stops at 5     | 1*3=3 | 10       | Single bar 3                     |

---

## Approach 2: Using a Monotonic Stack (Optimal)

### **Core Idea**
Use a **stack** to keep track of bars in increasing order of heights:
- When a bar is encountered that is lower than the top of the stack, it indicates the right boundary for the bar at the top.
- Pop from the stack and calculate areas for each popped bar, considering the current index as the right boundary.
- Push current bar indices onto the stack for future calculations.
- After processing all bars, pop remaining bars and compute areas similarly.

### **Algorithm**
1. Initialize an empty stack to store indices of bars.
2. Iterate through each bar `i`:
   - While the stack is not empty and `heights[i] < heights[stack.peek()]`, pop from the stack.
   - Calculate area for each popped bar:
     - Height = height of popped bar.
     - Width = current index `i` if the stack is empty after popping; else, `i - stack.peek() - 1`.
   - Push current index `i` onto the stack.
3. After the loop, pop remaining bars in the stack:
   - Calculate area similarly, with width as `n - stack.peek() - 1` if stack isn't empty, else `n`.

### **Java Code**
```java
public int largestRectangleArea(int[] heights) {
    int maxArea = 0;
    int n = heights.length;
    Deque<Integer> stack = new ArrayDeque<>();
    for (int i = 0; i <= n; i++) {
        int currentHeight = (i == n) ? 0 : heights[i];
        while (!stack.isEmpty() && currentHeight < heights[stack.peek()]) {
            int height = heights[stack.pop()];
            int width = stack.isEmpty() ? i : i - stack.peek() - 1;
            maxArea = Math.max(maxArea, height * width);
        }
        stack.push(i);
    }
    return maxArea;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, each bar is pushed and popped at most once.
- **Space Complexity:** *O(n)* for the stack.

### **Dry Run**
Input: `[2, 1, 5, 6, 2, 3]`

| i | Current Height | Stack before | Action | Stack after | Area calculation | Max Area | Explanation                              |
|---|------------------|----------------|---------|--------------|---------------------|------------|------------------------------------------|
| 0 | 2                | []             | push 0 | [0]          | -                   | 0          | push index 0 (height=2)                 |
| 1 | 1                | [0]            | pop 0 (2 ≥ 1), compute area: 2*1=2 | [] | 2 | Pop 0, height=2, width=1 (i=1, stack empty) |
|   |                  |                | push 1 | [1]          | -                   | 2 | push index 1 (height=1)                   |
| 2 | 5                | [1]            | push 2 | [1,2]        | -                   | 2 | push index 2 (height=5)                   |
| 3 | 6                | [1,2]          | push 3 | [1,2,3]      | -                   | 2 | push index 3 (height=6)                   |
| 4 | 2                | [1,2,3]        | pop 3 (6 ≥ 2), compute area: 6*(4-2-1)=6*1=6 | [1,2] | 6 | Pop 3, width=4-2-1=1, height=6 |
|   |                  |                | pop 2 (5 ≥ 2), compute area: 5*(4-1-1)=5*2=10 | [1] | 10 | Pop 2, width=4-1-1=2, height=5 |
|   |                  |                | push 4 | [1,4] | - | push index 4 (height=2)                   |
| 5 | 3                | [1,4]          | push 5 | [1,4,5] | - | push index 5 (height=3)                   |
| End | -              | -              | pop remaining | [] | compute areas for remaining bars | | process remaining in stack |

Final maximum area = 10.

---

## **Summary Table**

| Approach                     | Time Complexity | Space Complexity | Comments                                              |
|------------------------------|----------------|------------------|--------------------------------------------------------|
| Brute Force                  | O(n^2)        | O(1)             | Simple but inefficient for large inputs             |
| Monotonic Stack (Optimal)    | O(n)          | O(n)             | Efficient, single pass, ideal for large datasets     |

---

## **Final Tips for Interviews**
- Use a **monotonically increasing stack** to efficiently compute the largest rectangle.
- Push indices instead of heights to easily calculate widths.
- Always consider sentinel values (like appending a zero height at the end) to handle remaining bars.
- Practice variations such as "maximal rectangle in binary matrix" for more advanced problems.

---

**Happy coding!**
