# The Number of Beautiful Subsets

---

## Problem Overview
Given an array of integers `nums` and an integer `k`, find the **number of "beautiful" subsets** of `nums`.  
A subset is **beautiful** if **for any two distinct elements** `a` and `b` in the subset, **|a - b| ≠ k**.

---

## Approach 1: Brute Force

### Core Idea
- Generate all possible subsets.
- Check each subset if it satisfies the "beautiful" condition.
- Count subsets that are "beautiful".

### Algorithm
1. Generate all subsets of `nums` (using bitmasking or recursion).
2. For each subset:
   - Check all pairs `(a, b)` in the subset.
   - If any pair satisfies `|a - b| = k`, discard the subset.
3. Count all valid subsets.

### Recursion Tree Diagram
```
At each element, choose to include or exclude:
                 []
               /     \
             [nums[0]]  []
             /    \     /   \
       [nums[0],...]  [...]   ...
```

*(This is an exponential exploration of all subsets.)*

### Java Code
```java
import java.util.*;

public class BeautifulSubsets {
    int count = 0;
    int[] nums;
    int k;

    public int beautifulSubsets(int[] nums, int k) {
        this.nums = nums;
        this.k = k;
        backtrack(0, new ArrayList<>());
        return count;
    }

    private void backtrack(int index, List<Integer> subset) {
        if (index == nums.length) {
            if (isBeautiful(subset)) {
                count++;
            }
            return;
        }
        // Include nums[index]
        subset.add(nums[index]);
        backtrack(index + 1, subset);
        // Exclude nums[index]
        subset.remove(subset.size() - 1);
        backtrack(index + 1, subset);
    }

    private boolean isBeautiful(List<Integer> subset) {
        for (int i = 0; i < subset.size(); i++) {
            for (int j = i + 1; j < subset.size(); j++) {
                if (Math.abs(subset.get(i) - subset.get(j)) == k) {
                    return false;
                }
            }
        }
        return true;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(2^n * n^2)*  
  - There are `2^n` subsets, and checking each subset involves pairwise comparisons (`O(n^2)`).
- **Space Complexity:** *O(n)*  
  - For recursion call stack and subset storage.

### Dry Run (Sample Input)
```
nums = [1, 2, 3], k = 1
Subsets and checks:
[] -> valid
[1] -> valid
[2] -> valid
[3] -> valid
[1,2] -> |1-2|=1 -> invalid
[1,3] -> |1-3|=2 -> valid
[2,3] -> |2-3|=1 -> invalid
[1,2,3] -> contains invalid pairs -> invalid
Count = 4 valid subsets
```


---

## Approach 2: Backtracking with Pruning (Optimized Search)

### Core Idea
- Use backtracking but avoid generating invalid subsets early.
- When adding a new element, check if it violates the condition with elements already in the subset.
- Prune the search space as soon as the subset becomes invalid.

### Algorithm
1. Sort `nums` to process elements in order.
2. Use backtracking:
   - For each element, try to include it if it doesn't violate the difference condition.
   - Recurse with the updated subset.
3. Count all valid subsets.

### Recursion Tree Diagram
```
Start:
  Include nums[0], then recurse
    Include nums[1] if valid, recurse
    Exclude nums[1], recurse
  Exclude nums[0], recurse
```

*(Pruning prevents exploring invalid branches.)*

### Java Code
```java
import java.util.*;

public class BeautifulSubsetsOptimized {
    int count = 0;
    int[] nums;
    int k;

    public int beautifulSubsets(int[] nums, int k) {
        this.nums = nums;
        this.k = k;
        Arrays.sort(nums);
        backtrack(0, new ArrayList<>());
        return count;
    }

    private void backtrack(int index, List<Integer> subset) {
        // Count current subset as valid (non-empty)
        if (!subset.isEmpty()) {
            count++;
        }
        for (int i = index; i < nums.length; i++) {
            boolean canInclude = true;
            for (int num : subset) {
                if (Math.abs(num - nums[i]) == k) {
                    canInclude = false;
                    break;
                }
            }
            if (canInclude) {
                subset.add(nums[i]);
                backtrack(i + 1, subset);
                subset.remove(subset.size() - 1);
            }
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(2^n * n)*  
  - Worst case, every subset is explored, but pruning reduces some branches.
- **Space Complexity:** *O(n)*  
  - For recursion and subset storage.

### Dry Run (Sample Input)
```
nums = [1, 2, 3], k = 1
Sorted: [1, 2, 3]
Start:
  Include 1 -> subset=[1], count=1
    Include 2? |1-2|=1 -> invalid, skip
    Exclude 2
    Include 3? |1-3|=2 -> okay, subset=[1,3], count=2
      Next? no more elements
  Exclude 1
    Include 2 -> subset=[2], count=3
      Include 3? |2-3|=1 -> invalid, skip
  Exclude 2
    Include 3 -> subset=[3], count=4
Total valid subsets: 4
```

---

## Approach 3: Dynamic Programming with State Compression (Most Optimized)

### Core Idea
- Recognize that the problem involves choosing subsets with certain constraints.
- Use **bitmasking** to represent subsets.
- For each subset, check if it violates the difference condition; if not, include it in the count.

### Algorithm
1. Sort `nums`.
2. Generate all subset masks (`0` to `2^n - 1`).
3. For each subset:
   - Check if it contains pairs with `|a - b| = k`.
   - If valid, increment count.
4. Return total count.

### Java Code
```java
import java.util.*;

public class BeautifulSubsetsDP {
    public int beautifulSubsets(int[] nums, int k) {
        int n = nums.length;
        Arrays.sort(nums);
        int count = 0;
        for (int mask = 1; mask < (1 << n); mask++) {
            boolean isValid = true;
            List<Integer> subset = new ArrayList<>();
            for (int i = 0; i < n; i++) {
                if (((mask >> i) & 1) == 1) {
                    for (int num : subset) {
                        if (Math.abs(nums[i] - num) == k) {
                            isValid = false;
                            break;
                        }
                    }
                    if (!isValid) break;
                    subset.add(nums[i]);
                }
            }
            if (isValid) count++;
        }
        return count;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(2^n * n)*  
  - For each subset, checking pairs takes `O(n)` in the worst case.
- **Space Complexity:** *O(n)*  
  - For temporary list to check validity.

---

## Final Notes:
- **Brute force** is straightforward but inefficient for larger `n`.
- **Backtracking with pruning** reduces unnecessary exploration.
- **Bitmask DP** offers a clear, systematic enumeration of subsets but can be less efficient for large `n` due to constant subset checks.
- For large constraints, an **optimized backtracking** approach or problem-specific pruning is recommended.

---

## Key Takeaways
- Always consider sorting to enable early pruning.
- Use recursion or bitmasking to generate subsets.
- Check conditions early to prune invalid branches.
- Understand the trade-offs between different approaches based on input size constraints.

---
