# Partition to K Equal Sum Subsets

This problem asks whether an array can be partitioned into **k** subsets where each subset sums to the **same value**. It is a classic backtracking problem with multiple solution approaches, gradually optimized for efficiency.

---

## **Problem Statement**
Given an array `nums` of positive integers and an integer `k`, determine if it's possible to partition the array into `k` subsets each with **equal sum**.

---

## **1. Brute Force Approach**

### **Core Idea**
- Generate all possible partitions into `k` subsets.
- Check if any partition results in all subsets having the same sum.

### **Algorithm**
1. Calculate the total sum of all elements.
2. If total sum is not divisible by `k`, return `false`.
3. The target sum for each subset is `totalSum / k`.
4. Use recursion to assign each element to one of the `k` subsets:
   - Keep track of the sum of each subset.
   - If at any point, a subset's sum exceeds the target, backtrack.
5. When all elements are assigned, check if all subset sums are equal to the target.

### **Recursion Tree**
- At each level, assign the current element to one of the `k` subsets.
- The tree branches out with each choice, exploring all assignments.

### **Java Code**
```java
public class PartitionKSubsets {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        if (totalSum % k != 0) return false;
        int target = totalSum / k;
        boolean[] used = new boolean[nums.length];
        return backtrack(nums, used, k, 0, 0, target);
    }

    private boolean backtrack(int[] nums, boolean[] used, int k, int start, int currentSum, int target) {
        if (k == 1) return true; // last subset will be valid if others are
        if (currentSum == target) // current subset is filled, move to next subset
            return backtrack(nums, used, k - 1, 0, 0, target);
        for (int i = start; i < nums.length; i++) {
            if (used[i]) continue;
            if (currentSum + nums[i] > target) continue;
            used[i] = true;
            if (backtrack(nums, used, k, i + 1, currentSum + nums[i], target))
                return true;
            used[i] = false;
        }
        return false;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(k^n)* in worst case, since each element can go into any of the `k` subsets (exponential).
- **Space Complexity:** *O(n)* for the recursion stack and used array.

### **Dry Run Example**
- Input: `nums = [4, 3, 2, 3, 5, 2, 1]`, `k = 4`
- Total sum = 20, target = 5.
- Assign elements recursively to subsets, backtracking whenever sums exceed 5 or no valid assignment.

---

## **2. Better Solution (Sorting + Pruning)**

### **Core Idea**
- Sort the array in descending order to assign larger elements first.
- Prune branches early if an element cannot fit into any subset.
- Avoid redundant permutations by skipping identical elements in the same recursive call.

### **Algorithm**
1. Sort `nums` in decreasing order.
2. Check if the largest element exceeds the target sum; if yes, return `false`.
3. Use backtracking with an array `subsetSums` to track sums of each subset.
4. For each element:
   - Try adding it to each subset if it doesn't exceed the target sum.
   - If adding the element leads to a valid solution, recurse.
   - Backtrack if no valid placement is found.

### **Recursion Tree**
- Similar to brute-force but with pruning:
  - Larger elements tried first.
  - Skipping over duplicates reduces redundant attempts.

### **Java Code**
```java
import java.util.Arrays;

public class PartitionKSubsets {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        if (totalSum % k != 0) return false;
        int target = totalSum / k;
        Arrays.sort(nums); // sort in ascending
        reverse(nums); // reverse to descending
        if (nums[0] > target) return false;

