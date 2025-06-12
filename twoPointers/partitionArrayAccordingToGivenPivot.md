# Partition Array According to Given Pivot

---

## Problem Description
Given an array `nums` and a pivot value `pivot`, reorder the array such that:
- All elements **less than** `pivot` come **first**,
- Followed by all elements **equal to** `pivot`,
- Followed by all elements **greater than** `pivot`.

This operation should be done **in-place** with **O(1)** extra space.

**Example:**
```plaintext
Input: nums = [9, 12, 5, 10, 14, 3, 10], pivot = 10
Output: [9, 5, 3, 10, 10, 12, 14]
(Note: The order within the partitions does not matter)
```

---

## Approach 1: Brute Force (Counting and Reordering)

### **Core Idea**
Partition the array into three separate lists based on comparison with the pivot, then combine them back into the original array.

### **Algorithm**
1. Create three lists: `less`, `equal`, `greater`.
2. Traverse the array:
   - Append elements to `less` if less than pivot.
   - Append elements to `equal` if equal to pivot.
   - Append elements to `greater` if greater than pivot.
3. Concatenate these lists back into the original array.

*Note:* This approach uses extra space.

### **Java Code**
```java
public void partitionArrayBruteForce(int[] nums, int pivot) {
    List<Integer> less = new ArrayList<>();
    List<Integer> equal = new ArrayList<>();
    List<Integer> greater = new ArrayList<>();
    
    for (int num : nums) {
        if (num < pivot) {
            less.add(num);
        } else if (num == pivot) {
            equal.add(num);
        } else {
            greater.add(num);
        }
    }
    
    int index = 0;
    for (int num : less) {
        nums[index++] = num;
    }
    for (int num : equal) {
        nums[index++] = num;
    }
    for (int num : greater) {
        nums[index++] = num;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** `O(n)` — One pass through the array.
- **Space Complexity:** `O(n)` — Extra lists for partitioning.

### **Dry Run**
Input: `[9, 12, 5, 10, 14, 3, 10]`, pivot = 10

| Step | Elements processed | `less` | `equal` | `greater` | Final array after reassembly | Comments |
|-------|----------------------|---------|---------|------------|------------------------------|----------|
| 1     | 9                    | [9]     | []      | []         | [9, 12, 5, 10, 14, 3, 10]   | First element less than pivot |
| 2     | 12                   | [12]    | []      | [12]     | ... | Greater than pivot |
| 3     | 5                    | [9, 5]  | []      | [12]     | ... | Less than pivot |
| 4     | 10                   | [9, 5]  | [10]    | [12]     | ... | Equal to pivot |
| 5     | 14                   | [9, 5]  | [10]    | [12, 14] | ... | Greater than pivot |
| 6     | 3                    | [9, 5, 3] | [10] | [12, 14] | ... | Less than pivot |
| 7     | 10                   | [9, 5, 3] | [10, 10] | [12, 14] | Final: `[9, 5, 3, 10, 10, 12, 14]` | Reassembled array |

---

## Approach 2: In-Place Partition (Two Pointers, Dutch National Flag variant)

### **Core Idea**
Use three pointers:
- `low` to track the position for elements less than pivot.
- `mid` to traverse the array.
- `high` for elements greater than pivot.

Iterate through the array:
- If `nums[mid] < pivot`, swap `nums[low]` and `nums[mid]`, increment both `low` and `mid`.
- If `nums[mid] == pivot`, just move `mid`.
- If `nums[mid] > pivot`, swap `nums[mid]` and `nums[high]`, decrement `high`.

This approach partitions the array into three sections in a single pass.

### **Algorithm**
1. Initialize `low = 0`, `mid = 0`, `high = nums.length - 1`.
2. While `mid <= high`:
   - If `nums[mid] < pivot`: swap `nums[low]` and `nums[mid]`, `low++`, `mid++`.
   - Else if `nums[mid] == pivot`: `mid++`.
   - Else (`nums[mid] > pivot`): swap `nums[mid]` and `nums[high]`, `high--`.
3. The array will be partitioned into three sections.

### **Java Code**
```java
public void partitionArrayInPlace(int[] nums, int pivot) {
    int low = 0, mid = 0, high = nums.length - 1;
    while (mid <= high) {
        if (nums[mid] < pivot) {
            swap(nums, low, mid);
            low++;
            mid++;
        } else if (nums[mid] == pivot) {
            mid++;
        } else {
            swap(nums, mid, high);
            high--;
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
- **Time Complexity:** `O(n)` — Single pass with three pointers.
- **Space Complexity:** `O(1)` — In-place rearrangement.

### **Dry Run**
Input: `[9, 12, 5, 10, 14, 3, 10]`, pivot = 10

| Step | `low` | `mid` | `high` | `nums` | Action | Comments |
|-------|--------|--------|---------|---------|---------|----------|
| 1     | 0      | 0      | 6       | `[9, 12, 5, 10, 14, 3, 10]` | `nums[mid]=9<10` | Swap `nums[low]` and `nums[mid]` (no change), `low=1`, `mid=1` |
| 2     | 1      | 1      | 6       | `[9, 12, 5, 10, 14, 3, 10]` | `nums[mid]=12>10` | Swap `nums[mid]` and `nums[high]` -> `[9, 10, 5, 10, 14, 3, 12]`, `high=5` |
| 3     | 1      | 1      | 5       | `[9, 10, 5, 10, 14, 3, 12]` | `nums[mid]=10==10` | `mid++` -> 2 |
| 4     | 1      | 2      | 5       | `[9, 10, 5, 10, 14, 3, 12]` | `nums[mid]=5<10` | Swap `nums[low]` and `nums[mid]` -> `[5, 10, 9, 10, 14, 3, 12]`, `low=2`, `mid=3` |
| 5     | 2      | 3      | 5       | `[5, 10, 9, 10, 14, 3, 12]` | `nums[mid]=10==10` | `mid++` -> 4 |
| 6     | 2      | 4      | 5       | `[5, 10, 9, 10, 14, 3, 12]` | `nums[mid]=14>10` | Swap `nums[mid]` and `nums[high]` -> `[5, 10, 9, 10, 3, 14, 12]`, `high=4` |
| 7     | 2      | 4      | 4       | `[5, 10, 9, 10, 3, 14, 12]` | `nums[mid]=3<10` | Swap `nums[low]` and `nums[mid]` -> `[5, 10, 3, 10, 9, 14, 12]`, `low=3`, `mid=5` |

End when `mid=5` > `high=4`.  
Final array: `[5, 10, 3, 10, 9, 14, 12]` (Partitions: `<10`: 5, 3, 9; `==10`: 10, 10; `>10`: 14, 12)

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|----------------------|-----------------------|--------|
| Brute Force | Use extra lists to store partitions | `O(n)` | `O(n)` | Simple but extra space |
| In-Place Partition | Dutch National Flag technique | `O(n)` | `O(1)` | Efficient, in-place |

---

## Final Tips
- For in-place partitioning, the Dutch National Flag algorithm is optimal.
- The order within the partitions doesn't matter, so swapping is acceptable.
- Always handle edge cases such as empty arrays or arrays with all equal elements.

---

**Happy Coding!**
