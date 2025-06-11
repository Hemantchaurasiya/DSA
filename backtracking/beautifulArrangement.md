# Beautiful Arrangement

---

## Problem Overview:
Given an integer `n`, count the number of **beautiful arrangements** that can be formed by placing numbers 1 to n in an array such that:
- For each position `i (1-based)`, either:
  - The number at position `i` is divisible by `i`, **or**
  - `i` is divisible by the number at position `i`.

---

## Approach 1: Brute Force (Backtracking)

### **Core Idea:**
Generate all permutations of numbers 1 to n and count those satisfying the condition at each position.

### **Algorithm:**
1. Generate all permutations of the array `[1, 2, ..., n]`.
2. For each permutation:
   - Check for each position `i` whether the number at `i` satisfies the divisibility condition.
3. Count permutations that satisfy the condition.

### **Recursion Tree Diagram:**

```
Level 1: Choose first number (n options)
Level 2: Choose second number (remaining options)
...
Level n: Choose last number
At each permutation leaf, verify condition.
```

### **Java Code:**

```java
import java.util.*;

public class BeautifulArrangement {
    int count = 0;

    public int countArrangement(int n) {
        List<Integer> nums = new ArrayList<>();
        for (int i = 1; i <= n; i++) {
            nums.add(i);
        }
        backtrack(nums, 0);
        return count;
    }

    private void backtrack(List<Integer> nums, int index) {
        if (index == nums.size()) {
            count++;
            return;
        }
        for (int i = index; i < nums.size(); i++) {
            Collections.swap(nums, i, index);
            if (isValid(nums, index + 1)) {
                backtrack(nums, index + 1);
            }
            Collections.swap(nums, i, index);
        }
    }

    private boolean isValid(List<Integer> nums, int position) {
        int num = nums.get(position - 1);
        return (num % position == 0) || (position % num == 0);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n!)  
  Because in the worst case, all permutations are generated (n!), and validation is O(1) per permutation.
- **Space Complexity:** O(n)  
  For recursion stack and temporary storage during permutation generation.

### **Dry Run (Example `n=3`):**

| Permutation | Valid? | Explanation                     |
|--------------|---------|---------------------------------|
| [1, 2, 3]    | Yes     | 1|1, 2|2, 3|3 satisfy the condition |
| [1, 3, 2]    | No      | 3 at position 2; 2%2=0 but 3%2≠0 and 2%3≠0 |
| [2, 1, 3]    | Yes     | 2 at position 1; 2%1=0, 1 at 2; 2%2=0, 3 at 3; 3%3=0 |
| ...          | ...     | ...                             |

---

## Approach 2: Backtracking with Pruning (Better Solution)

### **Core Idea:**
Instead of generating all permutations blindly, **try placing numbers at positions** only if they satisfy the divisibility condition, pruning invalid branches early.

### **Algorithm:**
1. Use a recursive function `dfs(position, usedNumbers)`:
   - If `position > n`, increment result count.
   - For each number `num` from 1 to n:
     - If `num` is not used and satisfies `num % position == 0` or `position % num == 0`:
       - Mark `num` as used.
       - Recurse for `position + 1`.
       - Backtrack by marking `num` as unused.

### **Recursion Tree Diagram:**

```
At each position:
  - For each valid number (divisible condition met), recurse further.
  - Prune branches where no valid number can be placed.
```

### **Java Code:**

```java
public class BeautifulArrangement {
    int count = 0;

    public int countArrangement(int n) {
        boolean[] used = new boolean[n + 1];
        dfs(n, 1, used);
        return count;
    }

    private void dfs(int n, int position, boolean[] used) {
        if (position > n) {
            count++;
            return;
        }
        for (int num = 1; num <= n; num++) {
            if (!used[num] && (num % position == 0 || position % num == 0)) {
                used[num] = true;
                dfs(n, position + 1, used);
                used[num] = false;
            }
        }
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n!) in the worst case, but pruning reduces the actual runtime.
- **Space Complexity:** O(n) for the `used` array and recursion stack.

### **Dry Run (Example `n=3`):**

| Step | Position | Used numbers | Choices (valid numbers) | Recursive calls | Result |
|-------|------------|----------------|--------------------------|-------------------|---------|
| 1     | 1          | none           | 1, 2, 3                | Recursion for each | Proceed with valid choices |
| 2     | 2          | e.g., 1 used   | 2, 3 (if valid)        | Recurse or backtrack | Count arrangements satisfying conditions |
| ...   | ...        | ...            | ...                      | ...               | ...     |

---

## Approach 3: Dynamic Programming with Bitmasking (Most Optimal)

### **Core Idea:**
Use **bitmasking** to represent used numbers and **memoization** (DP) to avoid recomputation of states.  
This approach is efficient for larger `n`, leveraging overlapping subproblems.

### **Algorithm:**
1. Define a recursive function `dfs(mask, position)`:
   - `mask` is a bitmask representing which numbers are used.
   - `position` indicates current position to fill.
2. For each number `num` not used in `mask`, check if it satisfies the divisibility condition with `position`.
3. If valid, recurse with `mask` updated (`mask | (1 << (num - 1))`) and `position + 1`.
4. Use a memoization table `dp[mask]` to cache results for each state.

### **Recursion Tree Diagram:**

```
From each state (mask, position):
  - Explore all valid unused numbers.
  - Recurse into new states with updated mask.
  - Memoize results to avoid recomputation.
```

### **Java Code:**

```java
public class BeautifulArrangement {
    private int[] dp;
    private int n;

    public int countArrangement(int n) {
        this.n = n;
        int size = 1 << n; // total states
        dp = new int[size];
        Arrays.fill(dp, -1);
        return dfs(0, 1);
    }

    private int dfs(int mask, int position) {
        if (position > n) {
            return 1;
        }
        if (dp[mask] != -1) {
            return dp[mask];
        }
        int total = 0;
        for (int num = 1; num <= n; num++) {
            int bit = 1 << (num - 1);
            if ((mask & bit) == 0 && (num % position == 0 || position % num == 0)) {
                total += dfs(mask | bit, position + 1);
            }
        }
        dp[mask] = total;
        return total;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** O(n * 2^n)  
  - Each state (bitmask) is computed once; for each, we iterate through n options.
- **Space Complexity:** O(n * 2^n) for memoization and recursion stack.

### **Dry Run (Example `n=3`):**

| State (mask, position) | Valid choices | Recurse into states | Total count accumulated |
|-------------------------|-----------------|---------------------|---------------------------|
| (0b000, 1)             | 1, 2, 3       | Recurse with updated mask | Sum of all valid paths   |
| ...                     | ...             | ...                 | ...                       |

---

## Summary:
| Approach                                | Best Use Case                      | Key Technique           | Time Complexity         | Space Complexity |
|-----------------------------------------|-----------------------------------|---------------------------|------------------------|------------------|
| Brute Force (Backtracking)              | Small `n` (e.g., n ≤ 10)          | Generate permutations     | O(n!)                  | O(n)             |
| Backtracking with Pruning                | Moderate `n` (e.g., n ≤ 15)       | Prune invalid branches     | Less than O(n!)        | O(n)             |
| DP with Bitmasking                      | Larger `n` (e.g., n up to 20)     | Memoization + bitmasking   | O(n * 2^n)             | O(n * 2^n)       |

---

## Final Tips:
- For small constraints, brute-force backtracking is fine.
- For larger `n`, use the DP + bitmasking approach for efficiency.
- Always incorporate pruning where possible to reduce search space.
- Remember to verify the divisibility condition at each step to avoid unnecessary computations.

---

**Happy Coding & Interview Prep!**
