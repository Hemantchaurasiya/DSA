# Number of Subsequences That Satisfy the Given Sum Condition

This problem asks us to find the number of subsequences in an array such that the sum of the minimum and maximum elements of the subsequence is less than or equal to a given target.

---

## 1. Brute Force Approach

### Core Idea:

Generate all possible subsequences and count those that satisfy the condition `min + max <= target`.

### Algorithm:

- Generate every possible subsequence (using recursion or bitmask).
- For each subsequence:
    - Determine its minimum and maximum elements.
    - Check if `min + max <= target`.
    - Increment count if condition holds.

### Java Code:

```java
public int numSubseqBruteForce(int[] nums, int target) {
    int n = nums.length;
    int count = 0;
    for (int mask = 0; mask < (1 << n); mask++) {
        List<Integer> subsequence = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            if ((mask & (1 << i)) != 0) {
                subsequence.add(nums[i]);
            }
        }
        if (subsequence.size() == 0) continue;
        int min = Collections.min(subsequence);
        int max = Collections.max(subsequence);
        if (min + max <= target) {
            count++;
        }
    }
    return count;
}

```

### Complexity Analysis:

- **Time Complexity:** *O(2^n * n)*
Generating all subsequences: `O(2^n)`.
For each subsequence, finding min and max: `O(n)` in worst case.
- **Space Complexity:** *O(n)* (for storing subsequences temporarily, can be optimized).

---

## 2. Better Solution: Sorting + Two Pointers + Preprocessing

### Core Idea:

- Sorting the array simplifies finding min and max.
- Use two pointers to efficiently count valid subsequences.
- For each element considered as the minimum, find the maximum index where the sum condition holds.

### Algorithm:

1. **Sort** the array.
2. Initialize **left = 0** and **right = n - 1**.
3. Precompute powers of 2 up to `n` for counting subsequences.
4. For each position `left`:
    - Move `right` pointer inward until `nums[left] + nums[right] <= target`.
    - The number of valid subsequences with `nums[left]` as the minimum:
        - All subsequences formed by choosing any subset of elements between `left` and `right` (inclusive), with `nums[left]` fixed as the min.
        - Count: `2^(right - left)`.

### Java Code:

```java
import java.util.Arrays;

public class Solution {
    public int numSubseq(int[] nums, int target) {
        int n = nums.length;
        Arrays.sort(nums);
        int[] pow = new int[n];
        pow[0] = 1;
        for (int i = 1; i < n; i++) {
            pow[i] = (pow[i - 1] * 2) % 1_000_000_007;
        }
        int result = 0;
        int left = 0, right = n - 1;
        while (left <= right) {
            if (nums[left] + nums[right] <= target) {
                result = (result + pow[right - left]) % 1_000_000_007;
                left++;
            } else {
                right--;
            }
        }
        return result;
    }
}

```

### Complexity Analysis:

- **Time Complexity:** *O(n log n)* (sorting) + *O(n)* (two pointers traversal) → **O(n log n)**
- **Space Complexity:** *O(n)* (for the precomputed powers array).

---

## 3. Dry Run Example

### Input:

```
nums = [3, 5, 6, 7]
target = 9

```

### Step-by-step:

| Step | Sorted Array | Initialize | left | right | Result | Explanation |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | [3, 5, 6, 7] | pow = [1,2,4,8] | 0 | 3 | 0 | Check `nums[left] + nums[right]`: 3 + 7 = 10 > 9 → right-- |
| 2 | [3, 5, 6, 7] |  | 0 | 2 | 0 | 3 + 6 = 9 ≤ 9 → count += 2^(2-0)=4 → left++ |
|  |  |  | 1 | 2 | 4 | Now: left=1, right=2. Check 5 + 6 = 11 > 9 → right-- |
| 3 |  |  | 1 | 1 | 4 | Now left=1, right=1. 5 + 5=10 > 9 → right-- → left=2, right=1 (done) |

**Total subsequences satisfying the condition:** **4**

---

## Summary:

- **Brute-force** is straightforward but inefficient.
- **Optimized approach** sorts the array and uses **two pointers** with precomputed powers of 2 to efficiently count valid subsequences.
- Final solution runs in **O(n log n)** due to sorting, suitable for large input sizes.
- Always precompute powers of 2 for counting subsets to avoid recomputation.

---

## Tips for Interviews:

- Recognize the need for sorting to handle min/max efficiently.
- Use two pointers for linear traversal after sorting.
- Precompute powers of 2 to count subsets quickly.
- Carefully handle modulo operations for large counts.
- Dry run with sample inputs to build intuition.

---

**Happy coding!**
