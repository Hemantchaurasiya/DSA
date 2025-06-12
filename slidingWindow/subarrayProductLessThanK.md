# Subarray Product Less Than K

This problem involves counting the number of contiguous subarrays where the product of all elements is **less than** a given value `k`. It is a classic sliding window problem with multiplicative constraints.

---

## 1. Problem Restatement
Given an array `nums` of positive integers and an integer `k`, find the number of **subarrays** whose product of elements is **less than** `k`.

---

## 2. Approach 1: Brute Force Enumeration

### **Approach Name:**  
Naive Enumeration of All Subarrays

### **Core Idea:**  
Enumerate all possible subarrays, compute their product, and count those with product `< k`.

### **Algorithm:**  
1. Initialize `result` to 0.
2. Loop over all starting indices `i` from 0 to `n-1`.
3. For each `i`, initialize `product = 1`.
4. Loop over `j` from `i` to `n-1`:
   - Multiply `product` by `nums[j]`.
   - If `product >= k`, break the inner loop.
   - Else, increment `result`.
5. Return `result`.

### **Java Code:**
```java
public int numSubarrayProductLessThanK(int[] nums, int k) {
    int result = 0;
    int n = nums.length;

    for (int i = 0; i < n; i++) {
        long product = 1;
        for (int j = i; j < n; j++) {
            product *= nums[j];
            if (product >= k) {
                break;
            }
            result++;
        }
    }

    return result;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n^2)**, as all subarrays are checked and product calculation per subarray.
- **Space Complexity:** **O(1)**

---

## 3. Approach 2: Sliding Window (Optimal)

### **Approach Name:**  
Two Pointers / Sliding Window

### **Core Idea:**  
Use a sliding window to maintain a product of elements `< k`. Expand the right pointer, multiply the current element, and if the product exceeds or equals `k`, shrink from the left until the product is `< k`.  
Number of valid subarrays ending at `right` is `(right - left + 1)`.

### **Algorithm:**  
1. Initialize `left = 0`, `product = 1`, and `result = 0`.
2. Iterate `right` from 0 to `n-1`:
   - Multiply `product` by `nums[right]`.
   - While `product >= k` and `left <= right`:
     - Divide `product` by `nums[left]`.
     - Increment `left`.
   - Add `(right - left + 1)` to `result` (all subarrays ending at `right` with product `< k`).
3. Return `result`.

### **Edge Cases:**  
- When `k <= 1`, the result is zero because product of positive integers can't be less than 1 unless subarray is empty or invalid.

### **Java Code:**
```java
public int numSubarrayProductLessThanK(int[] nums, int k) {
    if (k <= 1) return 0;
    int left = 0, result = 0;
    long product = 1;

    for (int right = 0; right < nums.length; right++) {
        product *= nums[right];

        while (product >= k && left <= right) {
            product /= nums[left];
            left++;
        }

        result += right - left + 1;
    }

    return result;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**, each element is visited at most twice (once when expanding `right`, once when moving `left`).
- **Space Complexity:** **O(1)**

---

## 4. Dry Run with Sample Input

**Input:**

```plaintext
nums = [10, 5, 2, 6]
k = 100
```

### Step-by-step:

| `right` | `nums[right]` | `product` | `left` | Valid subarrays ending at `right` | `result` |
|----------|--------------|-----------|--------|----------------------------------|----------|
| 0        | 10           | 10        | 0      | (10)                             | 1        |
| 1        | 5            | 50        | 0      | (10,5), (5)                      | 3        |
| 2        | 2            | 100       | 0      | (10,5,2) - product == 100, not < 100, shrink| left=1, product=10, result=3+ (2-1+1)=4 |
| 3        | 6            | 60        | 1      | (5,2,6) - product=60, valid, result=4+ (3-1+1)=5 |

**Final answer:** 8

---

## 5. Summary

| Approach | Time Complexity | Space Complexity | Key Idea |
|--------------|------------------|------------------|------------|
| Naive Enumeration | O(n^2) | O(1) | Check all subarrays, compute product each time |
| Sliding Window | O(n) | O(1) | Maintain a window with product `<k` and count subarrays dynamically |

---

## **Final Tips for Interviews:**
- Recognize the sliding window pattern for problems involving subarrays with constraints on product or sum.
- Be cautious with edge cases, especially when `k <= 1`.
- Use floating or long data types to avoid overflow with large products.
- Practice similar problems to build intuition for multiplicative constraints.

---

**Happy Revising!**
