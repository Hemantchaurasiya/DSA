# Count Number of Nice Subarrays

This problem involves counting the number of subarrays where the number of even numbers is **exactly** a given value `k`. These subarrays are often called "nice" subarrays based on the problem statement.

---

## 1. Problem Restatement
Given an array `nums` of integers, and an integer `k`, count the number of subarrays that contain **exactly** `k` even numbers.

---

## 2. Approach 1: Brute Force (Naive Enumeration)

### **Approach Name:**  
Brute Force Enumeration

### **Core Idea:**  
Check all possible subarrays, count the number of even numbers in each, and increment the count if it equals `k`.

### **Algorithm:**  
1. Initialize a counter `count` to 0.
2. Iterate over all possible starting indices `i`.
3. For each `i`, iterate over all ending indices `j ≥ i`.
4. Count the number of even numbers in the subarray `nums[i..j]`.
5. If the count of even numbers equals `k`, increment the `count`.
6. Return the total `count`.

### **Java Code:**
```java
public int countNiceSubarrays(int[] nums, int k) {
    int result = 0;
    int n = nums.length;

    for (int i = 0; i < n; i++) {
        int evenCount = 0;
        for (int j = i; j < n; j++) {
            if (nums[j] % 2 == 0) {
                evenCount++;
            }
            if (evenCount > k) break; // No need to continue if count exceeds k
            if (evenCount == k) result++;
        }
    }

    return result;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n^2)**, since all subarrays are checked and counting evens takes O(1) per extension.
- **Space Complexity:** **O(1)**

---

## 3. Approach 2: Sliding Window + Counting

### **Approach Name:**  
Two Pointers with Counting

### **Core Idea:**  
Use a sliding window to efficiently count subarrays with **at most** `k` even numbers, then compute the difference to get exactly `k`.

### **Algorithm:**  
1. Define a helper function to count subarrays with **at most** `k` even numbers.
2. Use two pointers (`left` and `right`) to expand and shrink the window.
3. Count the number of subarrays with **at most** `k` evens.
4. Use the formula:  
   **Number of subarrays with exactly `k` evens** =  
   `countAtMost(k) - countAtMost(k - 1)`

### **Steps:**  
- Implement `countAtMost(k)`:
  - Initialize `left = 0` and `result = 0`.
  - Iterate `right` from 0 to `n-1`:
    - If `nums[right]` is even, increment count of evens in window.
    - While the count of evens exceeds `k`, move `left` forward, decreasing count of evens if needed.
    - Add `(right - left + 1)` to result (all subarrays ending at `right` with ≤ `k` evens).
- Compute `result = countAtMost(k) - countAtMost(k - 1)`.

### **Java Code:**
```java
public int countNiceSubarrays(int[] nums, int k) {
    return countAtMost(nums, k) - countAtMost(nums, k - 1);
}

private int countAtMost(int[] nums, int k) {
    int count = 0, result = 0, left = 0;
    int evensCount = 0;

    for (int right = 0; right < nums.length; right++) {
        if (nums[right] % 2 == 0) evensCount++;

        while (evensCount > k) {
            if (nums[left] % 2 == 0) evensCount--;
            left++;
        }

        result += right - left + 1;
    }

    return result;
}
```

### **Complexity Analysis:**  
- **Time Complexity:** **O(n)**, since each element is visited at most twice (once when expanding `right`, once when moving `left`).
- **Space Complexity:** **O(1)**

---

## 4. Dry Run Example

### Input:
```plaintext
nums = [1, 2, 1, 2, 3]
k = 2
```

### Step-by-step:

**Count subarrays with ≤ 2 evens:**

| `right` | `nums[right]` | Evens Count | Window `[left..right]` | Subarrays ending at `right` | Total count so far |
|-----------|--------------|--------------|-------------------------|------------------------------|---------------------|
| 0         | 1            | 0            | [1]                     | 1                            | 1                   |
| 1         | 2            | 1            | [1, 2]                  | 2                            | 3                   |
| 2         | 1            | 1            | [1, 2, 1]               | 3                            | 6                   |
| 3         | 2            | 2            | [1, 2, 1, 2]            | 4                            | 10                  |
| 4         | 3            | 2            | [1, 2, 1, 2, 3]          | 5                            | 15                  |

**Count subarrays with ≤ 1 even:**

| `right` | `nums[right]` | Evens Count | Window `[left..right]` | Subarrays ending at `right` | Total count so far |
|-----------|--------------|--------------|-------------------------|------------------------------|---------------------|
| 0         | 1            | 0            | [1]                     | 1                            | 1                   |
| 1         | 2            | 1            | [1, 2]                  | 2                            | 3                   |
| 2         | 1            | 1            | [1, 2, 1]               | 3                            | 6                   |
| 3         | 2            | 2            | Shift left until only 1 even | left=2, window=[1,2] | 2 (subarrays: [2], [2,3]) |
| 4         | 3            | 1            | [1, 2, 3]               | 3                            | 9                  |

**Number of subarrays with exactly 2 evens:**

= `(countAtMost(2)) - (countAtMost(1))`  
= `15 - 9` = **6**

---

## 5. Summary

| Approach | Time Complexity | Space Complexity | Key Idea |
|--------------|------------------|------------------|------------|
| Naive Enumeration | O(n^2) | O(1) | Check all subarrays, count evens |
| Sliding Window with AtMost | O(n) | O(1) | Count subarrays with ≤ `k` evens and subtract |

---

## **Final Tips for Interviews:**
- Recognize this as a problem of counting subarrays with **exactly** `k` occurrences.
- Use the "at most" method for efficient counting.
- Sliding window techniques significantly optimize brute-force solutions.
- Carefully handle edge cases, e.g., when `k=0` or array contains all odds.

---

**Happy Revising!**
