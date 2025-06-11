# Permutations II

---

## Problem Overview
**Permutations II** asks us to generate all unique permutations of a collection of numbers that may contain duplicates.

---

## Approach 1: Brute Force (Generate All Permutations & Filter Duplicates)

### Core Idea:
Generate all possible permutations (including duplicates), then remove duplicate permutations. This approach is simple but inefficient.

### Algorithm:
1. Generate all permutations of the input array using a backtracking approach.
2. Store each permutation in a set to eliminate duplicates.
3. Return the set as the final list of unique permutations.

### Recursion Tree:
- At each level, choose a number not used yet.
- Recursively generate permutations for remaining numbers.
- On reaching the end, add the current permutation to the set.

### Java Code:
```java
import java.util.*;

public class PermutationsII {
    public List<List<Integer>> permuteUnique(int[] nums) {
        Set<List<Integer>> result = new HashSet<>();
        backtrack(nums, new boolean[nums.length], new ArrayList<>(), result);
        return new ArrayList<>(result);
    }

    private void backtrack(int[] nums, boolean[] used, List<Integer> temp, Set<List<Integer>> result) {
        if (temp.size() == nums.length) {
            result.add(new ArrayList<>(temp));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (used[i]) continue;
            used[i] = true;
            temp.add(nums[i]);
            backtrack(nums, used, temp, result);
            temp.remove(temp.size() - 1);
            used[i] = false;
        }
    }
}
```

### Complexity Analysis:
- **Time Complexity:** *O(N! * N)* — generating all permutations (N!) and copying each permutation (O(N)) to set.
- **Space Complexity:** *O(N! * N)* — for storing all permutations in the worst case.

### Dry Run:
Input: `[1, 1, 2]`
- Generate permutations:
  - [1, 1, 2]
  - [1, 2, 1]
  - [2, 1, 1]
- Store in set to avoid duplicates.
- Final output: `[[1, 1, 2], [1, 2, 1], [2, 1, 1]]`.

---

## Approach 2: Sorting + Backtracking with Duplicate Skipping (Optimal Solution)

### Core Idea:
Sort the array to bring duplicates together. During backtracking, skip duplicate numbers at the same recursive level to avoid generating duplicate permutations.

### Algorithm:
1. **Sort** the input array.
2. Use backtracking to generate permutations.
3. At each recursive step:
   - Iterate over the elements.
   - If the current element is the same as the previous element and the previous element was not used in this recursive branch, skip it.
   - Use a boolean array to mark elements as used.
4. When the permutation is complete, add it to the result list.

### Recursion Tree:
- At each level, select an unused number.
- Skip duplicate numbers that would produce duplicate permutations.
- Recurse with the updated used array.
- Backtrack after exploring each option.

### Java Code:
```java
import java.util.*;

public class PermutationsII {
    public List<List<Integer>> permuteUnique(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
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
            // Skip duplicates
            if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) continue;

            used[i] = true;
            temp.add(nums[i]);
            backtrack(nums, used, temp, result);
            temp.remove(temp.size() - 1);
            used[i] = false;
        }
    }
}
```

### Complexity Analysis:
- **Time Complexity:** *O(N!)* — each unique permutation is generated once, skipping duplicates reduces redundant recursive calls.
- **Space Complexity:** *O(N)* — for the recursion stack and used array; result storage can be up to *O(N! * N)* in the worst case.

### Dry Run:
Input: `[1, 1, 2]`
- Sorted array: `[1, 1, 2]`
- Recursive steps:
  - Pick first `1`:
    - Skip second `1` (since it's the same as previous and previous is unused)
    - Pick `2`:
      - Complete permutation `[1, 2, 1]`
  - Pick second `1` (since first is used):
    - Pick `2`:
      - Complete permutation `[1, 2, 1]`
  - Pick `2`:
    - Pick first `1`:
      - Pick second `1`:
        - Complete permutation `[2, 1, 1]`
- Final result: `[[1, 1, 2], [1, 2, 1], [2, 1, 1]]`.

---

## Summary:
| Approach | Idea | Key Points | Time Complexity | Space Complexity |
|------------|-------|--------------|-----------------|------------------|
| Brute Force | Generate all permutations, filter duplicates | Simple, but inefficient for large inputs | O(N! * N) | O(N! * N) |
| Sorting + Backtracking with skipping duplicates | Sort input, skip duplicate choices during recursion | Efficient, avoids generating duplicate permutations | O(N!) | O(N) |

---

## Final Tips:
- Sorting helps in easily identifying duplicates.
- When skipping duplicates, ensure to check `nums[i] == nums[i - 1]` and whether `nums[i - 1]` was used.
- Use backtracking with a boolean array to track used elements.
- Always consider edge cases: empty array, all duplicates, large inputs.

---

This guide provides a comprehensive overview for quick revision and interview preparation on the **Permutations II** problem.
