# sqrt(x))

This guide covers various approaches to solve the problem of finding the **integer square root of a non-negative integer x** (i.e., the largest integer `r` such that `r * r <= x`). It is structured for interview prep and quick revision, progressing from brute-force to optimal solutions.

---

## **Approach 1: Brute Force**

### **Core Idea:**

Try all integers starting from 0 upwards until the square exceeds `x`. The last valid number before exceeding `x` is the answer.

---

### **Algorithm:**

1. Initialize a variable `i` to 0.
2. Increment `i` by 1 in each iteration.
3. Check if `i * i` is less than or equal to `x`. If yes, continue.
4. Once `i * i` exceeds `x`, return `i - 1` as the result.

---

### **Java Code:**

```java
public int mySqrt(int x) {
    int i = 0;
    while ((long)i * i <= x) {
        i++;
    }
    return i - 1;
}

```

> Note: Use (long)i * i to prevent integer overflow.
> 

---

### **Complexity Analysis:**

- **Time Complexity:** **O(√x)**, since in the worst case, we iterate up to the square root of `x`.
- **Space Complexity:** **O(1)**, only constant extra space.

---

### **Dry Run:**

| Input `x` | i | i*i | Condition (`i*i <= x`) | Loop continues? | Result (`i-1`) |
| --- | --- | --- | --- | --- | --- |
| 16 | 0 | 0 | true | yes | -- |
| 1 | 1 | 1 | true | yes | -- |
| 2 | 2 | 4 | false (4 > 2) | no | 2 - 1 = 1 |

**Output:** 1

---

## **Approach 2: Binary Search (Optimal Solution)**

### **Core Idea:**

Use binary search to efficiently find the largest integer `r` such that `r * r <= x`. Since the square root is monotonic, binary search reduces the search space logarithmically.

---

### **Algorithm:**

1. Set `low = 0`, `high = x` (or `x/2 + 1` for optimization).
2. While `low <= high`:
    - Find `mid = low + (high - low) / 2`.
    - Calculate `mid * mid`.
    - If `mid * mid` equals `x`, return `mid`.
    - If `mid * mid` is less than `x`, move `low` to `mid + 1`.
    - Else, move `high` to `mid - 1`.
3. When the loop ends, `high` will be at the position of the floor of the square root.

---

### **Java Code:**

```java
public int mySqrt(int x) {
    if (x == 0 || x == 1) return x;

    long low = 0, high = x;
    while (low <= high) {
        long mid = low + (high - low) / 2;
        long square = mid * mid;

        if (square == x) {
            return (int) mid;
        } else if (square < x) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return (int) high;
}

```

> Note: Use long for mid * mid to prevent overflow.
> 

---

### **Complexity Analysis:**

- **Time Complexity:** **O(log x)**, due to binary search halving the search space each iteration.
- **Space Complexity:** **O(1)**.

---

### **Dry Run:**

| Input `x` | low | high | mid | mid*mid | Comparison | Next step | Final answer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 8 | 0 | 8 | 4 | 16 | 16 > 8 (too high) | high = 3 |  |
|  | 0 | 3 | 1 | 1 | 1 < 8 | low = 2 |  |
|  | 2 | 3 | 2 | 4 | 4 < 8 | low = 3 |  |
|  | 3 | 3 | 3 | 9 | 9 > 8 (too high) | high = 2 | **Result: 2** |

---

## **Summary Table**

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | Increment from 0 until `i*i > x` | O(√x) | O(1) | Simple but slow for large `x`. |
| Binary Search | Use binary search to find the maximum `r` with `r*r <= x` | O(log x) | O(1) | Most efficient for large inputs. |

---

## **Final Tips:**

- Always consider overflow when dealing with `mid * mid` or `i * i`.
- Use binary search for optimal performance.
- Handle edge cases: `x = 0` and `x = 1`.

---

This concludes the comprehensive revision note for the **sqrt(x)** problem. Use these approaches to efficiently solve and optimize your solutions during interviews!
