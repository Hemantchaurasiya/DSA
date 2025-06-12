# Sort Array by Parity

---

## Problem Description
Given an integer array `nums`, reorder the array so that all **even** elements come before **odd** elements. The order within the even or odd partitions does not matter. The operation should be **in-place** with **O(1)** extra space.

**Example:**
```plaintext
Input: [3, 1, 2, 4]
Output: [2, 4, 3, 1] or [4, 2, 1, 3]
```

---

## Approach 1: Brute Force with Extra Space

### **Core Idea**
Create two separate lists for even and odd numbers, then combine them back into the original array.

### **Algorithm**
1. Initialize two lists: `evenList` and `oddList`.
2. Traverse `nums`:
   - If the number is even, add it to `evenList`.
   - If odd, add it to `oddList`.
3. Copy elements from `evenList` followed by `oddList` back into `nums`.
4. The array is now partitioned with all even numbers first, followed by odd numbers.

### **Java Code**
```java
public void sortArrayByParityBruteForce(int[] nums) {
    List<Integer> evens = new ArrayList<>();
    List<Integer> odds = new ArrayList<>();
    
    for (int num : nums) {
        if (num % 2 == 0) {
            evens.add(num);
        } else {
            odds.add(num);
        }
    }
    
    int index = 0;
    for (int num : evens) {
        nums[index++] = num;
    }
    for (int num : odds) {
        nums[index++] = num;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Single pass through the array.
- **Space Complexity:** `O(n)` — Extra space for the two lists.

### **Dry Run**
Input: `[3, 1, 2, 4]`

| Step | Traversal | `evens` | `odds` | `nums` after copying | Comments |
|-------|--------------|---------|--------|------------------------|----------|
| 1     | 3 (odd)      | []      | [3]    | `[3, 1, 2, 4]` | Added 3 to odds |
| 2     | 1 (odd)      | []      | [3, 1] | `[3, 1, 2, 4]` | Added 1 to odds |
| 3     | 2 (even)     | [2]     | [3, 1] | `[2, 1, 2, 4]` | Added 2 to evens |
| 4     | 4 (even)     | [2, 4]  | [3, 1] | `[2, 4, 3, 1]` | Added 4 to evens |
| Final | --           | `[2, 4]` | `[3, 1]` | `[2, 4, 3, 1]` | Partitioned array |

---

## Approach 2: Two-Pointer In-Place Solution

### **Core Idea**
Use two pointers:
- `left` starting from the beginning.
- `right` starting from the end.

Traverse the array:
- If `nums[left]` is even, move `left` forward.
- If `nums[right]` is odd, move `right` backward.
- If `nums[left]` is odd and `nums[right]` is even, swap them, then move both pointers inward.

Repeat until `left >= right`.

### **Algorithm**
1. Initialize `left = 0`, `right = nums.length - 1`.
2. While `left < right`:
   - If `nums[left]` is even, increment `left`.
   - Else if `nums[right]` is odd, decrement `right`.
   - Else, swap `nums[left]` and `nums[right]`, increment `left`, decrement `right`.
3. The array will be partitioned with all even elements before odd.

### **Java Code**
```java
public void sortArrayByParityTwoPointer(int[] nums) {
    int left = 0, right = nums.length - 1;
    while (left < right) {
        if (nums[left] % 2 == 0) {
            left++;
        } else if (nums[right] % 2 == 1) {
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
Input: `[3, 1, 2, 4]`

| Step | `left` | `right` | `nums` | Action | Comments |
|-------|---------|----------|---------|---------|----------|
| 1     | 0       | 3        | `[3, 1, 2, 4]` | `nums[left]=3` odd, `nums[right]=4` even | Swap `nums[left]` and `nums[right]` -> `[4, 1, 2, 3]`, `left=1`, `right=2` |
| 2     | 1       | 2        | `[4, 1, 2, 3]` | `nums[left]=1` odd, `nums[right]=2` even | Swap -> `[4, 2, 1, 3]`, `left=2`, `right=1` | Loop ends as `left >= right` |

Final array: `[4, 2, 1, 3]`, with all evens before odds.

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|----------------------|-----------------------|--------|
| Brute Force | Use extra lists to separate evens and odds | `O(n)` | `O(n)` | Simple, but uses extra space |
| Two-Pointer | In-place partitioning with two pointers | `O(n)` | `O(1)` | Efficient, no extra space |

---

## Final Tips
- The in-place two-pointer method is generally preferred for its optimal space complexity.
- The order within the partitions does not matter, so swapping is acceptable.
- Handle edge cases like empty arrays or arrays with all even/odd elements.

---

**Happy Coding!**
