# 3Sum

The **3Sum** problem involves finding all unique triplets in an array that sum up to zero.

---

## 1. Brute Force Approach

### **Core Idea:**
Check every possible triplet in the array and see if it sums to zero, while avoiding duplicate triplets.

### **Algorithm:**
1. Sort the array to help in handling duplicates.
2. Use three nested loops to pick triplets `(i, j, k)`:
   - Outer loop: iterate `i` from `0` to `n-3`.
   - Middle loop: iterate `j` from `i+1` to `n-2`.
   - Inner loop: iterate `k` from `j+1` to `n-1`.
3. For each triplet, check if `nums[i] + nums[j] + nums[k] == 0`.
4. Use a data structure (like a `Set`) to avoid duplicate triplets.
5. Collect all valid triplets in a result list.

### **Java Code:**
```java
public List<List<Integer>> threeSumBruteForce(int[] nums) {
    Arrays.sort(nums);
    Set<List<Integer>> result = new HashSet<>();
    int n = nums.length;
    for (int i = 0; i < n - 2; i++) {
        for (int j = i + 1; j < n - 1; j++) {
            for (int k = j + 1; k < n; k++) {
                if (nums[i] + nums[j] + nums[k] == 0) {
                    result.add(Arrays.asList(nums[i], nums[j], nums[k]));
                }
            }
        }
    }
    return new ArrayList<>(result);
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n^3)` due to triple nested loops.
- **Space Complexity:** `O(n)` for storing the result and auxiliary data structures.

### **Dry Run:**
- Input: `[-1, 0, 1, 2, -1, -4]`
- Sorted: `[-4, -1, -1, 0, 1, 2]`
- Check all triplets:
  - `(-4, -1, 1)` → sum= -4-1+1=-4, no
  - `(-4, -1, 2)` → sum=-4-1+2=-3, no
  - `(-4, 0, 2)` → sum=-4+0+2=-2, no
  - `(-1, -1, 2)` → sum=-1-1+2=0 → add
  - Continue until all triplets checked.

---

## 2. Two-Pointer Approach (Most Optimal)

### **Core Idea:**
Sort the array and fix one element at a time, then use two pointers to find the other two elements that sum to `-fixed_element`. This reduces the complexity significantly.

### **Algorithm:**
1. Sort the array.
2. Iterate `i` from `0` to `n-3`:
   - If `nums[i]` is the same as `nums[i-1]` (skip duplicates).
   - Set two pointers: `left = i+1`, `right = n-1`.
   - While `left < right`:
     - Calculate `sum = nums[i] + nums[left] + nums[right]`.
     - If `sum == 0`:
       - Add the triplet to the result.
       - Move `left` and `right` pointers, skipping duplicates.
     - Else if `sum < 0`:
       - Increment `left`.
     - Else:
       - Decrement `right`.
3. Return the list of triplets.

### **Java Code:**
```java
public List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> result = new ArrayList<>();
    int n = nums.length;

    for (int i = 0; i < n - 2; i++) {
        if (i > 0 && nums[i] == nums[i - 1]) continue; // skip duplicates
        int left = i + 1, right = n - 1;

        while (left < right) {
            int sum = nums[i] + nums[left] + nums[right];
            if (sum == 0) {
                result.add(Arrays.asList(nums[i], nums[left], nums[right]));
                // Skip duplicates for left
                while (left < right && nums[left] == nums[left + 1]) left++;
                // Skip duplicates for right
                while (left < right && nums[right] == nums[right - 1]) right--;
                left++;
                right--;
            } else if (sum < 0) {
                left++;
            } else {
                right--;
            }
        }
    }
    return result;
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n^2)` because for each element, the two-pointer search runs in linear time.
- **Space Complexity:** `O(1)` aside from the output list, since sorting is in-place.

### **Dry Run:**
- Input: `[-1, 0, 1, 2, -1, -4]`
- Sorted: `[-4, -1, -1, 0, 1, 2]`
- Iteration:
  - `i=0` (`nums[i]=-4`):
    - `left=1`, `right=5`
    - sum = `-4 + (-1) + 2 = -3` → `sum<0` → `left++`
    - `left=2`, `right=5`
    - sum = `-4 + (-1) + 2 = -3` → `left++`
    - `left=3`, `right=5`
    - sum = `-4 + 0 + 2= -2` → `left++`
    - `left=4`, `right=5`
    - sum = `-4 + 1 + 2= -1` → `left++`
    - `left=5`, stop
  - `i=1` (`nums[i]=-1`):
    - `left=2`, `right=5`
    - sum = `-1 + (-1) + 2= 0` → add triplet `[-1,-1,2]`
    - Skip duplicates for `left` and `right` if any
    - `left=3`, `right=4`
    - sum = `-1 + 0 + 1= 0` → add triplet `[-1,0,1]`
    - Continue moving pointers, skipping duplicates
  - `i=2` (`nums[i]=-1`): skip because duplicate
  - Continue until all triplets are checked.

---

# Summary

| Approach | Core Idea | Implementation | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- |
| **Brute Force** | Check all triplets | Triple nested loops | `O(n^3)` | `O(n)` for result storage |
| **Optimized Two-Pointer** | Fix one element, find other two with two pointers | Sorting + two pointers | `O(n^2)` | `O(1)` (excluding result list) |

---

This comprehensive guide covers multiple strategies for solving **3Sum**, emphasizing the most efficient approach for interview readiness and quick revision.
