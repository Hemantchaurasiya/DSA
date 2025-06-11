# Permutations

This guide provides a comprehensive overview of solving the **Permutations** problem, covering various approaches from brute-force to optimal solutions. It is structured for quick revision and interview preparation.

---

## Problem Statement
Given an array or string, generate all possible permutations of its elements.

---

## Approach 1: Brute Force (Backtracking with Swap)

### Core Idea
Generate permutations by swapping each element with every other element recursively. This approach explores all possible arrangements by swapping elements in place.

### Algorithm
1. Fix the first element, then recursively generate permutations for the remaining elements.
2. For each position, swap the current element with each subsequent element.
3. After recursive calls, backtrack by swapping back to restore the original array.

### Recursion Tree Diagram
```
Initial array: [1, 2, 3]
Level 0: Fix 1, permute [2, 3]
    - Swap 1 and 2 -> [2, 1, 3], recurse
        - Fix 2, permute [1, 3]
            - Swap 1 and 3 -> [2, 3, 1], recurse
                - Permutation: [2, 3, 1]
            - Backtrack
        - Swap back
    - Swap 1 and 3 -> [3, 2, 1], recurse
        - Permutation: [3, 2, 1]
    - Backtrack
- Swap back
```

### Java Code
```java
public class Permutations {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(nums, 0, result);
        return result;
    }
    
    private void backtrack(int[] nums, int start, List<List<Integer>> result) {
        if (start == nums.length) {
            List<Integer> permutation = new ArrayList<>();
            for (int num : nums) permutation.add(num);
            result.add(permutation);
            return;
        }
        for (int i = start; i < nums.length; i++) {
            swap(nums, start, i);
            backtrack(nums, start + 1, result);
            swap(nums, start, i); // backtrack
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(n!)*  
  Because all permutations of `n` elements are generated.

- **Space Complexity:** *O(n)*  
  For recursion stack and temporary storage during permutation formation.

### Dry Run (Input: [1, 2, 3])
| Step | Current Array | Actions | Resulting Permutations |
|-------|-----------------|---------|------------------------|
| 0     | [1, 2, 3]     | Fix 1, recurse | - |
| 1     | [2, 1, 3]     | Swap 1 & 2, recurse | - |
| 2     | [2, 3, 1]     | Fix 2, recurse | [2, 3, 1] |
| 2     | [3, 2, 1]     | Swap 2 & 3, recurse | [3, 2, 1] |
| 1     | [1, 2, 3]     | Swap back, fix 1 | - |
| 0     | [1, 2, 3]     | Swap 1 & 3, recurse | - |
| 1     | [3, 2, 1]     | Fix 3, recurse | [3, 2, 1] |

---

## Approach 2: Using Python's Built-in Functions (for understanding)
*(Not recommended for interviews but useful for quick testing)*

### Core Idea
Utilize language-specific libraries to generate permutations efficiently.

### Example (Python)
```python
import itertools
permutations = list(itertools.permutations([1, 2, 3]))
```

---

## Approach 3: **Optimized Backtracking with Pruning** (if applicable)
While standard permutation generation is already optimal in generating all permutations, in some cases, you can prune by avoiding duplicate permutations when input has duplicates.

### Core Idea
Skip duplicate elements during permutation generation to avoid redundant permutations.

### Algorithm
1. Sort the array.
2. During recursive calls, skip elements that are the same as previously considered elements at the same level.

### Java Code (with duplicate handling)
```java
public class PermutationsWithDup {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        Arrays.sort(nums);
        boolean[] used = new boolean[nums.length];
        backtrack(nums, used, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] nums, boolean[] used, List<Integer> temp, List<List<Integer>> result) {
        if (temp.size() == nums.length) {
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (used[i]) continue;
            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue; // skip duplicates
            used[i] = true;
            temp.add(nums[i]);
            backtrack(nums, used, temp, result);
            temp.remove(temp.size() - 1);
            used[i] = false;
        }
    }
}
```

### Complexity Analysis
Same as the brute-force approach, but with reduced permutations when duplicates exist.

---

## Summary

| Approach | Idea | When to Use | Time Complexity | Space Complexity |
|----------|-------|--------------|-------------------|------------------|
| Brute Force | Swap elements recursively | Basic permutation generation | *O(n!)* | *O(n)* (recursion stack) |
| Built-in / Library | Use language features | Quick testing, small inputs | *O(n!)* | *O(n)* |
| Backtracking with Duplicate Handling | Skip duplicates to reduce permutations | Inputs with duplicates | *O(n!)* (but fewer in practice) | *O(n)* |

---

## Final Tips for Interviews:
- Understand the recursive backtracking pattern.
- Practice generating permutations both with and without duplicates.
- Be mindful of optimizing by pruning duplicate permutations.
- Always analyze the time and space complexities.

---

**End of Permutations DSA Revision Note**
