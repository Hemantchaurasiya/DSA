# Two Sum II - Input array is sorted

This problem asks us to find two numbers in a **sorted array** that add up to a specific **target**. The array is **1-indexed**, meaning the returned indices should be 1-based.

---

## 1. Brute Force

### **Core Idea:**
Check every possible pair to find the two numbers that add up to the target.

### **Algorithm:**
- Iterate through each element in the array with index `i`.
- For each element, iterate through the subsequent elements with index `j`.
- Check if `nums[i] + nums[j] == target`.
- If yes, return `[i + 1, j + 1]` (since indices are 1-based).

### **Java Code:**
```java
public int[] twoSumBruteForce(int[] nums, int target) {
    int n = nums.length;
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (nums[i] + nums[j] == target) {
                return new int[] {i + 1, j + 1};
            }
        }
    }
    return new int[] {-1, -1}; // if no solution found
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n^2)` because of nested loops.
- **Space Complexity:** `O(1)` as no extra space is used apart from variables.

### **Dry Run:**
- Input: `nums = [2, 7, 11, 15]`, `target = 9`
- Iterations:
  - i=0, j=1: 2 + 7 = 9 → Found! Return `[1, 2]`.

---

## 2. Better Solution (Binary Search)

### **Core Idea:**
Since the array is sorted, for each element `nums[i]`, we can perform a binary search for `target - nums[i]` in the remaining part of the array.

### **Algorithm:**
- Loop through each element `nums[i]`.
- For each, calculate `complement = target - nums[i]`.
- Use binary search to find `complement` in `nums[i+1 ... n-1]`.
- If found, return `[i + 1, index_found + 1]`.

### **Java Code:**
```java
public int[] twoSumBinarySearch(int[] nums, int target) {
    int n = nums.length;
    for (int i = 0; i < n - 1; i++) {
        int complement = target - nums[i];
        int index = binarySearch(nums, complement, i + 1, n - 1);
        if (index != -1) {
            return new int[] {i + 1, index + 1};
        }
    }
    return new int[] {-1, -1};
}

private int binarySearch(int[] nums, int target, int low, int high) {
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

### **Complexity Analysis:**
- **Time Complexity:** `O(n log n)` because for each of the `n` elements, we perform a binary search (`O(log n)`).
- **Space Complexity:** `O(1)`, no extra space apart from variables.

### **Dry Run:**
- Input: `nums = [2, 7, 11, 15]`, `target = 9`
- i=0, `nums[0]=2`, complement=7
- Binary search for 7 in `[7, 11, 15]`:
  - Found at index=1 → Return `[1, 2]`.

---

## 3. Optimal Solution (Two Pointers)

### **Core Idea:**
Use two pointers starting at both ends of the array:
- If the sum is less than target, move the left pointer forward.
- If the sum is greater than target, move the right pointer backward.
- When sum equals target, return the indices.

This approach leverages the sorted nature of the array to find the solution in a single pass.

### **Algorithm:**
- Initialize two pointers: `left = 0`, `right = n - 1`.
- While `left < right`:
  - Calculate `sum = nums[left] + nums[right]`.
  - If `sum == target`, return `[left + 1, right + 1]`.
  - Else if `sum < target`, move `left` forward.
  - Else, move `right` backward.

### **Java Code:**
```java
public int[] twoSumTwoPointers(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left < right) {
        int sum = nums[left] + nums[right];
        if (sum == target) {
            return new int[] {left + 1, right + 1};
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
    return new int[] {-1, -1};
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n)` because each element is visited at most once by either pointer.
- **Space Complexity:** `O(1)` as no extra space is used.

### **Dry Run:**
- Input: `nums = [2, 7, 11, 15]`, `target=9`
- Initial: left=0, right=3
  - sum=2+15=17 >9 → right=2
- Now: left=0, right=2
  - sum=2+11=13 >9 → right=1
- Now: left=0, right=1
  - sum=2+7=9 → Found! Return `[1, 2]`

---

# Summary

| Approach | Core Idea | Implementation | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- |
| **Brute Force** | Check all pairs | Nested loops | `O(n^2)` | `O(1)` |
| **Binary Search** | For each element, binary search for complement | Loop + binary search | `O(n log n)` | `O(1)` |
| **Two Pointers** | Use two pointers from both ends | Two pointers approach | `O(n)` | `O(1)` |

---

This structured revision covers multiple approaches, their intuition, implementation, and analysis, preparing you thoroughly for interviews and quick reference.
