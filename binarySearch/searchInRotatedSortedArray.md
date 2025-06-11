# Search in Rotated Sorted Array

This problem asks us to find a target element in a rotated sorted array efficiently. The array was initially sorted in ascending order, but then rotated at some unknown pivot. Our goal is to locate the target element's index, or -1 if it doesn't exist.

---

## 1. Brute Force Approach

### **Core Idea**

Scan through the entire array linearly and check if each element matches the target.

### **Algorithm**

- Iterate over each element in the array.
- If the element equals the target, return its index.
- If no match is found after traversing the entire array, return -1.

### **Java Code**

```java
public int search(int[] nums, int target) {
    for (int i = 0; i < nums.length; i++) {
        if (nums[i] == target) {
            return i;
        }
    }
    return -1;
}

```

### **Complexity Analysis**

- **Time Complexity:O(n)**, since in the worst case, every element is checked.
- **Space Complexity:O(1)**, no extra space is used besides variables.

### **Dry Run**

- **Input:** `nums = [4,5,6,7,0,1,2], target = 0`
- **Process:**
    - Check index 0: 4 ≠ 0
    - Check index 1: 5 ≠ 0
    - Check index 2: 6 ≠ 0
    - Check index 3: 7 ≠ 0
    - Check index 4: 0 == 0 → Return 4
- **Output:** `4`

---

## 2. Better Solution: Linear Search with Rotation Awareness

### **Core Idea**

Identify the pivot point where the sorted array was rotated, then perform binary search in the appropriate subarray.

> Note: While this improves the conceptual understanding, it still involves linear search to find the pivot, making total complexity O(n).
> 

### **Algorithm**

- Find the pivot index (smallest element) by scanning the array.
- Determine which subarray (left or right of pivot) the target might reside in.
- Perform binary search in that subarray.

### **Java Code**

```java
public int search(int[] nums, int target) {
    int pivot = findPivot(nums);
    if (pivot == -1) {
        // Array is not rotated
        return binarySearch(nums, 0, nums.length - 1, target);
    }
    // Search in the left part
    int index = binarySearch(nums, 0, pivot - 1, target);
    if (index != -1) return index;
    // Search in the right part
    return binarySearch(nums, pivot, nums.length - 1, target);
}

private int findPivot(int[] nums) {
    for (int i = 0; i < nums.length - 1; i++) {
        if (nums[i] > nums[i + 1]) {
            return i + 1;
        }
    }
    return -1; // Not rotated
}

private int binarySearch(int[] nums, int low, int high, int target) {
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return -1;
}

```

### **Complexity Analysis**

- **Time Complexity:O(n)** in worst case (for pivot search), since `findPivot` is linear.
- **Space Complexity:O(1)**, no extra space.

### **Dry Run**

- **Input:** `nums = [4,5,6,7,0,1,2], target=0`
- **Pivot Search:**
    - Check pairs:
        - 4 > 5? No.
        - 5 > 6? No.
        - 6 > 7? No.
        - 7 > 0? Yes → pivot = 4.
- **Binary Search in right subarray `[0,1,2]`:**
    - mid = 4 + (6 - 4)/2 = 5 → nums[5]=1 ≠ 0, 1 > 0 → high=4
    - mid = 4 + (4 - 4)/2=4 → nums[4]=0 == target → return 4.

---

## 3. Most Optimal Solution: Modified Binary Search (O(log n))

### **Core Idea**

Use a modified binary search to efficiently locate the target without explicitly finding the pivot.

At each step:

- Check if the middle element is the target.
- Determine which side (left or right) is sorted.
- Decide whether to move left or right based on the target's value and the sorted half.

### **Intuition**

- The array is rotated, but one half is always sorted.
- We compare the target with the sorted half's bounds to decide which half to continue searching.

### **Algorithm**

1. Initialize `low = 0`, `high = nums.length - 1`.
2. While `low <= high`:
    - Compute `mid = low + (high - low) / 2`.
    - If `nums[mid] == target`, return `mid`.
    - Determine if left half is sorted:
        - If `nums[low] <= nums[mid]`, left half is sorted.
            - Check if target lies within `nums[low]` and `nums[mid]`.
                - If yes, move `high = mid - 1`.
                - Else, move `low = mid + 1`.
        - Else, right half is sorted:
            - Check if target lies within `nums[mid]` and `nums[high]`.
                - If yes, move `low = mid + 1`.
                - Else, move `high = mid - 1`.
3. If target not found, return -1.

### **Java Code**

```java
public int search(int[] nums, int target) {
    int low = 0, high = nums.length - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) return mid;

        // Left half is sorted
        if (nums[low] <= nums[mid]) {
            if (nums[low] <= target && target < nums[mid]) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        // Right half is sorted
        else {
            if (nums[mid] < target && target <= nums[high]) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
    }
    return -1;
}

```

### **Complexity Analysis**

- **Time Complexity:O(log n)**, because the search space halves with each iteration.
- **Space Complexity:O(1)**, no extra space used besides variables.

### **Dry Run**

- **Input:** `nums = [4,5,6,7,0,1,2], target=0`
- **Initial:** low=0, high=6
- **Step 1:** mid=3, nums[mid]=7
    - Check if `nums[low] <= nums[mid]` → 4<=7? Yes → left half sorted.
    - Target=0 is less than 4, so target not in left half.
    - Move `low=mid+1=4`.
- **Step 2:** low=4, high=6, mid=5
    - nums[mid]=1
    - Left half: nums[4]=0, nums[5]=1
    - Is left half sorted? 0<=1? Yes.
    - Is target in [0,1]? target=0, yes.
    - Since target=0 >= nums[4]=0 and < nums[5]=1, move `high=mid-1=4`.
- **Step 3:** low=4, high=4, mid=4
    - nums[mid]=0 == target → return 4.

---

# Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Key Points |
| --- | --- | --- | --- | --- |
| Brute Force | Linear scan | **O(n)** | **O(1)** | Simple but inefficient for large arrays |
| Find Pivot + Binary Search | Locate rotation point, then binary search | **O(n)** (pivot search) | **O(1)** | Slightly better, but still linear pivot search |
| Modified Binary Search | Use properties of sorted halves | **O(log n)** | **O(1)** | Most efficient; optimal solution |

---

# Final Note

For interview purposes, **Approach 3 (Modified Binary Search)** is the preferred method due to its optimal time complexity and elegant use of binary search logic tailored for rotated sorted arrays.

---

If you need further clarification or additional examples, feel free to ask!
