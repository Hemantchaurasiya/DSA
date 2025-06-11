# Valid Perfect Square

This note covers multiple approaches to solving the **Valid Perfect Square** problem, ranging from brute-force to the most optimized solution. It aims to serve as a quick reference for interview preparation and revision.

---

## Problem Statement

Given a positive integer `num`, determine if it is a perfect square without using built-in square root functions.

---

## Approach 1: Brute Force

### Core Idea

Iterate through all numbers starting from 1 up to `num`, and check if any number squared equals `num`.

### Algorithm

1. Loop `i` from 1 to `num`.
2. For each `i`, check if `i * i == num`.
3. If yes, return `true`.
4. If the loop completes without a match, return `false`.

### Java Code

```java
public boolean isPerfectSquare(int num) {
    for (int i = 1; i <= num; i++) {
        if (i * i == num) {
            return true;
        }
        if (i * i > num) {
            break; // No need to continue if i^2 exceeds num
        }
    }
    return false;
}

```

### Complexity Analysis

- **Time Complexity:** O(√n)
Because the loop runs roughly up to the square root of `num`.
- **Space Complexity:** O(1)
No extra space used.

### Dry Run

- Input: `num = 16`
- Loop: `i` from 1 to 16
    - i=1: 1*1=1 ≠ 16
    - i=2: 2*2=4 ≠ 16
    - i=3: 3*3=9 ≠ 16
    - i=4: 4*4=16 == 16 → **return true**

---

## Approach 2: Binary Search (More Efficient)

### Core Idea

Use binary search to find if there's an integer `mid` such that `mid * mid == num`.

### Algorithm

1. Initialize `low = 1`, `high = num`.
2. While `low <= high`:
    - Calculate `mid = low + (high - low) / 2`.
    - If `mid * mid == num`, return `true`.
    - If `mid * mid < num`, set `low = mid + 1`.
    - Else, set `high = mid - 1`.
3. If no match found, return `false`.

### Java Code

```java
public boolean isPerfectSquare(int num) {
    if (num < 2) return true; // 1 and 0 are perfect squares
    long low = 1, high = num;
    while (low <= high) {
        long mid = low + (high - low) / 2;
        long square = mid * mid;
        if (square == num) {
            return true;
        } else if (square < num) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return false;
}

```

### Complexity Analysis

- **Time Complexity:** O(log n)
Because binary search halves the search space each iteration.
- **Space Complexity:** O(1)
No extra space apart from variables.

### Dry Run

- Input: `num = 16`
- Initial: `low=1`, `high=16`
    - Iteration 1:
        - `mid=8`, `8*8=64 > 16` → `high=7`
    - Iteration 2:
        - `mid=4`, `4*4=16 == num` → **return true**

---

## Approach 3: Mathematical Approach Using Newton's Method (Optimized)

### Core Idea

Use Newton's method (also known as the Babylonian method) for approximating square roots to determine if `num` is a perfect square.

### Algorithm

1. Initialize `x = num`.
2. Loop until `x * x` is close to `num`:
    - Update `x = (x + num / x) / 2`.
3. Check if `x * x == num` (considering integer truncation).
4. Return the result accordingly.

### Java Code

```java
public boolean isPerfectSquare(int num) {
    if (num < 2) return true;
    long x = num;
    while (x * x > num) {
        x = (x + num / x) / 2;
    }
    return x * x == num;
}

```

### Complexity Analysis

- **Time Complexity:** O(log n)
Newton's method converges rapidly.
- **Space Complexity:** O(1)
Constant space usage.

### Dry Run

- Input: `num=16`
- Initial: `x=16`
    - Iteration 1:
        - `x = (16 + 16/16)/2 = (16 + 1)/2=8.5` → truncated to 8
    - Check: `8*8=64 > 16`, continue
    - Next iteration:
        - `x=(8 + 16/8)/2 = (8 + 2)/2=5`
    - Since `5*5=25 > 16`, continue
    - Next iteration:
        - `x=(5 + 16/5)/2 ≈ (5 + 3)/2=4`
    - Check: `4*4=16 == num` → **return true**

---

## Summary Table

| Approach | Idea | Code | Time Complexity | Space Complexity | Remarks |
| --- | --- | --- | --- | --- | --- |
| Brute Force | Check all numbers up to `num` | Provided | O(√n) | O(1) | Slow for large inputs |
| Binary Search | Search for `sqrt(num)` using binary search | Provided | O(log n) | O(1) | Efficient and reliable |
| Newton's Method | Approximate sqrt via iterative convergence | Provided | O(log n) | O(1) | Fast convergence, numerically stable |

---

## Final Tips

- For constraints up to `10^9`, binary search and Newton's method are recommended.
- Always handle small inputs (`num = 0 or 1`) as special cases.
- Use long type in Java during calculations to avoid overflow.

---

This comprehensive guide should help you understand various ways to solve **Valid Perfect Square** efficiently during interviews.
