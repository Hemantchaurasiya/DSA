# Combination Sum II

---

## Problem Overview:
Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations in `candidates` where the candidate numbers sum to `target`. Each number in `candidates` may only be used **once** in the combination. The solution must not contain duplicate combinations.

---

## Approach 1: Brute Force (Backtracking with Duplicates Handling)

### Core Idea:
Generate all possible combinations and filter out duplicates. Since duplicates are present, we need to ensure that we don't count duplicate combinations multiple times. This approach is straightforward but inefficient.

---

### Algorithm:
1. **Sort the input array** to handle duplicates easily.
2. Use **recursion** to explore each element:
   - For each index, decide to include or exclude the current element.
   - When including, move to the next index but skip duplicates.
   - When the sum exceeds `target`, backtrack.
3. **Avoid duplicate combinations** by skipping over duplicates after processing the first occurrence at each recursive depth.
4. Collect combinations that exactly sum to `target`.

---

### Recursion Tree Diagram:
```
Start with sorted candidates: [1, 1, 2, 5]

Level 0: choose first 1 -> proceed
  Level 1: choose second 1 -> proceed
    Level 2: choose 2 -> sum = 1+1+2=4
      ... continue
    Skip duplicate 1 at level 1
  Level 1: skip first 1, choose 2
Level 0: skip first 1, choose 2
...
```

*(Note: For visual clarity, refer to an actual diagram in implementation or visualize as a tree with branches representing choices.)*

---

### Java Code:
```java
import java.util.*;

public class CombinationSum2 {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        List<List<Integer>> results = new ArrayList<>();
        backtrack(candidates, target, 0, new ArrayList<>(), results);
        return results;
    }

    private void backtrack(int[] candidates, int target, int start, List<Integer> tempList, List<List<Integer>> results) {
        if (target == 0) {
            results.add(new ArrayList<>(tempList));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            // Skip duplicates
            if (i > start && candidates[i] == candidates[i - 1]) continue;
            if (candidates[i] > target) break; // No need to proceed further
            tempList.add(candidates[i]);
            backtrack(candidates, target - candidates[i], i + 1, tempList, results);
            tempList.remove(tempList.size() - 1);
        }
    }
}
```

---

### Complexity Analysis:
- **Time Complexity:** *O(2^n)* in the worst case, since each element can be either included or excluded, but pruning reduces some branches.
- **Space Complexity:** *O(n)* for the recursion stack and temporary list storage.

---

### Dry Run Example:
**Input:** `candidates = [10,1,2,7,6,1,5]`, `target=8`

| Step | Current Array | Current Target | Path          | Actions                                              |
|-------|----------------|----------------|--------------|------------------------------------------------------|
| 1     | [1,1,2,5,6,7,10]| 8             | []           | Start, choose 1                                       |
| 2     | [1,2,5,6,7,10]| 7             | [1]          | Choose next 1 (skip duplicate)                        |
| 3     | [2,5,6,7,10]| 6             | [1,1]        | Choose 2                                              |
| 4     | [5,6,7,10]| 4             | [1,1,2]      | Choose 5 (exceeds, prune)                             |
| 4     | [6,7,10]| 1             | [1,1,2]      | Skip 5, backtrack                                    |
| 3     | [2,5,6,7,10]| 6             | [1]          | Skip 2, choose 5                                    |
| ...   | ...            | ...            | ...          | Continue exploring for other combinations             |

*(Process continues until all combinations are explored)*

---

## Approach 2: **Optimized Solution Using Sorting, Pruning, and Duplicate Skipping**

### Core Idea:
Sort the array to handle duplicates efficiently. Use backtracking with pruning (stopping early when the current sum exceeds target). Skip duplicate elements at each recursive level to avoid duplicate combinations.

---

### Algorithm:
1. **Sort** the `candidates` array.
2. Use **recursion** to explore potential combinations:
   - At each step, iterate over candidates starting from `start`.
   - If the current element is the same as the previous one (and not at the start of this loop), skip it to avoid duplicates.
   - If `candidate[i]` exceeds the remaining `target`, break early (pruning).
   - Include `candidate[i]` and recurse with updated `target` and next index.
3. When `target == 0`, add the current combination to results.
4. Backtrack after exploring each choice.

---

### Recursion Tree Diagram:
*(Similar to Approach 1 but with pruning and duplicate skipping)*

---

### Java Code:
```java
import java.util.*;

public class CombinationSum2 {
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        Arrays.sort(candidates);
        List<List<Integer>> results = new ArrayList<>();
        backtrack(candidates, target, 0, new ArrayList<>(), results);
        return results;
    }

    private void backtrack(int[] candidates, int target, int start, List<Integer> current, List<List<Integer>> results) {
        if (target == 0) {
            results.add(new ArrayList<>(current));
            return;
        }
        for (int i = start; i < candidates.length; i++) {
            // Skip duplicates
            if (i > start && candidates[i] == candidates[i - 1]) continue;
            if (candidates[i] > target) break; // Pruning
            current.add(candidates[i]);
            backtrack(candidates, target - candidates[i], i + 1, current, results);
            current.remove(current.size() - 1);
        }
    }
}
```

---

### Complexity Analysis:
- **Time Complexity:** *O(2^n)* in the worst case, due to exploring power set, with pruning reducing the search space.
- **Space Complexity:** *O(n)* for recursion and temporary storage.

---

### Dry Run Example:
**Input:** `candidates = [10,1,2,7,6,1,5]`, `target=8`

| Step | Current Array | Current Target | Path        | Actions                                                   |
|-------|----------------|----------------|-------------|-----------------------------------------------------------|
| 1     | [1,1,2,5,6,7,10]| 8             | []          | Start, choose 1                                            |
| 2     | [1,2,5,6,7,10]| 7             | [1]         | Choose 1 (skip duplicate at same level)                   |
| 3     | [2,5,6,7,10]| 6             | [1,1]       | Choose 2                                                 |
| 4     | [5,6,7,10]| 4             | [1,1,2]     | Choose 5 (exceeds, prune)                                |
| 4     | [6,7,10]| 1             | [1,1,2]     | Skip 5, backtrack                                       |
| 3     | [2,5,6,7,10]| 6             | [1]         | Skip 2, choose 5                                       |
| ...   | ...            | ...            | ...         | Continue with other combinations                         |

*(The process continues systematically to find all valid combinations)*

---

## Summary:
- **Handling duplicates**: Always sort the array and skip duplicate elements at the same recursive level.
- **Pruning**: Break early if the current candidate exceeds the remaining target.
- **Backtracking**: Explore inclusion/exclusion with proper undo steps.

---

This guide covers the progression from naive to optimized solutions for **Combination Sum II**, providing insights for interview preparation and quick revision.
