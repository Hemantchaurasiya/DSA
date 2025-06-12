# Remove Element

---

## Problem Description
Given an array `nums` and a value `val`, remove all instances of `val` in-place and return the **new length** of the array after removal. The relative order of elements can be changed. You must do this **in-place** with **O(1)** extra space.

**Example:**
```plaintext
Input: nums = [3,2,2,3], val = 3
Output: 2, Array after modification: [2, 2, ...]
```

---

## Approach 1: Brute Force (Using Extra Space)

### **Core Idea**
Create a new array or use additional data structure to filter out the elements equal to `val`.

### **Algorithm**
1. Initialize a new list or use an auxiliary array.
2. Traverse the original array:
   - For each element, if it is not equal to `val`, add it to the new array.
3. Copy the filtered elements back into the original array.
4. Return the count of remaining elements.

*Note:* This approach uses extra space and is not optimal for in-place requirements.

### **Java Code**
```java
public int removeElementBruteForce(int[] nums, int val) {
    int[] temp = new int[nums.length];
    int index = 0;
    for (int num : nums) {
        if (num != val) {
            temp[index++] = num;
        }
    }
    // Copy back to original array
    for (int i = 0; i < index; i++) {
        nums[i] = temp[i];
    }
    return index; // New length
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Single traversal.
- **Space Complexity:** `O(n)` — Extra space for temporary array.

### **Dry Run**
Input: `[3, 2, 2, 3]`, `val=3`

| Step | Array traversal | Filtered Elements | Array after copying | `index` | Comments                               |
|-------|---------------------|-------------------|---------------------|---------|----------------------------------------|
| 1     | 3 == 3? yes        | -                 | -                   | 0       | Skip 3, do not add                     |
| 2     | 2 != 3? yes        | 2                 | `[2, 2, 2, 3]`      | 1       | Add 2                                  |
| 3     | 2 != 3? yes        | 2                 | `[2, 2, 2, 3]`      | 2       | Add 2                                  |
| 4     | 3 == 3? yes        | -                 | -                   | 2       | Skip 3                               |

Result: length = 2, array `[2, 2, ...]`

---

## Approach 2: Two Pointers (Optimal In-Place Solution)

### **Core Idea**
Use two pointers:
- `left` (or `i`) to traverse the array.
- `writeIndex` (or `j`) to mark position for the next non-`val` element.

When encountering an element not equal to `val`, copy it to position `writeIndex` and increment `writeIndex`.

### **Algorithm**
1. Initialize `writeIndex = 0`.
2. Loop `i` from 0 to `n-1`:
   - If `nums[i] != val`:
     - Assign `nums[writeIndex] = nums[i]`.
     - Increment `writeIndex`.
3. Return `writeIndex` as the new length.

### **Java Code**
```java
public int removeElement(int[] nums, int val) {
    int writeIndex = 0;
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] != val) {
            nums[writeIndex++] = nums[i];
        }
    }
    return writeIndex; // Length of array after removal
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Single pass through array.
- **Space Complexity:** `O(1)` — In-place modification.

### **Dry Run**
Input: `[3, 2, 2, 3]`, `val=3`

| Step | `i` | `nums[i]` | `nums` after operation | `writeIndex` | Comments                     |
|-------|-------|-------------|-------------------------|--------------|------------------------------|
| Initial | 0 | 3 | `[3, 2, 2, 3]` | 0 | `nums[0]=3` equals `val`, skip |
| 1     | 1 | 2 | `[2, 2, 2, 3]` | 1 | `nums[1]=2` != `val`, copy to `nums[0]` |
| 2     | 2 | 2 | `[2, 2, 2, 3]` | 2 | `nums[2]=2` != `val`, copy to `nums[1]` |
| 3     | 3 | 3 | `[2, 2, 2, 3]` | 2 | `nums[3]=3` equals `val`, skip |

Result: length = `writeIndex = 2`, array `[2, 2, ...]`

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|----------------------|-----------------------|--------|
| Brute Force | Use extra array to filter out `val` | `O(n)` | `O(n)` | Not in-place, extra space needed |
| Two Pointers | In-place, overwrite non-`val` elements | `O(n)` | `O(1)` | Most efficient method |

---

## Final Tips
- Since array is unordered after removal, no need to preserve original order.
- The two pointers method is the most optimal for in-place removal.
- Always consider edge cases like empty arrays, arrays with all elements as `val`, or no elements as `val`.

---

**Happy Coding!**
