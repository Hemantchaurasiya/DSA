# Arranging Coins

---

## Problem Statement

Given `n` coins, arrange them in a staircase shape, where the first row has 1 coin, the second row has 2 coins, the third row has 3 coins, and so on. Find the total number of complete rows that can be formed with `n` coins.

**Example:**

```
Input: n = 5
Output: 2
Explanation: The staircase can be formed with:
  Row 1: 1 coin
  Row 2: 2 coins
Remaining coin (1) is not enough to form the third row (which needs 3 coins).

```

---

## Approach 1: Brute Force

### Core Idea

Iteratively subtract the number of coins required for each row from `n` until there aren't enough coins to form the next row.

### Algorithm

1. Initialize a variable `rows` to 0.
2. Loop while `n` is greater than or equal to the number of coins needed for the next row (`rows + 1`).
3. In each iteration:
    - Subtract `rows + 1` from `n`.
    - Increment `rows` by 1.
4. When the loop terminates, `rows` represents the maximum number of complete rows.

### Java Code

```java
public int arrangeCoins(int n) {
    int rows = 0;
    while (n >= rows + 1) {
        n -= (rows + 1);
        rows++;
    }
    return rows;
}

```

### Complexity Analysis

- **Time Complexity:** O(k), where `k` is the number of rows formed. In the worst case, this is approximately `O(√n)` because the sum of the first `k` natural numbers is `k(k+1)/2`, which is `O(n)`. But since each iteration subtracts an increasing number, it is roughly `O(√n)`.
- **Space Complexity:** O(1), as only a few variables are used.

### Dry Run

| Input | n = 5 |
| --- | --- |
| Iteration 1 | n = 5, rows = 0, subtract 1 → n=4, rows=1 |
| Iteration 2 | n=4, rows=1, subtract 2 → n=2, rows=2 |
| Iteration 3 | n=2, rows=2, need 3 but n=2, stop |
| **Result** | 2 rows |

---

## Approach 2: Mathematical Formula (Using Quadratic Equation)

### Core Idea

The total number of coins needed to form `k` complete rows is `k(k+1)/2`. To find the maximum `k` such that `k(k+1)/2 <= n`.

### Algorithm

1. Recognize the inequality: `k(k+1)/2 <= n`.
2. Rearrange to standard quadratic form: `k^2 + k - 2n <= 0`.
3. Solve for `k` using the quadratic formula:
\[
k = \frac{-1 + \sqrt{1 + 8n}}{2}
\]
4. Take the floor of this value as the maximum complete rows.

### Java Code

```java
public int arrangeCoins(int n) {
    return (int) (Math.sqrt(2L * n + 0.25) - 0.5);
}

```

*(Note: Using `2L * n` to avoid integer overflow, and `Math.sqrt` for square root calculation.)*

### Complexity Analysis

- **Time Complexity:** O(1), since the operation involves a constant number of calculations.
- **Space Complexity:** O(1).

### Dry Run

| Input | n=5 |
| --- | --- |
| Compute `k = (√(2*5 + 0.25) - 0.5)` | `k = (√(10 + 0.25) - 0.5) ≈ (√10.25 - 0.5) ≈ (3.2 - 0.5) = 2.7` |
| Floor of `k` | 2 |
| **Result** | 2 rows |

---

## Summary Table

| Approach | Core Idea | Algorithm | Code | Time Complexity | Space Complexity | Dry Run Example |
| --- | --- | --- | --- | --- | --- | --- |
| **Brute Force** | Incrementally subtract coins for each row until insufficient | Loop subtracting 1, 2, 3... | Provided above | O(√n) | O(1) | n=5 → 2 rows |
| **Mathematical Formula** | Use quadratic formula to directly compute maximum rows | Solve `k(k+1)/2 <= n` | Provided above | O(1) | O(1) | n=5 → 2 rows |

---

## Final Notes

- For large `n`, the mathematical approach is preferable due to its constant time complexity.
- The brute-force approach is simple and intuitive, suitable for understanding the problem.
- Always consider integer overflow in calculations involving large `n`.

---

**End of Revision Note**
