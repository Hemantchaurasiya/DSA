# Rearrange Array Elements by Sign

---

## Problem Description
Given an array `nums` of integers, rearrange the array so that:
- All **negative** numbers come first,
- Followed by all **positive** numbers,
- Zeroes can be placed anywhere after negatives and before positives (or at the end).

The rearrangement should be **in-place**, ideally with minimal extra space.

---

## Approach 1: Brute Force with Extra Space

### **Core Idea**
Create separate lists for negatives, zeros, and positives, then combine them back into the original array.

### **Algorithm**
1. Initialize three lists: `negatives`, `zeros`, `positives`.
2. Traverse the array:
   - If the element is negative, add to `negatives`.
   - If zero, add to `zeros`.
   - If positive, add to `positives`.
3. Overwrite the original array with elements from `negatives`, then `zeros`, then `positives`.

### **Java Code**
```java
public void rearrangeBySignBruteForce(int[] nums) {
    List<Integer> negatives = new ArrayList<>();
    List<Integer> zeros = new ArrayList<>();
    List<Integer> positives = new ArrayList<>();
    
    for (int num : nums) {
        if (num < 0) {
            negatives.add(num);
        } else if (num == 0) {
            zeros.add(num);
        } else {
            positives.add(num);
        }
    }
    
    int index = 0;
    for (int num : negatives) {
        nums[index++] = num;
    }
    for (int num : zeros) {
        nums[index++] = num;
    }
    for (int num : positives) {
        nums[index++] = num;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Single traversal.
- **Space Complexity:** `O(n)` — Extra lists used for temporary storage.

### **Dry Run**
Input: `[0, -1, 3, -2, 0, 4]`

| Step | Traversal | `negatives` | `zeros` | `positives` | Final array after copying | Comments |
|-------|--------------|--------------|---------|--------------|----------------------------|----------|
| 1     | 0 (zero)     | []           | [0]     | []           | `[0, -1, 3, -2, 0, 4]`     | Added 0 to zeros |
| 2     | -1 (neg)     | [-1]         | [0]     | []           | ... | Negative |
| 3     | 3 (pos)      | [-1]         | [0]     | [3]          | ... | Positive |
| 4     | -2 (neg)     | [-1, -2]     | [0]     | [3]          | ... | Negative |
| 5     | 0 (zero)     | [-1, -2]     | [0, 0]  | [3]          | ... | Zero |
| 6     | 4 (positive) | [-1, -2]     | [0, 0]  | [3, 4]       | Final: `[-1, -2, 0, 0, 3, 4]` | Rearranged |

---

## Approach 2: In-Place Two-Pointer Partition

### **Core Idea**
Use two pointers:
- `left` starting from the beginning,
- `right` starting from the end.

Traverse:
- If `nums[left]` is negative, move `left` forward.
- If `nums[right]` is positive, move `right` backward.
- If `nums[left]` is positive and `nums[right]` is negative, swap and move both pointers.

Repeat until `left >= right`.

This method ensures negatives are moved to the front and positives to the end in-place.

### **Algorithm**
1. Initialize `left = 0`, `right = nums.length - 1`.
2. While `left < right`:
   - If `nums[left] < 0`, `left++`.
   - Else if `nums[right] >= 0`, `right--`.
   - Else swap `nums[left]` and `nums[right]`, `left++`, `right--`.

### **Java Code**
```java
public void rearrangeBySignTwoPointer(int[] nums) {
    int left = 0, right = nums.length - 1;
    while (left < right) {
        if (nums[left] < 0) {
            left++;
        } else if (nums[right] >= 0) {
            right--;
        } else {
            swap(nums, left, right);
            left++;
            right--;
        }
    }
}

private void swap(int[] arr, int i, int j) {
    int temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Single traversal with two pointers.
- **Space Complexity:** `O(1)` — In-place operation.

### **Dry Run**
Input: `[0, -1, 3, -2, 0, 4]`

| Step | `left` | `right` | `nums` | Action | Comments |
|-------|---------|----------|---------|---------|----------|
| 1     | 0       | 5        | `[0, -1, 3, -2, 0, 4]` | `nums[left]=0` (not negative), `nums[right]=4` (positive) | `right--` -> 4 |
| 2     | 0       | 4        | `[0, -1, 3, -2, 0, 4]` | Same check | `right--` -> 3 |
| 3     | 0       | 3        | `[0, -1, 3, -2, 0, 4]` | `nums[left]=0` (not negative), `nums[right]=-2` (negative) | Swap -> `[-2, -1, 3, 0, 0, 4]`, `left=1`, `right=2` |
| 4     | 1       | 2        | `[-2, -1, 3, 0, 0, 4]` | `nums[left]=-1` (negative), `left++` -> 2 | |
| 5     | 2       | 2        | `[-2, -1, 3, 0, 0, 4]` | Loop ends | |

Final array: `[-2, -1, 3, 0, 0, 4]` (negatives at front, positives at end).

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|----------------------|-----------------------|--------|
| Brute Force | Use extra lists to segregate negatives, zeros, positives | `O(n)` | `O(n)` | Simple but extra space |
| Two-Pointer | In-place partitioning with two pointers | `O(n)` | `O(1)` | Efficient, uses no extra space |

---

## Final Tips
- The two-pointer in-place approach is preferable for space efficiency.
- The order within negatives, zeros, or positives can be arbitrary.
- Handle edge cases like empty arrays or arrays with all negatives or all positives.

---

**Happy Coding!**
