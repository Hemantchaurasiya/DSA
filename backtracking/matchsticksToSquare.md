# Matchsticks to Square

---

## Problem Statement
Given an array `matchsticks` where each element represents the length of a matchstick, determine if it is possible to use all the matchsticks to form **exactly one square**. Each matchstick must be used exactly once.

---

## Approach 1: Brute Force (Backtracking with all permutations)

### 1. **Core Idea**
Try **all possible ways** to partition matchsticks into 4 groups with equal sum, ensuring each group forms a side of the square.

### 2. **Algorithm**
- First, check if the total sum of matchsticks is divisible by 4; if not, return false.
- Calculate the side length as `totalSum / 4`.
- Use **backtracking** to assign each matchstick to one of the four sides, ensuring:
  - No side exceeds the target length.
  - All matchsticks are used.
- Recursively try to assign each matchstick to each side and backtrack if constraints are violated.

### 3. **Recursion Tree Diagram**
```
Start:
    - Assign first matchstick to side 1,2,3,4
        - For each assignment, recurse to next matchstick
            - Continue until all matchsticks are assigned
            - If at any point, a side exceeds target, backtrack
```

### 4. **Java Code**
```java
import java.util.Arrays;

public class MatchsticksToSquare {
    public boolean makesquare(int[] matchsticks) {
        int sum = 0;
        for (int m : matchsticks) sum += m;
        if (sum % 4 != 0) return false;
        int sideLength = sum / 4;
        Arrays.sort(matchsticks);
        // To optimize, process larger matchsticks first
        reverse(matchsticks);
        int[] sides = new int[4];
        return backtrack(matchsticks, 0, sides, sideLength);
    }

    private boolean backtrack(int[] matchsticks, int index, int[] sides, int target) {
        if (index == matchsticks.length) {
            // Check if all sides are equal to target
            for (int side : sides) {
                if (side != target) return false;
            }
            return true;
        }
        for (int i = 0; i < 4; i++) {
            if (sides[i] + matchsticks[index] <= target) {
                sides[i] += matchsticks[index];
                if (backtrack(matchsticks, index + 1, sides, target)) {
                    return true;
                }
                sides[i] -= matchsticks[index]; // backtrack
            }
            // Optimization: if this side is 0, no need to try other sides with 0
            if (sides[i] == 0) break;
        }
        return false;
    }

    private void reverse(int[] arr) {
        int i = 0, j = arr.length - 1;
        while (i < j) {
            int temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
            i++; j--;
        }
    }
}
```

### 5. **Complexity Analysis**
- **Time Complexity:** *O(4^n)* in worst case, because each matchstick can go into 4 sides, leading to exponential possibilities.
- **Space Complexity:** *O(n)* for recursion stack and sides array.

### 6. **Dry Run Example**

Input: `[1,1,2,2,2]`

| Step | Matchstick | Sides State             | Action                                   | Result/Next Step                                               |
|-------|--------------|-------------------------|------------------------------------------|--------------------------------------------------------------|
| 0     | 2            | [0, 0, 0, 0]            | Assign to side 1                        | [2, 0, 0, 0]                                                  |
| 1     | 2            | [2, 0, 0, 0]            | Assign to side 1 (exceeds? No)          | [4, 0, 0, 0] -> exceeds side length? No, proceed             |
| 2     | 2            | [4, 0, 0, 0]            | Assign to side 2                        | [4, 2, 0, 0]                                                  |
| 3     | 1            | [4, 2, 0, 0]            | Assign to side 3                        | [4, 2, 1, 0]                                                  |
| 4     | 1            | [4, 2, 1, 0]            | Assign to side 4                        | [4, 2, 1, 1]                                                  |
| End   | All assigned | Sides = [4, 2, 1, 1]   | Check if all sides are equal to target  | No, backtrack and try other assignments                 |

---

## Approach 2: Optimized Backtracking (Sorting + Pruning)

### 1. **Core Idea**
- Sort matchsticks in descending order to place larger matchsticks first, reducing the search space.
- Use pruning:
  - Skip assignment if the current matchstick doesn't fit.
  - Avoid duplicate states by skipping identical side states.
  
### 2. **Algorithm**
- Same as above but with sorting and pruning techniques to reduce the search space.
- Always try to place the largest matchstick first to quickly identify invalid configurations.

