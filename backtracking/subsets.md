# Subsets

The **Subsets** problem involves generating all possible subsets (the power set) of a given set of distinct integers.

---

## 1. **Approach Name:** Brute Force (Backtracking / Power Set Enumeration)

### **Core Idea:**
- Generate all possible combinations of elements, where each element can be either included or excluded.
- Essentially, for a set of size `n`, there are `2^n` subsets.

### **Algorithm:**
1. Use recursion to explore two choices at each element:
   - Include the current element in the subset.
   - Exclude the current element.
2. Maintain a temporary list to store the current subset.
3. When you've processed all elements, add the current subset to the result list.
4. Backtrack to explore other possibilities.

### **Recursion Tree Diagram:**

```
Start with empty subset []

Level 0: Choose or not choose first element:
- Include 1: [1]
  - Include 2: [1,2]
    - Include 3: [1,2,3] -> Add to result
    - Exclude 3: [1,2]
  - Exclude 2: [1]
    - Include 3: [1,3]
    - Exclude 3: [1]
- Exclude 1: []
  - Include 2: [2]
    - Include 3: [2,3]
    - Exclude 3: [2]
  - Exclude 2: []

All leaf nodes are subsets.
```

### **Java Code:**
```java
import java.util.ArrayList;
import java.util.List;

public class Subsets {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] nums, int index, List<Integer> current, List<List<Integer>> result) {
        if (index == nums.length) {
            result.add(new ArrayList<>(current));
            return;
        }

        // Include nums[index]
        current.add(nums[index]);
        backtrack(nums, index + 1, current, result);

        // Exclude nums[index]
        current.remove(current.size() - 1);
        backtrack(nums, index + 1, current, result);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(2^n)** because each element has two choices (include/exclude), leading to `2^n` subsets.
- **Space Complexity:** **O(n)** for the recursion stack (maximum depth) plus the space for storing all subsets.

### **Dry Run (Example: nums = [1, 2])**

| Step | Current Subset | Result List | Comments                                   |
|-------|----------------|--------------|--------------------------------------------|
| Start | []             | []           | initial call                              |
| Include 1 | [1]            | []           | choose 1                                |
| Include 2 | [1, 2]        | []           | choose 2                                |
| Add [1,2] | -             | [[1,2]]     | base case reached, add to result        |
| Back to [1], exclude 2 | [1]            | [[1,2]]     | backtrack, remove 2                     |
| Exclude 1 | []             | [[1,2], [1]]| exclude 1, explore next                |
| Include 2 | [2]            | [[1,2], [1]]| include 2 in new branch                  |
| Add [2] | -              | [[1,2], [1], [2]] | add [2] to result               |
| Exclude 2 | []             | [[1,2], [1], [2]] | exclude 2, end branch             |

---

## 2. **Approach Name:** Iterative Approach (Bit Manipulation / Power Set Construction)

### **Core Idea:**
- Use binary representation to generate all subsets.
- For a set of size `n`, numbers from `0` to `2^n - 1` can represent all subsets, where each bit indicates inclusion/exclusion.

### **Algorithm:**
1. Loop through all numbers from `0` to `(1 << n) - 1`.
2. For each number, check each bit position:
   - If the bit is set, include the corresponding element.
3. Collect each subset generated this way.

### **Visualization:**
- For `nums = [1, 2, 3]`, binary `101` (5) corresponds to subset `[1, 3]`.

### **Java Code:**
```java
import java.util.ArrayList;
import java.util.List;

public class Subsets {
    public List<List<Integer>> subsets(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        int total = 1 << n; // 2^n

        for (int mask = 0; mask < total; mask++) {
            List<Integer> subset = new ArrayList<>();
            for (int i = 0; i < n; i++) {
                if (((mask >> i) & 1) == 1) {
                    subset.add(nums[i]);
                }
            }
            result.add(subset);
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(2^n * n)** because for each subset (2^n), we check up to n bits.
- **Space Complexity:** **O(2^n * n)** for storing all subsets.

---

## 3. **Approach Name:** Optimized Backtracking with Pruning (If applicable)

*Note:* For the pure subsets problem, the above backtracking approach is already optimal. However, in variants like subset sum, pruning can be applied.

---

## **Summary Table**

| Approach | Idea | Backtracking / Iterative | Time Complexity | Space Complexity |
|------------|-------|--------------------------|------------------|------------------|
| Brute Force | Generate all subsets via recursion | Yes | O(2^n) | O(n) (recursion stack) + output space |
| Iterative (Bitmask) | Use binary representation to generate subsets | Yes | O(2^n * n) | O(2^n * n) |

---

## **Final Tips for Interview:**
- Understand the recursive (backtracking) approach as it’s most intuitive.
- Be comfortable with bit manipulation for generating subsets efficiently.
- Remember the total number of subsets is `2^n`, which influences the complexity.
- Practice dry runs for small inputs to master the flow.

---

**Happy Coding!**
