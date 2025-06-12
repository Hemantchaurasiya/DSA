# Grumpy Bookstore Owner

This note provides a comprehensive overview of various approaches to solving the "Grumpy Bookstore Owner" problem, progressing from brute-force to the most optimal solution. It is structured to facilitate quick revision and interview preparation.

---

## Problem Summary
Given:
- An array `customers[]` where `customers[i]` is the number of customers at minute `i`.
- An array `grumpy[]` where `grumpy[i]` is 1 if the owner is grumpy at minute `i`, else 0.
- An integer `X` representing the duration for which the owner can suppress their grumpiness (using a technique).

**Goal:** Maximize the number of satisfied customers by choosing **one** window of size `X` during which the owner suppresses grumpiness.

---

## Approach 1: Brute Force

### **Core Idea:**
Try all possible windows of size `X` to find the one that results in the maximum increase in satisfied customers.

---

### **Algorithm:**

1. Calculate the total number of satisfied customers (when `grumpy[i] == 0`).
2. For each possible window of size `X`:
   - Calculate the number of additional customers who would become satisfied if the owner suppresses grumpiness during this window.
   - Keep track of the maximum additional satisfied customers.
3. Sum the base satisfied customers and the maximum additional customers from the best window.

---

### **Java Code:**
```java
public int maxSatisfied(int[] customers, int[] grumpy, int X) {
    int totalSatisfied = 0;
    int n = customers.length;

    // Step 1: Calculate base satisfied customers
    for (int i = 0; i < n; i++) {
        if (grumpy[i] == 0) {
            totalSatisfied += customers[i];
        }
    }

    int maxAdditional = 0;
    // Step 2: Try all windows of size X
    for (int start = 0; start <= n - X; start++) {
        int additional = 0;
        for (int i = start; i < start + X; i++) {
            if (grumpy[i] == 1) {
                additional += customers[i];
            }
        }
        maxAdditional = Math.max(maxAdditional, additional);
    }

    // Total maximum satisfied customers
    return totalSatisfied + maxAdditional;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n * X)** — For each window, we iterate over `X` elements, and there are (`n - X + 1`) windows.

- **Space Complexity:**  
  **O(1)** — No extra space apart from variables.

---

### **Dry Run:**

**Input:**
```plaintext
customers = [1, 0, 1, 2, 1, 1, 7, 5]
grumpy =    [0, 1, 0, 1, 0, 1, 0, 1]
X = 3
```

| Step | Description | Variables / Computation |
|-------|--------------|-------------------------|
| 1     | Calculate base satisfied customers (grumpy=0) | sum = 1 + 0 + 1 + 0 + 1 + 0 + 7 + 5 = 15, but only when grumpy=0 |  
| 2     | Total satisfied: positions 0, 2, 4, 6 | totalSatisfied = 1 + 1 + 1 + 7 = 10 |  
| 3     | Try all windows of size 3: | |  
|       | Window [0-2]: customers = [1, 0, 1], grumpy = [0,1,0] | additional = 0 + 1 + 0 = 1 |  
|       | Window [1-3]: customers = [0, 1, 2], grumpy = [1,0,1] | additional = 0 + 0 + 2 = 2 |  
|       | Window [2-4]: customers = [1, 2, 1], grumpy = [0,1,0] | additional = 1 + 2 + 0 = 3 |  
|       | Window [3-5]: customers = [2, 1, 1], grumpy = [1,0,1] | additional = 2 + 0 + 1 = 3 |  
|       | Window [4-6]: customers = [1, 1, 7], grumpy = [0,1,0] | additional = 1 + 1 + 0 = 2 |  
|       | Window [5-7]: customers = [1, 7, 5], grumpy = [1,0,1] | additional = 1 + 0 + 5 = 6 |  
| 4     | Max additional = 6 (window [5-7]) | Final answer = totalSatisfied + 6 = 10 + 6 = 16 |

---

## Approach 2: Sliding Window Optimization (Most Efficient)

### **Core Idea:**
Use a sliding window of size `X` to compute the additional satisfied customers dynamically, avoiding recomputation for each window by subtracting the element leaving the window and adding the new element entering.

---

### **Algorithm:**

1. Calculate the base satisfied customers (when `grumpy[i] == 0`).
2. Initialize a variable `additional` to store the sum of customers in the first window where `grumpy[i] == 1`.
3. Use a sliding window to update `additional`:
   - Remove the customers at the start of the previous window if `grumpy[start] == 1`.
   - Add the customers at the end of the current window if `grumpy[end] == 1`.
4. Track the maximum `additional` during window sliding.
5. Final result: base satisfied customers + maximum `additional`.

---

### **Java Code:**
```java
public int maxSatisfied(int[] customers, int[] grumpy, int X) {
    int totalSatisfied = 0;
    int n = customers.length;
    int additional = 0;

    // Calculate initial base satisfied customers
    for (int i = 0; i < n; i++) {
        if (grumpy[i] == 0) {
            totalSatisfied += customers[i];
        }
    }

    // Initialize the first window
    for (int i = 0; i < X; i++) {
        if (grumpy[i] == 1) {
            additional += customers[i];
        }
    }

    int maxAdditional = additional;

    // Slide the window
    for (int start = 1; start <= n - X; start++) {
        int end = start + X - 1;

        // Remove the outgoing element
        if (grumpy[start - 1] == 1) {
            additional -= customers[start - 1];
        }

        // Add the incoming element
        if (grumpy[end] == 1) {
            additional += customers[end];
        }

        maxAdditional = Math.max(maxAdditional, additional);
    }

    return totalSatisfied + maxAdditional;
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)** — Single pass to compute initial sums and sliding window updates.

- **Space Complexity:**  
  **O(1)** — Only variables used, no extra data structures.

---

### **Dry Run (Same input as above):**

| Step | Description | Variables / Computation |
|-------|--------------|-------------------------|
| 1     | Base satisfied: positions 0, 2, 4, 6 | totalSatisfied = 10 |  
| 2     | Initial window (0-2): grumpy=1 at position 1, customers=0 | additional = 0 |  
| 3     | Slide to window (1-3): | remove position 0 (grumpy=0), no change; add position 3 (grumpy=1, customers=2) | additional = 0 + 2 = 2 |  
| 4     | Slide to window (2-4): remove position 1 (grumpy=1, customers=0), subtract 0; add position 4 (grumpy=0), no addition | additional = 2 |  
| 5     | Slide to window (3-5): remove position 2 (grumpy=0), no change; add position 5 (grumpy=1, customers=1) | additional = 2 + 1 = 3 |  
| 6     | Slide to window (4-6): remove position 3 (grumpy=1, customers=2), subtract 2; add position 6 (grumpy=0), no addition | additional = 3 - 2 = 1 |  
| 7     | Slide to window (5-7): remove position 4 (grumpy=0), no change; add position 7 (grumpy=1, customers=5) | additional = 1 + 5 = 6 |  
| 8     | Max additional = 6 | Final answer = 10 + 6 = 16 |

---

## Summary of Approaches:

| Approach | Time Complexity | Space Complexity | Notes |
|------------|-------------------|--------------------|--------|
| Brute Force | **O(n * X)** | **O(1)** | Simple but inefficient for large `n` and `X`. |
| Sliding Window | **O(n)** | **O(1)** | Most optimal, preferred for large inputs. |

---

## Final Tips:
- Always analyze whether a sliding window can optimize brute-force solutions.
- Be mindful of calculating base satisfaction separately to simplify the problem.
- Use prefix sums or sliding window techniques to optimize time complexity.

---

This structured guide should help you understand the problem deeply and recall the optimal approach during interviews.
