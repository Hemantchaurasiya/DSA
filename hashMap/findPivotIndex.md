# Find Pivot Index

## Problem Overview
Given an array of integers `nums`, find the "pivot" index where the sum of all elements to the left of the index is equal to the sum of all elements to the right of the index.  
- If no such index exists, return `-1`.  
- If multiple exist, return the leftmost one.

---

## 1. Brute Force Approach

### **Core Idea**
- For each index, calculate the sum of elements to the left and right.
- Check if they are equal.
- Return the first index where the condition holds, or `-1` if none.

### **Algorithm**
1. Loop through each index `i` in the array.
2. For each `i`, compute:
   - Left sum: sum of `nums[0..i-1]`.
   - Right sum: sum of `nums[i+1..end]`.
3. If left sum equals right sum, return `i`.
4. If no such index is found after checking all, return `-1`.

### **Java Code**

```java
public int pivotIndexBruteForce(int[] nums) {
    int n = nums.length;
    for (int i = 0; i < n; i++) {
        int leftSum = 0, rightSum = 0;
        for (int j = 0; j < i; j++) {
            leftSum += nums[j];
        }
        for (int j = i + 1; j < n; j++) {
            rightSum += nums[j];
        }
        if (leftSum == rightSum) {
            return i;
        }
    }
    return -1;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n²)
  - For each index, summing left and right parts takes O(n).
- **Space Complexity:** O(1)
  - Only variables used; no extra space.

### **Dry Run (Example):**
`nums = [1, 7, 3, 6, 5, 6]`

| i | Left sum | Right sum | Condition? |
|---|------------|------------|------------|
| 0 | 0          | 7+3+6+5+6=27 | No  |
| 1 | 1          | 3+6+5+6=20  | No  |
| 2 | 1+7=8      | 6+5+6=17    | No  |
| 3 | 1+7+3=11   | 5+6=11      | Yes → return 3 |

---

## 2. Better Solution: Prefix Sum with Single Pass

### **Core Idea**
- Precompute total sum of array.
- Iterate through array, maintaining a running sum of elements to the left.
- For each index `i`, check if `leftSum == totalSum - leftSum - nums[i]`.
- If yes, return `i`.

### **Algorithm**
1. Calculate total sum of the array.
2. Initialize `leftSum = 0`.
3. Loop through each index `i`:
   - If `leftSum == totalSum - leftSum - nums[i]`, return `i`.
   - Else, add `nums[i]` to `leftSum`.
4. Return `-1` if no index satisfies the condition.

### **Java Code**

```java
public int pivotIndex(int[] nums) {
    int totalSum = 0;
    for (int num : nums) {
        totalSum += num;
    }
    int leftSum = 0;
    for (int i = 0; i < nums.length; i++) {
        if (leftSum == totalSum - leftSum - nums[i]) {
            return i;
        }
        leftSum += nums[i];
    }
    return -1;
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n)
  - Single pass to compute total sum, and one pass to find pivot.
- **Space Complexity:** O(1)
  - Only variables used.

### **Dry Run (Example):**
`nums = [1, 7, 3, 6, 5, 6]`

- totalSum = 1+7+3+6+5+6=28
- i=0: leftSum=0, check: 0 == 28 - 0 - 1=27 → No, leftSum=1
- i=1: leftSum=1, check: 1 == 28 - 1 - 7=20 → No, leftSum=8
- i=2: leftSum=8, check: 8 == 28 - 8 - 3=17 → No, leftSum=11
- i=3: leftSum=11, check: 11 == 28 - 11 - 6=11 → Yes → return 3

---

## 3. Summary

| Approach                     | Time Complexity | Space Complexity | Description                                            |
|------------------------------|-----------------|------------------|--------------------------------------------------------|
| Brute Force                  | O(n²)          | O(1)             | Checks sums for each index individually               |
| Prefix Sum & Single Pass     | O(n)           | O(1)             | Uses total sum and running sum for efficient check   |

---

## **Key Takeaways**
- The optimal solution uses prefix sums and total sum to reduce time complexity.
- Always consider prefix sums or running totals for sum-related array problems.
- The problem asks for the *leftmost* index, so return immediately when condition is met.

---

**Happy coding!**
