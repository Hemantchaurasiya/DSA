# Combination Sum

---

## Problem Overview
Given an array of **distinct integers** `candidates` and a target sum `target`, find **all unique combinations** of candidates where the candidate numbers sum to `target`. You may **reuse** the same number multiple times.

---

## Approach 1: Brute Force (Recursive Backtracking without Optimization)

### Core Idea
Explore **all possible combinations** by trying each candidate multiple times, then backtrack if the sum exceeds the target. This approach generates all potential combinations, including duplicates.

### Algorithm
1. **Start with an empty combination** and sum = 0.
2. For each candidate:
   - If adding the candidate keeps sum ≤ target, recurse with updated sum and current combination.
   - If sum == target, add the current combination to the result.
   - If sum > target, backtrack.
3. After exploring each candidate, backtrack to explore other options.

### Recursion Tree Diagram
```
Start (sum=0, combination=[])
├── choose candidate 1 (e.g., 2):
│   ├── choose 2 (sum=2): continue
│   │   ├── choose 2 (sum=4): continue
│   │   │   ├── choose 2 (sum=6) -> stop
│   │   │   └── choose 3 (sum=5) -> stop
│   │   ├── choose 3 (sum=5): continue
│   │   │   ├── choose 2 (sum=7) -> stop
│   │   │   └── choose 3 (sum=8) -> stop
│   ├── choose 3 (sum=3): continue
│   │   ...
├── choose candidate 2 (e.g., 3):
│   ...
```

*(Note: Tree expands recursively; branches terminate when sum exceeds target or finds a valid combination.)*

### Java Code
```java
import java.util.*;

public class CombinationSum {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(candidates, target, 0, new ArrayList<>(), result);
        return result;
    }
    
    private void backtrack(int[] candidates, int target, int start, List<Integer> current, List<List<Integer>> result) {
        if (target == 0) {
            result.add(new ArrayList<>(current));
            return;
        }
        if (target < 0) {
            return; // no valid combination
        }
        for (int i = start; i < candidates.length; i++) {
            current.add(candidates[i]);
            backtrack(candidates, target - candidates[i], i, current, result); // not i+1 because we can reuse same element
            current.remove(current.size() - 1); // backtrack
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(2^N)** in the worst case, where N is the number of candidates. Because we explore all combinations, including duplicates, this can be exponential.
- **Space Complexity:** **O(N)** for recursion stack and current combination storage.

### Dry Run: Example
**Input:** `candidates = [2, 3, 6, 7]`, `target = 7`

| Step | Current Combination | Remaining Target | Action                         | Result                               |
|-------|------------------------|------------------|--------------------------------|-------------------------------------|
| 1     | []                     | 7                | Choose 2                       | [2], target=5                      |
| 2     | [2]                    | 5                | Choose 2                       | [2,2], target=3                    |
| 3     | [2,2]                  | 3                | Choose 2                       | [2,2,2], target=1                  |
| 4     | [2,2,2]                | 1                | Choose 2 -> stop (exceeds)    | backtrack                         |
| 3     | [2,2]                  | 3                | Choose 3                       | [2,2,3], target=0 -> add to result |
| 2     | [2]                    | 5                | Choose 3                       | [2,3], target=2                    |
| 3     | [2,3]                  | 2                | Choose 6 -> stop (exceeds)    | backtrack                         |
| 3     | [2,3]                  | 2                | Choose 7 -> stop (exceeds)    | backtrack                         |
| 2     | [2]                    | 5                | Choose 6 -> stop (exceeds)    | backtrack                         |
| 2     | [2]                    | 5                | Choose 7 -> stop (exceeds)    | backtrack                         |
| 1     | []                     | 7                | Choose 3                       | [3], target=4                      |
| 2     | [3]                    | 4                | Choose 6 -> stop (exceeds)    | backtrack                         |
| 2     | [3]                    | 4                | Choose 7 -> stop (exceeds)    | backtrack                         |
| ...   | ...                    | ...              |                                |                                     |

---

## Approach 2: Better Solution (Sorted + Pruning)

### Core Idea
Sort candidates to facilitate **early pruning** — stop exploring further once the candidate exceeds remaining target.

### Algorithm
1. Sort the input array.
2. Use backtracking similar to the brute force approach.
3. **Prune** the recursion when the current candidate exceeds the remaining target.
4. Continue exploring only candidates that are ≤ remaining target.

### Java Code
```java
import java.util.*;

public class CombinationSum {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates); // sorting for pruning
        List<List<Integer>> result = new ArrayList<>();
        backtrack(candidates, target, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] candidates, int remaining, int start, List<Integer> current, List<List<Integer>> result) {
        if (remaining == 0) {
            result.add(new ArrayList<>(current));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            if (candidates[i] > remaining) break; // prune
            current.add(candidates[i]);
            backtrack(candidates, remaining - candidates[i], i, current, result);
            current.remove(current.size() - 1);
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N! / (N - target/min_element)!)**, generally better than brute force due to pruning.
- **Space Complexity:** **O(N)** for recursion and current list.

---

## Approach 3: Most Optimal Solution (Backtracking with Sorting & Early Pruning + Memoization (if applicable))

### Core Idea
Implement **backtracking with sorting and pruning**, and optionally cache subproblem results to avoid recomputation (though for this problem, caching is less common).

### Algorithm
- Similar to Approach 2, with an emphasis on **early stopping**.
- Additional memoization isn't straightforward here because combinations are different for different paths, but pruning significantly reduces redundant exploration.

### Java Code
*Same as Approach 2 with added comments on potential memoization.*

```java
import java.util.*;

public class CombinationSum {
    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        Arrays.sort(candidates);
        List<List<Integer>> result = new ArrayList<>();
        backtrack(candidates, target, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] candidates, int remaining, int start, List<Integer> current, List<List<Integer>> result) {
        if (remaining == 0) {
            result.add(new ArrayList<>(current));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            if (candidates[i] > remaining) break; // prune
            current.add(candidates[i]);
            backtrack(candidates, remaining - candidates[i], i, current, result);
            current.remove(current.size() - 1);
        }
    }
}
```

---

## Summary
| Aspect | Approach 1 | Approach 2 | Approach 3 |
|---------|--------------|--------------|--------------|
| **Core Idea** | Explore all combinations (no sorting) | Sort + Pruning | Same as Approach 2 with potential memoization |
| **Optimization** | None | Sorting + Early stopping | Same as Approach 2 |
| **Time Complexity** | O(2^N) | Better than O(2^N) due to pruning | Similar to Approach 2, often faster in practice |
| **Space Complexity** | O(N) | O(N) | O(N) |

---

This structured guide provides a clear progression from brute-force to optimized solutions, with detailed explanations, code, and dry runs to facilitate quick revision and strong interview preparedness.
