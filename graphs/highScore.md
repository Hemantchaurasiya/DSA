# High Score

---

## Problem Overview:
Given an array of integers, determine the maximum score that can be achieved based on specific rules (e.g., selecting elements with constraints). The problem typically involves selecting elements to maximize a score, often with constraints like **not choosing adjacent elements** or **removing elements with specific rules**.

*(Note: The exact problem statement can vary; here, we assume a common version similar to "Remove and Earn" or "Maximize Score" problems.)*

---

## Approach 1: Brute Force

### Core Idea:
Explore all possible ways to select elements to maximize the score. This involves trying every combination, which leads to exponential complexity.

### Algorithm:
1. Generate all subsets of the array.
2. For each subset:
   - Check if it satisfies the constraints (e.g., no two selected elements are adjacent).
   - Calculate the total score.
3. Keep track of the maximum score obtained from all valid subsets.

### Java Code:
```java
public class HighScoreBruteForce {
    private int maxScore = 0;

    public int maxScore(int[] nums) {
        backtrack(nums, 0, 0);
        return maxScore;
    }

    private void backtrack(int[] nums, int index, int currentScore) {
        if (index >= nums.length) {
            maxScore = Math.max(maxScore, currentScore);
            return;
        }
        // Option 1: Skip current element
        backtrack(nums, index + 1, currentScore);
        // Option 2: Take current element
        // Check constraint (e.g., no adjacent picks)
        if (index == 0 || nums[index - 1] != nums[index]) { // Example constraint
            backtrack(nums, index + 1, currentScore + nums[index]);
        }
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(2^n)** — exploring all subset combinations.
- **Space Complexity:** **O(n)** — for recursion stack.

### Dry Run (Sample Input: `[1, 2, 3]`):
| Step | Current Index | Selected Elements | Current Score | Max Score |
|-------|----------------|---------------------|----------------|------------|
| 1     | 0              | []                  | 0              | 0          |
| 2     | 1              | []                  | 0              | 0          |
| 3     | 2              | []                  | 0              | 0          |
| ...   | ...            | ...                 | ...            | ...        |

*(All subsets explored)*

---

## Approach 2: Better Solution (Using Frequency Map / Hash Map)

### Core Idea:
Transform the problem into a frequency-based problem:
- Count the total value for each number.
- Think of the problem like "Delete and Earn," where choosing a number earns its total sum, but then the adjacent numbers are forbidden.

### Algorithm:
1. Create an array (or map) to store total sum for each number.
2. Use dynamic programming to decide whether to take or skip each number (like House Robber problem).

### Java Code:
```java
import java.util.HashMap;
import java.util.Map;

public class HighScoreBetter {
    public int deleteAndEarn(int[] nums) {
        Map<Integer, Integer> sumMap = new HashMap<>();
        int maxVal = 0;
        for (int num : nums) {
            sumMap.put(num, sumMap.getOrDefault(num, 0) + num);
            maxVal = Math.max(maxVal, num);
        }

        int[] dp = new int[maxVal + 1];
        dp[0] = 0;
        dp[1] = sumMap.getOrDefault(1, 0);
        for (int i = 2; i <= maxVal; i++) {
            int earn = sumMap.getOrDefault(i, 0);
            dp[i] = Math.max(dp[i - 1], dp[i - 2] + earn);
        }
        return dp[maxVal];
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(n + m)**, where n is the length of input array and m is the max value in array.
- **Space Complexity:** **O(m)** — for the DP array and the map.

### Dry Run (Sample Input: `[3, 4, 2]`):
| Step | sumMap | maxVal | DP array | Computation                     | Result   |
|-------|---------|---------|------------|---------------------------------|----------|
| Build | {2: 2, 3: 3, 4: 4} | 4 | [0, 0, 0, 0, 0] |                               |          |
| i=1   |         |         | [0, 3, 0, 0, 0] | DP[1] = sumMap[1] = 3           |          |
| i=2   |         |         | [0, 3, 2, 0, 0] | DP[2] = max(DP[1], DP[0] + sumMap[2]) = 3 |          |
| i=3   |         |         | [0, 3, 2, 6, 0] | DP[3] = max(DP[2], DP[1] + sumMap[3]) = max(2, 3+3=6) = 6 |          |
| i=4   |         |         | [0, 3, 2, 6, 10] | DP[4] = max(DP[3], DP[2] + sumMap[4]) = max(6, 2+4=6) = 6 | Final answer: 6 |

---

## Approach 3: Most Optimal Solution (Using DP with Space Optimization)

### Core Idea:
Use a DP approach similar to the "House Robber" problem, optimized for space.

### Algorithm:
1. Count total sum for each number.
2. Use two variables (`prev` and `curr`) to keep track of the maximum score till current number.
3. Iterate through numbers from 1 to maxValue:
   - Decide whether to include current number or skip it.
   - Update `prev` and `curr` accordingly.

### Java Code:
```java
public class HighScoreOptimal {
    public int deleteAndEarn(int[] nums) {
        int maxVal = 0;
        for (int num : nums) {
            maxVal = Math.max(maxVal, num);
        }

        int[] sum = new int[maxVal + 1];
        for (int num : nums) {
            sum[num] += num;
        }

        int prev = 0;
        int curr = 0;
        for (int i = 1; i <= maxVal; i++) {
            int temp = curr;
            curr = Math.max(curr, prev + sum[i]);
            prev = temp;
        }
        return curr;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(n + m)** — due to counting and iteration.
- **Space Complexity:** **O(m)** — for the sum array.

### Dry Run (Sample Input: `[3, 4, 2]`):
| Step | sum array | prev | curr | Calculation                            | Result |
|-------|------------|--------|-------|----------------------------------------|---------|
| Build | [0,0,2,3,4,0,0] | 0 | 0 |                                        |         |
| i=1   |            | 0 | max(0, 0+0) = 0 |                                    |         |
| i=2   |            | 0 | max(0, 0+2) = 2 |                                    |         |
| i=3   |            | 2 | max(2, 0+3) = 3 |                                    |         |
| i=4   |            | 3 | max(3, 2+4) = 6 | Final answer                                   | 6       |

---

## Summary:
| Approach | Time Complexity | Space Complexity | Best Use Case                                            |
|------------|-----------------|------------------|----------------------------------------------------------|
| Brute Force | O(2^n)        | O(n)             | Small input sizes, exploratory or initial implementation |
| Hash Map + DP | O(n + m)    | O(m)             | Moderate input sizes, where m = max value in array     |
| Space-Optimized DP | O(n + m) | O(m)             | Large input sizes, optimized for space and time        |

---

**Tip:** For problems similar to "High Score" or "Delete and Earn," transforming the problem into a variation of the "House Robber" problem and using DP with space optimization is often the most efficient approach.

---

**Happy Revising!**
