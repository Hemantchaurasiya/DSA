# Remove Duplicates From Sorted Array II

---

## Problem Description
Given a **sorted array** `nums`, remove **duplicates** such that each element appears **at most twice**. Modify the array **in-place** and return the **new length** of the array. The order of elements can be changed, but the problem emphasizes in-place modifications.

**Example:**
```plaintext
Input: [0,0,1,1,1,1,2,3,3]
Output: 7, Array after modification: [0,0,1,1,2,3,3]
```

---

## Approach 1: Brute Force (Counting Occurrences)

### **Core Idea**
Count the frequency of each element, and only include elements that have appeared less than or equal to twice.

### **Algorithm**
1. Initialize a new list to store valid elements.
2. Traverse the sorted array:
   - For each element, count how many times it has appeared so far.
   - If the count is less than or equal to 2, include it in the array.
3. Copy the valid elements back into the original array if needed.
4. Return the new length.

*Note:* This approach is straightforward but not efficient as it involves counting occurrences repeatedly.

### **Java Code**
```java
public int removeDuplicatesBruteForce(int[] nums) {
    int n = nums.length;
    List<Integer> temp = new ArrayList<>();
    int count = 1;
    
    for (int i = 0; i < n; i++) {
        count = 1;
        while (i + 1 < n && nums[i] == nums[i + 1]) {
            count++;
            i++;
        }
        int limit = Math.min(count, 2);
        for (int j = 0; j < limit; j++) {
            temp.add(nums[i]);
        }
    }
    // Copy back to original array
    for (int i = 0; i < temp.size(); i++) {
        nums[i] = temp.get(i);
    }
    return temp.size();
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n^2)` in the worst case (due to counting duplicates repeatedly).
- **Space Complexity:** `O(n)` for auxiliary storage.

### **Dry Run**
Input: `[0,0,1,1,1,1,2,3,3]`

| Step | Current element | Count of consecutive duplicates | Action | Result array | Comments                    |
|-------|------------------|----------------------------------|---------|--------------|------------------------------|
| 1     | 0                | 2                                | Include 0 twice | `[0,0]` | First two zeros included |
| 2     | 1                | 4                                | Include 1 twice | `[0,0,1,1]` | Four ones, include only twice |
| 3     | 2                | 1                                | Include 2 once | `[0,0,1,1,2]` | Single 2 included |
| 4     | 3                | 2                                | Include 3 twice | `[0,0,1,1,2,3,3]` | Two 3s included |

---

## Approach 2: Two Pointers (In-Place, Efficient)

### **Core Idea**
Since the array is sorted, duplicate elements are adjacent. Use two pointers:
- `slow` to track the position of the last valid element.
- `fast` to scan through the array.

Allow each element to appear **at most twice** by:
- When encountering a new element, check if it can be added based on the previous elements.
- Maintain the condition that no element appears more than twice.

### **Algorithm**
1. If array length <= 2, return length directly.
2. Initialize `slow = 2` (since first two elements are always valid).
3. Loop `fast` from 2 to end:
   - If `nums[fast] != nums[slow - 2]`, it means the current element can be added.
   - Assign `nums[slow] = nums[fast]` and increment `slow`.
4. Return `slow` as the new length.

### **Java Code**
```java
public int removeDuplicates(int[] nums) {
    int n = nums.length;
    if (n <= 2) return n;
    int slow = 2;
    for (int fast = 2; fast < n; fast++) {
        if (nums[fast] != nums[slow - 2]) {
            nums[slow] = nums[fast];
            slow++;
        }
    }
    return slow;
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — Single pass through the array.
- **Space Complexity:** `O(1)` — In-place modification.

### **Dry Run**
Input: `[0,0,1,1,1,1,2,3,3]`

| Step | `fast` | `nums[fast]` | `nums[slow-2]` | Action | `nums` after operation | `slow` | Comments |
|-------|----------|--------------|----------------|---------|------------------------|--------|---------|
| 0     | 2 | 1 | 0 | 1 != 0? Yes | `[0,0,1,1,1,1,2,3,3]` | 3 | Include 1 |
| 1     | 3 | 1 | 1 | 1 != 1? No | `[0,0,1,1,1,1,2,3,3]` | 3 | Skip, already two 1s |
| 2     | 4 | 1 | 1 | No | same | 3 | Skip |
| 3     | 5 | 1 | 1 | No | same | 3 | Skip |
| 4     | 6 | 2 | 1 | 2 != 1? Yes | `[0,0,1,1,2,1,2,3,3]` | 4 | Include 2 |
| 5     | 7 | 3 | 1 | 3 != 1? Yes | `[0,0,1,1,2,3,2,3,3]` | 5 | Include 3 |
| 6     | 8 | 3 | 2 | 3 != 2? Yes | `[0,0,1,1,2,3,3,3,3]` | 6 | Include second 3 |

Result length: `6`  
Array after modification: `[0,0,1,1,2,3,3,...]`

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|----------------------|-----------------------|--------|
| Brute Force | Count occurrences for each element | `O(n^2)` | `O(n)` | Not optimal |
| Two Pointers | In-place, allow at most two duplicates | `O(n)` | `O(1)` | Most efficient |

---

## Final Tips
- Since the array is sorted, duplicates are adjacent, simplifying detection.
- Use the two pointers method for optimal in-place solution.
- Always handle edge cases with small arrays (`length <= 2`).

---

**Happy Coding!**
