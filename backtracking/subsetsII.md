# Subsets II

This problem asks us to generate **all possible subsets (the power set)** of a given array that may contain **duplicate elements**, ensuring **no duplicate subsets** are included in the output.

---

## 1. Approach Name: Brute Force (Backtracking with Duplicates Handling)

---

### **Core Idea:**
Generate all subsets by exploring choices at each element (include or exclude), then remove duplicates from the result. Handling duplicates is essential to avoid duplicate subsets.

---

### **Algorithm:**

1. **Sort the input array** to bring duplicates together.
2. Use **backtracking** to explore all subset possibilities:
   - At each recursive call, decide whether to include the current element.
   - **Skip duplicates**: When choosing to include an element, skip over subsequent duplicates to prevent duplicate subsets.
3. Add each generated subset to the result list.
4. Return the list of all subsets.

---

### **Recursion Tree Diagram:**

```
For input: [1, 2, 2]

Start:
- Include [] 
  - Include 1
    - Include [1]
      - Include 2
        - Include [1, 2]
        - Exclude 2
      - Exclude 2
        - Include [1]
        - Exclude [1]
  - Exclude 1
    - Include 2
      - Include [2]
      - Exclude 2
    - Exclude 2
```

*(Note: For simplicity, visualize the tree with branches representing include/exclude decisions, with duplicate handling at the inclusion step.)*

---

### **Java Code:**

```java
import java.util.*;

public class SubsetsWithDup {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] nums, int start, List<Integer> current, List<List<Integer>> result) {
        result.add(new ArrayList<>(current));

        for (int i = start; i < nums.length; i++) {
            // Skip duplicates
            if (i > start && nums[i] == nums[i - 1]) continue;

            current.add(nums[i]);
            backtrack(nums, i + 1, current, result);
            current.remove(current.size() - 1);
        }
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:** **O(2^n * n)**  
  Generate all subsets (2^n). For each subset, copying takes O(n) in the worst case.

- **Space Complexity:** **O(n * 2^n)**  
  The space required to store all subsets, each of size up to n, plus recursion stack.

---

### **Dry Run with Input: [1, 2, 2]**

| Step | Current Subset | Action | Result List | Notes |
|-------|----------------|---------|--------------|--------|
| Start | []             | -       | [[]]         | initial |
| Include 1 | [1] | Choose 1 | [[], [1]] | |
| Include 2 | [1, 2] | Choose 2 | [[], [1], [1, 2]] | |
| Include 2 | [1, 2, 2] | Choose duplicate 2 | [[], [1], [1, 2], [1, 2, 2]] | skip duplicate at same level |
| Backtrack | [1, 2] | Remove last 2 | [[], [1], [1, 2]] | |
| Exclude last 2 | [1] | Remove last 2 | [[], [1], [1, 2]] | |
| Exclude 2 at index 1 | [1] | Skip duplicate | - | handled via `if` condition |
| Exclude 1 | [] | Remove 1 | [[], [1]] | explore other branches |
| Include 2 at index 1 | [2] | Choose 2 | [[], [1], [2]] | |
| Include 2 at index 2 | [2, 2] | Choose 2 | [[], [1], [2], [2, 2]] | |
| Backtrack | [2] | Remove last 2 | [[], [1], [2], [2, 2]] | |
| Exclude last 2 | [2] | Remove 2 | [[], [1], [2], [2, 2]] | |

Result: `[[], [1], [2], [1, 2], [2, 2], [1, 2, 2]]`

---

## 2. Approach Name: Optimized Backtracking with Sorting and Duplicate Handling

---

### **Core Idea:**
Sort the array to group duplicates together; during recursion, **skip duplicate elements** at the same recursive level. This avoids generating duplicate subsets upfront, reducing the need for post-processing.

---

### **Algorithm:**

1. Sort `nums`.
2. Use **backtracking**:
   - At each recursive level, iterate from `start` to end.
   - If the current element is the same as the previous element **and** the index is greater than `start`, **skip** it.
   - Otherwise, include the element and recurse.
3. Add all generated subsets directly to the result.

---

### **Recursion Tree Diagram:**

Same as above but with duplicate skipping, the branches for duplicate elements are pruned early.

---

### **Java Code:**

```java
import java.util.*;

public class SubsetsWithDupOptimized {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] nums, int start, List<Integer> current, List<List<Integer>> result) {
        result.add(new ArrayList<>(current));
        for (int i = start; i < nums.length; i++) {
            if (i > start && nums[i] == nums[i - 1]) continue; // skip duplicates
            current.add(nums[i]);
            backtrack(nums, i + 1, current, result);
            current.remove(current.size() - 1);
        }
    }
}
```

*(Note: The code is similar to approach 1 but emphasizes duplicate skipping at the iteration level.)*

---

### **Complexity Analysis:**

- **Time Complexity:** **O(2^n * n)**, similar reasoning as above.
- **Space Complexity:** **O(n * 2^n)**.

---

### **Dry Run with Input: [1, 2, 2]**

Follows the same steps but avoids generating duplicate subsets due to the `if` condition to skip duplicate elements at the same recursive level.

---

## 3. Approach Name: Iterative Subset Generation with Duplicate Handling

---

### **Core Idea:**
Build subsets iteratively. For each element:
- For each existing subset, add the current element.
- To handle duplicates:
  - If the current element is a duplicate, only extend the subsets generated in the **previous step** to avoid duplicates.

---

### **Algorithm:**

1. Sort the array.
2. Initialize `result` with an empty subset.
3. Use a variable `startIndex` to track where new subsets are added in the previous iteration.
4. For each element:
   - If it's a duplicate, only extend subsets added in the last iteration.
   - Else, extend all subsets.

---

### **Java Code:**

```java
import java.util.*;

public class SubsetsWithDupIterative {
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        result.add(new ArrayList<>());
        int startIndex = 0, prevSize = 0;

        for (int i = 0; i < nums.length; i++) {
            int size = result.size();
            int start = (i > 0 && nums[i] == nums[i - 1]) ? prevSize : 0;

            prevSize = size;
            for (int j = start; j < size; j++) {
                List<Integer> subset = new ArrayList<>(result.get(j));
                subset.add(nums[i]);
                result.add(subset);
            }
        }
        return result;
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:** **O(2^n * n)**.
- **Space Complexity:** **O(n * 2^n)**.

---

### **Dry Run with Input: [1, 2, 2]**

| Iteration | Current Element | Existing subsets | New subsets added | Result after iteration | Notes |
|------------|------------------|--------------------|-------------------|------------------------|--------|
| 1          | 1                | [[]]               | [ [1] ]          | [ [], [1] ]            | |
| 2          | 2                | [ [], [1] ]       | [ [2], [1, 2] ] | [ [], [1], [2], [1, 2] ] | |
| 3          | 2 (duplicate)    | [ [], [1], [2], [1, 2] ] | [ [2], [1, 2], [2, 2] ] | Final result includes only new subsets for duplicates |

---

# Summary & Best Practices:
- **Sorting** helps in handling duplicates efficiently.
- **Backtracking with skipping duplicates** is the most common approach for such problems.
- Always consider the **recursive tree structure** during implementation.
- For large inputs, prefer **optimized backtracking** to prune duplicate branches early.
- Be mindful of **space and time complexities**; exponential growth is inherent in subset generation problems.

---

This comprehensive guide should serve as a quick revision and deep understanding of solving **Subsets II** efficiently.