        int[] subsetSums = new int[k];
        return backtrack(nums, subsetSums, 0, target);
    }

    private boolean backtrack(int[] nums, int[] subsetSums, int index, int target) {
        if (index == nums.length) {
            for (int sum : subsetSums)
                if (sum != target) return false;
            return true;
        }

        for (int i = 0; i < subsetSums.length; i++) {
            if (subsetSums[i] + nums[index] > target) continue;
            subsetSums[i] += nums[index];
            if (backtrack(nums, subsetSums, index + 1, target))
                return true;
            subsetSums[i] -= nums[index];
            if (subsetSums[i] == 0) break; // optimization to avoid duplicate states
        }
        return false;
    }

    private void reverse(int[] arr) {
        int i = 0, j = arr.length - 1;
        while (i < j) {
            int temp = arr[i];
            arr[i] = arr[j];
            arr[j] = temp;
            i++; j--;
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(k * 2^n)* in the worst case, but pruning significantly reduces runtime.
- **Space Complexity:** *O(n)* for recursion stack and subset sums array.

### **Dry Run Example**
- Input: `nums = [4, 3, 2, 3, 5, 2, 1]`, `k=4`.
- Sorted: `[5, 4, 3, 3, 2, 2, 1]`.
- Assign elements greedily, backtracking when sum exceeds target (5).

---

## **3. Most Optimal Solution (Bitmask + DP / Memoization)**

### **Core Idea**
- Use bitmasking to track which elements are used.
- Use memoization to avoid repeated states.
- Efficiently check subsets forming the required sum.

### **Algorithm**
1. Calculate total sum and derive target sum.
2. Use a recursive function with:
   - `usedMask` (bitmask representing used elements),
   - `currentSum` (sum of the current subset being formed),
   - `k` (remaining subsets to fill).
3. Memoize states based on `usedMask` and `currentSum`.
4. When current sum reaches target, recurse for the next subset with reset current sum.
5. Terminate when all elements are used.

### **Recursion Tree**
- Each node represents a particular set of used elements (bitmask).
- Branches represent choosing unused elements to add to the current subset.

### **Java Code**
```java
import java.util.HashMap;

public class PartitionKSubsets {
    public boolean canPartitionKSubsets(int[] nums, int k) {
        int totalSum = 0;
        for (int num : nums) totalSum += num;
        if (totalSum % k != 0) return false;
        int target = totalSum / k;

        Arrays.sort(nums);
        if (nums[nums.length - 1] > target) return false;

        HashMap<Integer, Boolean> memo = new HashMap<>();
        boolean[] used = new boolean[nums.length];

        return backtrack(nums, used, 0, 0, k, target, memo);
    }

    private boolean backtrack(int[] nums, boolean[] used, int start, int currentSum, int k, int target, HashMap<Integer, Boolean> memo) {
        if (k == 1) return true; // last subset
        int usedMask = 0;
        for (int i = 0; i < used.length; i++) {
            if (used[i]) usedMask |= (1 << i);
        }

        if (currentSum == target) {
            return backtrack(nums, used, 0, 0, k - 1, target, memo);
        }

        if (memo.containsKey(usedMask))
            return memo.get(usedMask);

        for (int i = start; i < nums.length; i++) {
            if (used[i]) continue;
            if (currentSum + nums[i] > target) continue;
            used[i] = true;
            if (backtrack(nums, used, i + 1, currentSum + nums[i], k, target, memo)) {
                used[i] = false;
                memo.put(usedMask, true);
                return true;
            }
            used[i] = false;
        }
        memo.put(usedMask, false);
        return false;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(k * 2^n)* due to memoization over subsets.
- **Space Complexity:** *O(n)* for recursion and memoization cache.

---

## **Summary Table**

| Approach                 | Idea                                    | Optimization | Time Complexity     | Space Complexity | Suitable for             |
|--------------------------|-----------------------------------------|--------------|---------------------|------------------|-------------------------|
| Brute Force              | Generate all partitions                | No           | O(k^n)             | O(n)             | Small `n`, initial checks |
| Sorting + Pruning       | Assign larger first, prune branches     | Yes          | ~O(k * 2^n)        | O(n)             | Medium `n`, optimized runtime |
| Bitmask + Memoization   | Use bitmask for used elements, DP memo | Yes          | O(k * 2^n)        | O(n)             | Larger `n`, efficient solutions |

---

## **Final Tips for Interview**
- Always check the sum divisibility (`totalSum % k == 0`).
- Use sorting to optimize backtracking.
- Prune early when possible.
- For large `n`, prefer memoization with bitmasking.
- Handle edge cases (e.g., largest element > target).

---

This structured guide should help you understand, implement, and optimize solutions to the **Partition to K Equal Sum Subsets** problem efficiently!
