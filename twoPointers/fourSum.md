# 4Sum Problem

The **4Sum** problem asks us to find all unique quadruplets in an array that sum up to a given target value.

---

## 1. Brute Force Approach

### **Core Idea:**
Check **every possible combination** of four elements and verify if their sum equals the target.

### **Algorithm:**
- Use four nested loops to pick all quadruples.
- For each quadruple `(nums[i], nums[j], nums[k], nums[l])`, check if the sum equals the target.
- To avoid duplicates, store quadruples in a set or sort each quadruple and skip duplicates.

### **Java Code:**
```java
import java.util.*;

public class FourSumBruteForce {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Set<List<Integer>> result = new HashSet<>();
        int n = nums.length;
        for (int i = 0; i < n - 3; i++) {
            for (int j = i + 1; j < n - 2; j++) {
                for (int k = j + 1; k < n - 1; k++) {
                    for (int l = k + 1; l < n; l++) {
                        if (nums[i] + nums[j] + nums[k] + nums[l] == target) {
                            List<Integer> quadruple = Arrays.asList(nums[i], nums[j], nums[k], nums[l]);
                            Collections.sort(quadruple);
                            result.add(quadruple);
                        }
                    }
                }
            }
        }
        return new ArrayList<>(result);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n^4)** — Four nested loops over the array.
- **Space Complexity:** **O(k)**, where *k* is the number of quadruples stored (due to the set).

### **Dry Run:**
Suppose `nums = [1, 0, -1, 0, -2, 2]`, `target = 0`.

- Loop i=0 (`nums[i]=1`):
  - Loop j=1 (`nums[j]=0`):
    - Loop k=2 (`nums[k]=-1`):
      - Loop l=3 (`nums[l]=0`):
        - Sum = 1 + 0 + (-1) + 0 = 0 → store `[ -1, 0, 0, 1 ]`
      - Continue for other l values...
  - Continue for other i, j combinations.

---

## 2. Better Solution: Sorting + Two Pointers

### **Core Idea:**
Sort the array, then fix two elements and use **two pointers** to find the remaining two elements that sum up to `target - (fixed elements)`.

### **Algorithm:**
1. Sort the array.
2. Loop `i` from `0` to `n-4`.
3. Avoid duplicates for `i`.
4. Loop `j` from `i+1` to `n-3`.
5. Avoid duplicates for `j`.
6. Initialize two pointers: `left = j+1`, `right = n-1`.
7. While `left < right`:
   - Calculate current sum: `nums[i] + nums[j] + nums[left] + nums[right]`.
   - If sum == target, add quadruple to result, move `left` and `right` avoiding duplicates.
   - If sum < target, move `left++`.
   - If sum > target, move `right--`.

### **Java Code:**
```java
import java.util.*;

public class FourSumTwoPointers {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> result = new ArrayList<>();
        int n = nums.length;
        Arrays.sort(nums);
        for (int i = 0; i < n - 3; i++) {
            if (i > 0 && nums[i] == nums[i - 1]) continue; // skip duplicates
            for (int j = i + 1; j < n - 2; j++) {
                if (j > i + 1 && nums[j] == nums[j - 1]) continue; // skip duplicates
                int left = j + 1;
                int right = n - 1;
                while (left < right) {
                    int sum = nums[i] + nums[j] + nums[left] + nums[right];
                    if (sum == target) {
                        result.add(Arrays.asList(nums[i], nums[j], nums[left], nums[right]));
                        // Skip duplicates for left
                        while (left < right && nums[left] == nums[left + 1]) left++;
                        // Skip duplicates for right
                        while (left < right && nums[right] == nums[right - 1]) right--;
                        left++;
                        right--;
                    } else if (sum < target) {
                        left++;
                    } else {
                        right--;
                    }
                }
            }
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(n^3)** — Outer two loops + inner two-pointer traversal.
- **Space Complexity:** **O(1)** (excluding output storage).

### **Dry Run:**
Input: `nums = [1, 0, -1, 0, -2, 2]`, `target=0`.

- Sorted `nums` = `[-2, -1, 0, 0, 1, 2]`.
- Fix `i=0` (`-2`):
  - Fix `j=1` (`-1`):
    - `left=2`, `right=5`:
      - sum = -2 + (-1) + 0 + 2 = -1 < 0 → `left++`.
      - `left=3`, sum = -2 + (-1) + 0 + 2 = -1 → `left++`.
      - `left=4`, sum = -2 + (-1) + 1 + 2 = 0 → store `[-2, -1, 1, 2]`.
      - Move `left` and `right` accordingly, skipping duplicates.
- Continue for other `i`, `j`.

---

## 3. Most Optimal Solution: Sorting + Two Pointers with Duplicate Handling

This approach is essentially the second approach but with careful implementation and pruning for efficiency. It is the standard and most efficient method for **4Sum** within reasonable constraints.

### **Summary:**
- Sort the array to ease duplicate handling and two-pointer traversal.
- Fix two elements with nested loops.
- Use two pointers to find remaining pairs.
- Skip duplicate elements to ensure unique quadruplets.
- Achieve **O(n^3)** time complexity, which is the best possible for this problem.

---

## **Key Takeaways for 4Sum:**
- Always sort the array.
- Use nested loops to fix the first two elements.
- Use two pointers for the remaining two elements.
- Avoid duplicates at each step.
- Time Complexity: **O(n^3)**.
- Space Complexity: **O(1)** (excluding output).

---

Feel free to practice with different inputs to master the approach!
