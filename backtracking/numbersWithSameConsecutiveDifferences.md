# Numbers With Same Consecutive Differences

This problem asks us to generate all numbers of length **N** where the absolute difference between every pair of consecutive digits is exactly **K**. 

---

## 1. **Approach Name:** Brute Force Enumeration

### 2. **Core Idea:**
Generate all possible numbers of length **N** and filter those where every pair of consecutive digits has a difference of **K**.

### 3. **Algorithm:**

- Iterate over all possible **N**-digit numbers:
  - For each number:
    - Check if the absolute difference between every pair of consecutive digits is **K**.
    - If yes, include it in the result.
- **Note:** The brute-force approach is inefficient because it considers all numbers from `10^(N-1)` to `10^N - 1` (or `0` for smaller leading digit considerations), leading to exponential complexity.

### 4. **Recursion Tree Diagram:**

Since brute-force is exhaustive, the recursion tree would be enormous, representing all possible numbers. It is not practical to draw explicitly, but conceptually:

```
Start with each possible leading digit (0-9)
  For each position:
    Pick next digit within difference K
    Recurse for remaining positions
```

### 5. **Java Code:**

```java
import java.util.*;

public class NumbersWithSameConsecutiveDifferences {
    public List<Integer> numsSameConsecDiff(int N, int K) {
        List<Integer> result = new ArrayList<>();
        if (N == 1) {
            for (int i = 0; i <= 9; i++) {
                result.add(i);
            }
            return result;
        }
        for (int start = 1; start <= 9; start++) {
            dfs(N - 1, start, K, start, result);
        }
        return result;
    }

    private void dfs(int remainingDigits, int currentNumber, int K, int lastDigit, List<Integer> result) {
        if (remainingDigits == 0) {
            result.add(currentNumber);
            return;
        }
        int nextDigit1 = lastDigit + K;
        int nextDigit2 = lastDigit - K;

        if (nextDigit1 <= 9) {
            dfs(remainingDigits - 1, currentNumber * 10 + nextDigit1, K, nextDigit1, result);
        }
        if (K != 0 && nextDigit2 >= 0) {  // Avoid duplicates when K == 0
            dfs(remainingDigits - 1, currentNumber * 10 + nextDigit2, K, nextDigit2, result);
        }
    }
}
```

### 6. **Complexity Analysis:**

- **Time Complexity:**  
  - **O(2^(N))** in the worst case, since at each digit we branch into up to 2 recursive calls (for `+K` and `-K`), and there are `N` levels.
- **Space Complexity:**  
  - **O(N)** for recursion stack depth, plus space for the result list.

### 7. **Dry Run with Sample Input:**

- **Input:** `N = 3`, `K = 7`
- **Process:**

| Step | Current Number | Remaining Digits | Last Digit | Next Digits to Explore | Result Added?         |
|-------|------------------|------------------|--------------|------------------------|-----------------------|
| Start | 1                | 2                | 1            | 1+7=8, 1-7=-6(no),   | No                    |
|       | 8                | 1                | 8            | 8+7=15(no), 8-7=1      | No (already explored)|
| Start | 2                | 2                | 2            | 2+7=9, 2-7=-5(no)     | No                    |
|       | 9                | 1                | 9            | 9+7=16(no), 9-7=2      | No                    |
| ...   | ...              | ...              | ...          | ...                    | ...                   |
| Final | 1 8 15 (Invalid) | N=0            | -            | -                      | N/A                   |

*Note:* Actual recursive calls will generate valid combinations like 18, 81, etc.

---

# Summary:

- **Brute Force** is straightforward but inefficient for large N.
- The optimal approach employs **Backtracking/DFS** to generate valid numbers directly, pruning invalid paths early.
- Key insights:
  - Handle the first digit separately (cannot be zero unless N=1).
  - For each current digit, explore next digits that differ by **K**.
  - Use recursion to build numbers step-by-step.

This structured approach ensures efficient generation of numbers with the specified property, suitable for interview discussions and quick revisions.
