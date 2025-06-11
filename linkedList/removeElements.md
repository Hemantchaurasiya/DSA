# 203 - Remove Elements

---

## Problem Statement

Given an array `nums` and an integer `val`, remove all instances of `val` in-place and return the new length of the array. The relative order of elements may be changed, and you must do this **in-place** with **O(1)** extra space.

---

## Approach 1: Brute Force (Using Extra Space)

### Core Idea

Use an auxiliary array or list to collect elements that are **not** equal to `val`, then copy them back into the original array.

### Algorithm

1. Initialize an empty list or temporary array.
2. Iterate through the array:
    - If `nums[i] != val`, add `nums[i]` to the temporary list.
3. Copy the filtered elements from the temporary list back into the original array.
4. Return the size of the filtered list.

### Java Code

```java
public int removeElementBruteForce(int[] nums, int val) {
    List<Integer> temp = new ArrayList<>();
    for (int num : nums) {
        if (num != val) {
            temp.add(num);
        }
    }
    for (int i = 0; i < temp.size(); i++) {
        nums[i] = temp.get(i);
    }
    return temp.size();
}

```

### Complexity Analysis

- **Time Complexity:** `O(n)` — Iterates through the array once.
- **Space Complexity:** `O(n)` — Uses extra space proportional to the input size for the temporary list.

### Dry Run

**Input:** `nums = [3,2,2,3], val = 3`

| Step | `nums` | `temp` | Action |
| --- | --- | --- | --- |
| 1 | [3,2,2,3] | [] | Initialize temp |
| 2 | [3,2,2,3] | [] | Check 3: skip (since 3 == val) |
| 3 | [3,2,2,3] | [2] | Check 2: add to temp (not equal to val) |
| 4 | [3,2,2,3] | [2, 2] | Check 2: add to temp |
| 5 | [3,2,2,3] | [2, 2] | Check 3: skip |
| 6 | Copy temp to `nums` | [2, 2, _, _] | Overwrite first two elements in `nums` |
| **Output:** 2, **Modified `nums`:** `[2, 2, 2, 3]` (only first 2 are relevant) |  |  |  |

---

## Approach 2: Two-Pointer Method (Optimized In-Place Solution)

### Core Idea

Use two pointers:

- **Slow pointer (`i`)**: tracks position to place the next non-`val` element.
- **Fast pointer (`j`)**: scans through the array.

Whenever `nums[j] != val`, assign `nums[i] = nums[j]` and increment `i`. This overwrites elements equal to `val` with subsequent non-`val` elements.

### Algorithm

1. Initialize `i = 0`.
2. Iterate `j` from 0 to `n-1`:
    - If `nums[j] != val`:
        - Assign `nums[i] = nums[j]`
        - Increment `i`.
3. After iteration, `i` represents the new length of the array with all `val` removed.

### Java Code

```java
public int removeElement(int[] nums, int val) {
    int i = 0; // Slow pointer
    for (int j = 0; j < nums.length; j++) {
        if (nums[j] != val) {
            nums[i] = nums[j];
            i++;
        }
    }
    return i; // Length of array after removal
}

```

### Complexity Analysis

- **Time Complexity:** `O(n)` — Single pass through the array.
- **Space Complexity:** `O(1)` — In-place, no extra data structures used.

### Dry Run

**Input:** `nums = [3,2,2,3], val = 3`

| Step | `nums` | `i` | `j` | Action |
| --- | --- | --- | --- | --- |
| 1 | [3,2,2,3] | 0 | 0 | `nums[j] = 3` → skip (since equals val) |
| 2 | [3,2,2,3] | 0 | 1 | `nums[j] = 2` ≠ val → `nums[i] = 2`, i=1 |
| 3 | [2,2,2,3] | 1 | 2 | `nums[j] = 2` ≠ val → `nums[i] = 2`, i=2 |
| 4 | [2,2,2,3] | 2 | 3 | `nums[j] = 3` → skip (since equals val) |
| **Result:** i=2, first 2 elements `[2, 2]` are valid. Remaining elements can be ignored. |  |  |  |  |

---

## Summary Table

| Method | Approach | In-place | Extra Space | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- | --- |
| Brute Force | Use temporary array for filtering | No | Yes (`O(n)`) | `O(n)` | `O(n)` |
| Two-Pointer (Optimized) | Use two pointers to overwrite elements | Yes | No (`O(1)`) | `O(n)` | `O(1)` |

---

## Final Notes

- The most optimal solution is the **Two-Pointer Approach**.
- Always prefer in-place algorithms with `O(1)` space complexity for such problems.
- This method maintains **O(n)** time complexity with minimal space overhead, making it suitable for large input sizes.

---

**End of Revision Note**