### 3. **Recursion Tree Diagram**
Same as above but with fewer branches due to pruning.

### 4. **Java Code**
*(See code above, with sorting)*

### 5. **Complexity Analysis**
- **Time Complexity:** *Better than brute force, roughly O(4^{n}) in worst case*, but pruning often reduces actual runtime.
- **Space Complexity:** *O(n)* for recursion stack and sides array.

### 6. **Dry Run**
Same as above, but starting with sorted array `[2,2,2,1,1]` (descending), leading to faster pruning.

---

## Approach 3: Bitmask + Memoization (Advanced)

### 1. **Core Idea**
- Use **bitmask** to represent used matchsticks.
- Use **memoization** to cache states and avoid recomputation.
- Attempt to build sides by selecting matchsticks greedily and backtracking.

### 2. **Algorithm**
- Calculate total sum, check divisibility.
- Use a recursive function with:
  - current sum of the current side,
  - number of sides completed,
  - bitmask of used matchsticks.
- Memoize states based on used matchsticks bitmask and current side sum.
- When the current side reaches target, move to the next side.
- Stop when all matchsticks are used and 4 sides are formed.

### 3. **Recursion Tree Diagram**
```
Start:
    - Select matchsticks not used (bitmask)
    - Build sides up to target sum
    - Memoize states to avoid recomputation
    - Once 4 sides formed, return true
```

### 4. **Java Code**
```java
import java.util.Arrays;
import java.util.HashMap;

public class MatchsticksToSquareBitmask {
    public boolean makesquare(int[] matchsticks) {
        int total = 0;
        for (int m : matchsticks) total += m;
        if (total % 4 != 0) return false;
        int target = total / 4;
        Arrays.sort(matchsticks);
        // reverse to optimize
        for (int i = 0, j = matchsticks.length - 1; i < j; i++, j--) {
            int temp = matchsticks[i];
            matchsticks[i] = matchsticks[j];
            matchsticks[j] = temp;
        }
        HashMap<Integer, Boolean> memo = new HashMap<>();
        return dfs(matchsticks, 0, new int[4], target, 0, memo);
    }

    private boolean dfs(int[] matchsticks, int index, int[] sides, int target, int usedMask, HashMap<Integer, Boolean> memo) {
        if (index == matchsticks.length) {
            for (int side : sides) {
                if (side != target) return false;
            }
            return true;
        }
        if (memo.containsKey(usedMask)) return memo.get(usedMask);
        for (int i = 0; i < 4; i++) {
            if (sides[i] + matchsticks[index] <= target) {
                sides[i] += matchsticks[index];
                int newMask = usedMask | (1 << index);
                if (dfs(matchsticks, index + 1, sides, target, newMask, memo)) {
                    return true;
                }
                sides[i] -= matchsticks[index];
            }
        }
        memo.put(usedMask, false);
        return false;
    }
}
```

### 5. **Complexity Analysis**
- **Time Complexity:** *Potentially exponential, but memoization reduces duplicate computations.*
- **Space Complexity:** *O(2^n) for bitmask states + O(4) for sides array and recursion stack.*

---

## Summary Table

| Approach                         | Key Techniques                         | Best Use Case                     | Time Complexity                     | Space Complexity                  |
|----------------------------------|----------------------------------------|----------------------------------|-------------------------------------|----------------------------------|
| Brute Force (Backtracking)       | All permutations, recursive assignment | Small input sizes               | O(4^n)                            | O(n)                            |
| Optimized Backtracking           | Sorting + pruning                     | Medium to large inputs            | Better than brute force due to pruning | O(n)                        |
| Bitmask + Memoization            | State caching with bitmask             | Larger inputs with memoization  | Potentially exponential but optimized | O(2^n) + recursion stack    |

---

## Final Tips for Interview Preparation
- Always check if total sum is divisible by 4.
- Sort the matchsticks in descending order to optimize backtracking.
- Use pruning to cut off invalid paths early.
- For advanced solutions, consider bitmask + memoization for large inputs.
- Practice dry runs with small inputs to understand recursion flow.

---

This comprehensive guide should help you understand various approaches to the "Matchsticks to Square" problem for effective interview preparation and quick revision.
