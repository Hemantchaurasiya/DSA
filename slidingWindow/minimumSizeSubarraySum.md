# Minimum Size Subarray Sum

This problem involves finding the **smallest subarray length** such that the sum of its elements is **at least** a given target `s`.

---

## 1. Brute Force Approach

### **Approach Name:**  
Brute Force

### **Core Idea:**  
Check all possible subarrays, calculate their sums, and determine the smallest length among those with sum ≥ `s`.

### **Algorithm:**  
1. Iterate through all starting indices `i` from 0 to `n-1`.
2. For each `i`, iterate through all ending indices `j` ≥ `i`.
3. Calculate the sum of the subarray from `i` to `j`.
4. If the sum ≥ `s`, update the minimum length.
5. Continue until all subarrays are checked.

### **Java Code:**
```java
public int minSubArrayLen(int s, int[] nums) {
    int n = nums.length;
    int minLength = Integer.MAX_VALUE;

    for (int start = 0; start < n; start++) {
        int sum = 0;
        for (int end = start; end < n; end++) {
            sum += nums[end];
            if (sum >= s) {
                minLength = Math.min(minLength, end - start + 1);
                break; // No need to extend further once sum >= s
            }
        }
    }
    return (minLength == Integer.MAX_VALUE) ? 0 : minLength;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n^2)**  
  - Nested loops over `start` and `end` indices, each up to `n`.
- **Space Complexity:** **O(1)**

---

## 2. Better Solution (Sliding Window Approach)

### **Approach Name:**  
Sliding Window

### **Core Idea:**  
Use two pointers (`left` and `right`) to maintain a window that sums to at least `s`. Expand `right` to increase the sum, and shrink from `left` to find the minimal window size satisfying the condition.

### **Algorithm:**  
1. Initialize `left = 0`, `sum = 0`, and `minLength = ∞`.
2. Iterate `right` from 0 to `n-1`:
   - Add `nums[right]` to `sum`.
   - While `sum` ≥ `s`:
     - Update `minLength` with the current window size (`right - left + 1`).
     - Subtract `nums[left]` from `sum`.
     - Increment `left` to try to shrink the window.
3. Return `0` if no valid window found; otherwise, return `minLength`.

### **Java Code:**
```java
public int minSubArrayLen(int s, int[] nums) {
    int n = nums.length;
    int left = 0, sum = 0, minLength = Integer.MAX_VALUE;

    for (int right = 0; right < n; right++) {
        sum += nums[right];

        while (sum >= s) {
            minLength = Math.min(minLength, right - left + 1);
            sum -= nums[left];
            left++;
        }
    }
    return (minLength == Integer.MAX_VALUE) ? 0 : minLength;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**  
  - Each element is visited at most twice (once when expanding `right`, once when shrinking `left`).  
- **Space Complexity:** **O(1)**

---

## 3. Most Optimal Solution (Sliding Window with Early Termination)

This is the same as the sliding window solution but emphasizing the efficient shrinking based on the sum condition.

### **Approach Name:**  
Sliding Window with Dynamic Sum Adjustment

### **Core Idea:**  
Expand the window until the sum reaches at least `s`, then shrink from the left to find the minimal valid window, updating the answer accordingly.

### **Algorithm:**  
Same as the better solution, as it already efficiently handles the problem.

### **Java Code:**
```java
public int minSubArrayLen(int s, int[] nums) {
    int left = 0, sum = 0, minLen = Integer.MAX_VALUE;

    for (int right = 0; right < nums.length; right++) {
        sum += nums[right];

        while (sum >= s) {
            minLen = Math.min(minLen, right - left + 1);
            sum -= nums[left];
            left++;
        }
    }
    return minLen == Integer.MAX_VALUE ? 0 : minLen;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**  
- **Space Complexity:** **O(1)**

---

## **Dry Run Example**

### Input:
`nums = [2,3,1,2,4,3]`, `s = 7`

| Step | `right` | `nums[right]` | `sum` | Condition (`sum >= s`) | `left` | Current Window | Min Length Update | Result so far |
|-------|----------|--------------|--------|------------------------|--------|------------------|------------------|--------------|
| 0     | 0        | 2            | 2      | No                     | 0      | [2]              | -                | -            |
| 1     | 1        | 3            | 5      | No                     | 0      | [2, 3]           | -                | -            |
| 2     | 2        | 1            | 6      | No                     | 0      | [2, 3, 1]        | -                | -            |
| 3     | 3        | 2            | 8      | Yes                    | 0      | [2, 3, 1, 2]     | minLength=4     | 4            |
|       |          |              |        |                        | 0      | Remove 2: sum=6  | -                | -            |
|       |          |              |        |                        | 1      | [3, 1, 2]       | -                | -            |
| 4     | 4        | 4            | 10     | Yes                    | 1      | [3, 1, 2, 4]     | minLength=4     | 4            |
|       |          |              |        |                        | 1      | Remove 3: sum=7  | minLength=4     | 4            |
|       |          |              |        |                        | 2      | [1, 2, 4]       | -                | -            |
| 5     | 5        | 3            | 10     | Yes                    | 2      | [1, 2, 4, 3]     | minLength=4     | 4            |
|       |          |              |        |                        | 2      | Remove 1: sum=8  | -                | -            |
|       |          |              |        |                        | 3      | [2, 4, 3]       | -                | -            |

**Answer:** The smallest subarray length with sum ≥ 7 is **3** (subarray `[4,3]` or `[2,4,3]`).

---

## **Summary**

| Approach | Time Complexity | Space Complexity | Key Idea |
|------------|------------------|------------------|------------|
| Brute Force | O(n^2) | O(1) | Check all subarrays, compute sums |
| Sliding Window | O(n) | O(1) | Expand and shrink window to find minimal length with sum ≥ `s` |
| Optimized Sliding Window | O(n) | O(1) | Same as above, but efficient sum updates |

---

## **Final Tips for Interviews:**
- Always check if a sliding window approach can be applied, as it offers linear time complexity.
- Maintain a running sum, and only move pointers when necessary.
- Think about early termination conditions to optimize further.
- Practice with different variations, such as sum constraints or different data types.

---

**Happy Revising!**
