# Asteroid Collision

---

## Problem Overview
Given an array of integers representing asteroids moving along a line, where:
- Positive values indicate asteroids moving to the right.
- Negative values indicate asteroids moving to the left.

When two asteroids collide:
- The smaller one (by absolute value) explodes.
- If both are the same size, both explode.
- The larger one survives and continues.

**Goal:** Return the state of the asteroids after all collisions.

---

## Approach 1: Brute Force (Simulation with Repeated Checks)

### **Core Idea**
- Simulate the process by repeatedly checking for collisions.
- For each asteroid moving right, check subsequent asteroids moving left to detect collisions.
- Remove or keep asteroids based on collision rules.

### **Algorithm**
1. Use a list or array to simulate the sequence of asteroids.
2. Repeat until no changes occur:
   - Traverse the list:
     - When encountering an asteroid moving right (`>0`) followed by an asteroid moving left (`<0`), they collide.
     - Compare their sizes:
       - Remove the smaller one.
       - If equal, remove both.
     - Continue until the end of the list.
3. When no more collisions are possible, the list is the final state.

### **Limitations**
- Inefficient due to repeated traversal and modifications.
- Time complexity can be poor in the worst case.

---

## Approach 2: Stack-Based Solution (Most Common and Efficient)

### **Core Idea**
- Use a stack to model the process.
- Push right-moving asteroids onto the stack.
- When a left-moving asteroid appears, compare it with the top of the stack:
  - If the top is moving right and smaller, pop it.
  - If they are equal, pop the top and discard the current.
  - Repeat until no collision or the stack is empty.
- Push left-moving asteroid if it survives all collisions.

### **Algorithm**
1. Initialize an empty stack.
2. Iterate over each asteroid:
   - If the asteroid moves right (`>0`), push onto the stack.
   - If it moves left (`<0`):
     - While the stack is not empty and the top asteroid is moving right:
       - Compare sizes.
       - Pop smaller ones; if equal, pop top and discard current.
       - If current is smaller, discard it.
     - If no collision occurs, push the current asteroid.
3. The stack now contains the final state.

### **Java Code**
```java
import java.util.Stack;

public class AsteroidCollision {
    public int[] asteroidCollision(int[] asteroids) {
        Stack<Integer> stack = new Stack<>();
        for (int asteroid : asteroids) {
            boolean destroyed = false;
            while (!stack.isEmpty() && asteroid < 0 && stack.peek() > 0) {
                if (Math.abs(asteroid) > Math.abs(stack.peek())) {
                    stack.pop(); // Top asteroid destroyed
                } else if (Math.abs(asteroid) == Math.abs(stack.peek())) {
                    stack.pop(); // Both destroyed
                    destroyed = true;
                    break;
                } else {
                    destroyed = true; // Current asteroid destroyed
                    break;
                }
            }
            if (!destroyed) {
                stack.push(asteroid);
            }
        }
        // Convert stack to array
        int[] result = new int[stack.size()];
        for (int i = result.length - 1; i >= 0; i--) {
            result[i] = stack.pop();
        }
        return result;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)* — each asteroid is pushed and popped at most once.
- **Space Complexity:** *O(n)* — in the worst case, all asteroids are stored in the stack.

### **Dry Run**
Input: `[5, 10, -5]`

| Step | Stack (before) | Current asteroid | Action | Stack (after) | Explanation                    |
|-------|----------------|---------------------|---------|--------------|--------------------------------|
| 1     | []             | 5                   | Push    | [5]          | Moving right                     |
| 2     | [5]            | 10                  | Push    | [5, 10]      | Moving right                     |
| 3     | [5, 10]        | -5                  | Collision with 10 (top) | Pop 10 | -5 < 10, -5 destroyed, stop |
| Final | [5]            |                     | End     | [5]          | Remaining asteroid 5 |

Result: `[5]`

---

## Approach 3: Optimized Approach with In-place Array (Less Common)

### **Core Idea**
- Use two pointers and modify the input array in-place to simulate the process.
- Similar to the stack approach but avoids extra space.
- Less readable and more complex, generally not preferred.

---

## **Summary Table**

| Approach                        | Time Complexity | Space Complexity | Comments                                              |
|---------------------------------|----------------|------------------|-------------------------------------------------------|
| Brute Force (Repeated Simulation)| O(n^2)        | O(1) or O(n)     | Inefficient for large inputs                         |
| Stack-Based Solution            | O(n)           | O(n)             | Most common, efficient, handles nested collisions   |
| In-place Array Method           | O(n)           | O(1)             | Slightly optimized in space, complex implementation |

---

## **Final Tips for Interviews**
- Use the stack approach for clarity and efficiency.
- Carefully handle cases where multiple collisions occur in sequence.
- Test with various inputs: all moving right, all moving left, mixed sizes, and equal sizes.
- Be mindful of edge cases like empty arrays or single elements.

---

**Happy coding!**
