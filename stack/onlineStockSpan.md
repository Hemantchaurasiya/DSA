# Online Stock Span

---

## Problem Overview
Given a series of daily stock prices, the **Stock Span Problem** asks for each day's stock price, to determine the **span**: the maximum number of consecutive days (up to and including the current day) the price was less than or equal to today's price.

**Example:**
- Input: `[100, 80, 60, 70, 60, 75, 85]`
- Output: `[1, 1, 1, 2, 1, 4, 6]`

**Explanation:**
- For `100`: span is 1 (only today)
- For `80`: span is 1
- For `60`: span is 1
- For `70`: span is 2 (current and previous day with price 60)
- For `60`: span is 1
- For `75`: span is 4 (days with prices 70, 60, 70, 60)
- For `85`: span is 6 (all previous days with prices less than or equal to 85)

---

## Approach 1: Brute Force

### **Core Idea**
For each day's price, look backward to count how many consecutive previous days had prices less than or equal to today's price.

### **Algorithm**
1. Loop through each day's price `i`.
2. For each `i`, initialize a counter to 1.
3. Move backward from `i-1` to `0`:
   - If the previous day's price is less than or equal to today's price, increment the counter.
   - Else, stop.
4. Store the counter as the span for day `i`.

### **Java Code**
```java
public int[] calculateSpan(int[] prices) {
    int n = prices.length;
    int[] spans = new int[n];
    for (int i = 0; i < n; i++) {
        int count = 1;
        for (int j = i - 1; j >= 0; j--) {
            if (prices[j] <= prices[i]) {
                count++;
            } else {
                break;
            }
        }
        spans[i] = count;
    }
    return spans;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n^2)*, since for each element, we may look back through all previous elements.
- **Space Complexity:** *O(n)* for the output array.

### **Dry Run**
Input: `[100, 80, 60, 70, 60, 75, 85]`

| i | Price | Backward scan | Span | Explanation                                              |
|---|---------|---------------|-------|----------------------------------------------------------|
| 0 | 100     | -             | 1     | Only today                                              |
| 1 | 80      | stops at 100 (>80) | 1 | Previous day's price > current, stop                   |
| 2 | 60      | stops at 80 (>60) | 1 | Stop at first previous day, price 80 > 60             |
| 3 | 70      | 60 ≤ 70 (count=2), stop at 80 (>70) | 2 | Two days: 60, 70; stop at 80                        |
| 4 | 60      | stop at 70 (>60) | 1 | Previous day 70 > 60, stop                            |
| 5 | 75      | 60 ≤ 75 (count=2), 70 ≤ 75 (count=3), stop at 80 (>75) | 4 | Days with 60, 70, 60, 75                          |
| 6 | 85      | 75 ≤ 85 (count=2), 70 ≤ 85 (3), 80 ≤ 85 (4), 100 > 85 - stop | 6 | All previous days except 100                     |

---

## Approach 2: Using a Monotonic Stack (Optimal)

### **Core Idea**
Use a **stack** to efficiently compute spans:
- Maintain a stack of indices whose prices are in decreasing order.
- For each day's price:
  - Pop from the stack while the top's price is less than or equal to the current price.
  - If the stack is empty, the span is the entire length from the beginning.
  - Else, the span is the difference between current index and the index at the top of the stack.
- Push current index onto the stack.

### **Algorithm**
1. Initialize an empty stack to store indices.
2. Create an array `spans` to store the result.
3. Loop over each day's price:
   - While the stack is not empty and `prices[stack.peek()] <= prices[i]`, pop the stack.
   - If the stack is empty, `spans[i] = i + 1`.
   - Else, `spans[i] = i - stack.peek()`.
   - Push current index onto the stack.
4. Return `spans`.

### **Java Code**
```java
public int[] calculateSpan(int[] prices) {
    int n = prices.length;
    int[] spans = new int[n];
    Deque<Integer> stack = new ArrayDeque<>();

    for (int i = 0; i < n; i++) {
        while (!stack.isEmpty() && prices[stack.peek()] <= prices[i]) {
            stack.pop();
        }
        spans[i] = stack.isEmpty() ? i + 1 : i - stack.peek();
        stack.push(i);
    }
    return spans;
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, each index is pushed and popped at most once.
- **Space Complexity:** *O(n)* for the stack and output array.

### **Dry Run**
Input: `[100, 80, 60, 70, 60, 75, 85]`

| i | Price | Stack before | Popped elements | Stack after | Span | Explanation                                    |
|---|--------|----------------|-----------------|--------------|-------|------------------------------------------------|
| 0 | 100    | []             | -               | [0]        | 1     | Stack empty, span is 1                       |
| 1 | 80     | [0]            | no pop          | [0,1]      | 1     | Price at index 0 > 80, span=1                |
| 2 | 60     | [0,1]          | no pop          | [0,1,2]    | 1     | Same as above                                |
| 3 | 70     | [0,1,2]        | pop 2 (60 ≤ 70), 1 (80 > 70) | [0,3]    | 3-3=2 | Pop 2 and 1; current index 3, previous index 0 |
| 4 | 60     | [0,3]          | no pop          | [0,3,4]    | 4-3=1 | Previous at index 3 (70) > 60             |
| 5 | 75     | [0,3,4]        | pop 4 (60 ≤ 75), pop 3 (70 ≤ 75) | [0,5] | 5-0=5 | Pop 4 and 3; current index 5, previous index 0 |
| 6 | 85     | [0,5]          | pop 5 (75 ≤ 85) | [6]        | 6-0=6 | Pop 5; current index 6; previous index 0 |

Result: `[1, 1, 1, 2, 1, 4, 6]`

---

## **Summary Table**

| Approach                     | Time Complexity | Space Complexity | Comments                                              |
|------------------------------|----------------|------------------|--------------------------------------------------------|
| Brute Force                  | O(n^2)        | O(n)             | Straightforward but inefficient for large inputs     |
| Monotonic Stack (Optimal)    | O(n)          | O(n)             | Efficient, single pass, ideal for large datasets     |

---

## **Final Tips for Interviews**
- Use a **stack** to solve span or "next greater" type problems efficiently.
- Maintain a decreasing stack to quickly find the span or next greater element.
- Remember to process elements in a single pass for optimal solutions.
- Practice variations such as "daily temperatures," "next greater element," and "stock span" problems.

---

**Happy coding!**
