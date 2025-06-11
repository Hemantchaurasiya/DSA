# Valid Triangle Number

This problem is a classic example of combinatorial reasoning combined with efficient array processing. The goal is to count the number of triplets `(i, j, k)` in an array such that these indices form a valid triangle, i.e., the lengths satisfy the triangle inequality:

- `nums[i] + nums[j] > nums[k]`
- `nums[i] + nums[k] > nums[j]`
- `nums[j] + nums[k] > nums[i]`

Since the array contains side lengths, the key is to count triplets that satisfy these conditions efficiently.

---

## Approach 1: Brute Force

### Core Idea

Check all possible triplets `(i, j, k)` and verify whether they can form a triangle.

### Algorithm

1. Sort the array. Sorting helps in simplifying the inequality checks.
2. Use three nested loops:
    - Loop `i` from 0 to `n-3`.
    - Loop `j` from `i+1` to `n-2`.
    - Loop `k` from `j+1` to `n-1`.
3. For each triplet `(i, j, k)`, check if `nums[i] + nums[j] > nums[k]`. Since the array is sorted, if this holds, the other two triangle inequalities are automatically satisfied.
4. Increment the count for each valid triplet.

### Java Code

```java
public int triangleNumber(int[] nums) {
    Arrays.sort(nums);
    int count = 0;
    int n = nums.length;
    for (int i = 0; i < n - 2; i++) {
        for (int j = i + 1; j < n - 1; j++) {
            for (int k = j + 1; k < n; k++) {
                if (nums[i] + nums[j] > nums[k]) {
                    count++;
                }
            }
        }
    }
    return count;
}

```

### Complexity Analysis

- **Time Complexity:** `O(n^3)` — three nested loops over `n`.
- **Space Complexity:** `O(1)` — only a few variables used, no extra data structures.

### Dry Run

- Input: `[2, 2, 3, 4]`
- Sorted: `[2, 2, 3, 4]`

| i | j | k | nums[i] | nums[j] | nums[k] | Check: nums[i] + nums[j] > nums[k]? | Valid? |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 1 | 2 | 2 | 2 | 3 | 2 + 2 > 3? 4 > 3 → Yes | Count++ |
| 0 | 1 | 3 | 2 | 2 | 4 | 2 + 2 > 4? 4 > 4? No | No |
| 0 | 2 | 3 | 2 | 3 | 4 | 2 + 3 > 4? 5 > 4 → Yes | Count++ |
| 1 | 2 | 3 | 2 | 3 | 4 | 2 + 3 > 4? 5 > 4 → Yes | Count++ |

**Total count = 3**

---

## Approach 2: Better Solution (Two Pointers)

### Core Idea

Leverage sorting and two pointers to reduce complexity:

- Fix the largest element in the triplet.
- Use two pointers to find valid pairs that satisfy the triangle inequality with the fixed element.

### Algorithm

1. Sort the array.
2. Loop `k` from `n-1` down to 2 (consider `nums[k]` as the largest side).
3. Initialize two pointers:
    - `left = 0`
    - `right = k - 1`
4. While `left < right`:
    - If `nums[left] + nums[right] > nums[k]`, then all elements between `left` and `right` (inclusive) with `right` form valid triangles. Increment `count` by `(right - left)` and decrement `right`.
    - Else, increment `left`.

### Java Code

```java
public int triangleNumber(int[] nums) {
    Arrays.sort(nums);
    int count = 0;
    int n = nums.length;
    for (int k = n - 1; k >= 2; k--) {
        int left = 0;
        int right = k - 1;
        while (left < right) {
            if (nums[left] + nums[right] > nums[k]) {
                count += right - left;
                right--;
            } else {
                left++;
            }
        }
    }
    return count;
}

```

### Complexity Analysis

- **Time Complexity:** `O(n^2)` — outer loop runs `n` times, and inner two-pointer traversal runs in linear time.
- **Space Complexity:** `O(1)` — only variables used.

### Dry Run

- Input: `[2, 2, 3, 4]`
- Sorted: `[2, 2, 3, 4]`

| `k` | `nums[k]` | `left` | `right` | Condition | Count change | Explanation |
| --- | --- | --- | --- | --- | --- | --- |
| 3 | 4 | 0 | 2 | 2 + 2 > 4? 4 > 4? No | 0 | Increment left to 1 |
| 3 | 4 | 1 | 2 | 2 + 3 > 4? 5 > 4? Yes | count += 1 (right-left=1) | Valid triplet (2,3,4) |
|  |  |  |  |  |  | Decrement right to 1, now left=1, right=1 (loop ends) |

Total count = 1 (initial) + 2 (from previous step if any) = 3

---

## Approach 3: Most Optimal Solution (Two Pointers + Sorting)

This is the approach described in Approach 2, which offers the best time complexity for the problem: **`O(n^2)`**.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | When to Use |
| --- | --- | --- | --- | --- |
| Brute Force | Check all triplets; sort array first | `O(n^3)` | `O(1)` | Small input sizes, initial implementation |
| Better Solution (Two Pointers) | Fix largest element, use two pointers | `O(n^2)` | `O(1)` | Larger inputs, optimized solutions, interviews |
| Optimal Solution | Same as above, most efficient with sorted array | `O(n^2)` | `O(1)` | Final approach for large inputs |

---

## Final Tips for Interviews

- Always sort the array first; it simplifies inequality checks.
- Use the two-pointer method to reduce nested loops.
- Carefully handle the pointers to count multiple valid triplets at once.
- Think about the problem constraints; `O(n^2)` is generally optimal for this problem.

---

Feel free to practice with different inputs to solidify your understanding!
