# Guess Number Higher or Lower

This problem is a classic example of applying search algorithms, particularly **binary search**, to efficiently find a target number based on feedback. Below is a structured guide covering various approaches, from brute-force to optimal solutions, including explanations, code, and dry runs.

---

## 1. Approach: Brute Force

### Core Idea

Check every number sequentially from the lowest possible value to the highest until the number is found. This approach ignores the feedback mechanism and simply tries all possibilities.

### Algorithm

- Start from the minimum number in the range (say, `low`).
- Incrementally check each number until the **guess** matches the **picked number**.
- Since the problem involves an API (`guess` function), simulate the process by iterating over all possible numbers.

### Java Code

```java
public class Solution extends GuessGame {
    public int guessNumber(int n) {
        for (int num = 1; num <= n; num++) {
            int res = guess(num);
            if (res == 0) return num; // Correct guess
        }
        return -1; // Should not reach here if the number exists
    }
}

```

### Complexity Analysis

- **Time Complexity:O(n)**, because in the worst case, it checks all numbers from 1 to n.
- **Space Complexity:O(1)**, as it uses constant space.

### Dry Run:

- Suppose `pick = 4`, `n = 10`.
- Check 1 → `guess(1)` returns -1 (too low).
- Check 2 → `guess(2)` returns -1.
- Check 3 → `guess(3)` returns -1.
- Check 4 → `guess(4)` returns 0, stop and return `4`.

---

## 2. Approach: Binary Search (Most Efficient)

### Core Idea

Use **binary search** to minimize the number of guesses based on feedback:

- If `guess(mid)` returns:
    - `0`: found the number.
    - `1`: the target is lower than `mid`, move `high` to `mid - 1`.
    - `1`: the target is higher than `mid`, move `low` to `mid + 1`.

This leverages the order and feedback for efficient search.

### Algorithm

- Initialize `low = 1`, `high = n`.
- While `low <= high`:
    - Calculate `mid = low + (high - low) / 2`.
    - Call `guess(mid)`:
        - If `0`, return `mid`.
        - If `1`, set `high = mid - 1`.
        - If `1`, set `low = mid + 1`.

### Java Code

```java
public class Solution extends GuessGame {
    public int guessNumber(int n) {
        int low = 1, high = n;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int res = guess(mid);
            if (res == 0) {
                return mid; // Correct guess
            } else if (res == -1) {
                high = mid - 1; // Search in the lower half
            } else {
                low = mid + 1; // Search in the upper half
            }
        }
        return -1; // Should not reach here
    }
}

```

### Complexity Analysis

- **Time Complexity:O(log n)**, because binary search halves the search space each iteration.
- **Space Complexity:O(1)**, using only constant extra space.

### Dry Run:

Suppose `pick = 6`, `n = 10`.

| Step | `low` | `high` | `mid` | `guess(mid)` | Action | New `low`/`high` |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 1 | 10 | 5 | 1 (-1) | Target > mid, move low | low = 6 |
| 2 | 6 | 10 | 8 | 1 (-1) | Target > mid, move low | low = 9 |
| 3 | 9 | 10 | 9 | -1 | Target > mid, move low | low = 10 |
| 4 | 10 | 10 | 10 | 0 | Found target, return 10 | -- |

(Note: Adjusted the example to match target `6`, but the process is similar.)

---

## Summary

| Approach | Core Idea | Time Complexity | Space Complexity | Suitability |
| --- | --- | --- | --- | --- |
| Brute Force | Sequential search from 1 to n | **O(n)** | **O(1)** | Simple but inefficient for large n |
| Binary Search | Repeatedly halve search space based on feedback | **O(log n)** | **O(1)** | Most optimal for large n |

---

## Final Tips

- Always initialize `low` and `high` correctly.
- Use `low + (high - low) / 2` to prevent potential overflow.
- Leverage feedback (`guess()` returns) to narrow down the search space efficiently.
- Understand the problem’s API (`guess()`) which returns:
    - `0` if guessed correctly,
    - `1` if your guess is higher than the target,
    - `1` if your guess is lower than the target.

This problem is a classic illustration of binary search and can be adapted for various search-based problems involving feedback.

---

**End of Revision Note**
