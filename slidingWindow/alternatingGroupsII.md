# Alternating Groups II

---

## Problem Overview
Given an array of integers `A`, the task is to partition the array into groups such that:
- Each group contains **at most 3 elements**.
- The sum of the **maximum** and **minimum** elements in each group is **less than or equal to** a given value `k`.

The goal is to **maximize** the number of such groups formed.

---

## Approach 1: Brute Force (Greedy + Sorting)

### **Core Idea**
Sort the array to process elements in order, then greedily form groups of 1, 2, or 3 elements starting from the smallest element, ensuring the sum of min and max in each group doesn't exceed `k`.

### **Algorithm**
1. Sort the array in ascending order.
2. Initialize a counter for groups formed (`count`).
3. Use a pointer `i` starting from 0.
4. While `i` is within array bounds:
   - Form the smallest possible group:
     - Try forming a group of 3 if possible:
       - Check if `A[i] + A[i+2] <= k`.
       - If yes, form a group of 3 and move `i` by 3.
     - Else try forming a group of 2:
       - Check if `A[i] + A[i+1] <= k`.
       - If yes, form a group of 2 and move `i` by 2.
     - Else form a single-element group:
       - Move `i` by 1.
   - Increment `count` each time a group is formed.
5. Return the total `count`.

### **Java Code**
```java
import java.util.Arrays;

public int maxGroups(int[] A, int k) {
    Arrays.sort(A);
    int i = 0, n = A.length, count = 0;
    while (i < n) {
        if (i + 2 < n && A[i] + A[i + 2] <= k) {
            // Form group of 3
            i += 3;
        } else if (i + 1 < n && A[i] + A[i + 1] <= k) {
            // Form group of 2
            i += 2;
        } else {
            // Form single group
            i += 1;
        }
        count++;
    }
    return count;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n log n)** due to sorting, plus **O(n)** for greedy grouping.
- **Space Complexity:** **O(1)** or **O(n)** depending on the sorting implementation.

### **Dry Run**
- Input: `A = [3, 1, 4, 2, 5], k = 6`

| Sorted Array | Step | Group Formed | Elements Used | Remaining Array | Count |
|----------------|-------|--------------|-----------------|-----------------|--------|
| [1, 2, 3, 4, 5] | Start | - | - | [1,2,3,4,5] | 0 |
| i=0 | Check 3 elements | Yes, 1+3=4 ≤6 | Group of [1,2,3], i=3 | 4,5 | 1 |
| i=3 | Remaining [4,5] | Try 2 elements | 4+5=9 >6 | Single element [4], i=4 | 2 |
| i=4 | Remaining [5] | Single group | i=5 (end) | 1 | 3 |

Maximum groups formed = **3**.

---

## Approach 2: Two-Pointer Greedy (Optimized)

### **Core Idea**
Sort the array, then use two pointers:
- One (`left`) starting at the beginning.
- One (`right`) starting at the end.
Form groups of 1, 2, or 3 elements from the smallest and largest elements, ensuring the sum of min and max in each group does not exceed `k`.

### **Algorithm**
1. Sort the array.
2. Initialize two pointers:
   - `left = 0`
   - `right = n - 1`
3. Initialize `groupsCount = 0`.
4. While `left <= right`:
   - If `A[left] + A[right] > k`, then:
     - Form a group of 1 with `A[right]`.
     - Decrement `right`.
   - Else:
     - Try to form a group of 3:
       - Check if `left + 2 <= right` and `A[left] + A[right] <= k`.
       - If yes, form a group of 3: move `left += 2`, `right -= 1`.
     - Else try to form a group of 2:
       - Check if `A[left] + A[right] <= k`.
       - If yes, `left += 1`, `right -= 1`.
     - Else form a single element group:
       - `right -= 1`.
   - Increment `groupsCount` each time a group is formed.
5. Return `groupsCount`.

### **Java Code**
```java
import java.util.Arrays;

public int maxGroups(int[] A, int k) {
    Arrays.sort(A);
    int left = 0, right = A.length - 1;
    int groupsCount = 0;

    while (left <= right) {
        if (A[left] + A[right] > k) {
            // Can't form larger groups, assign a single group with the right element
            right--;
            groupsCount++;
        } else {
            // Try to form bigger groups
            if (left + 2 <= right && A[left] + A[right] <= k) {
                // Form group of 3
                left += 2;
                right--;
            } else {
                // Form group of 2 or 1
                left++;
                right--;
            }
            groupsCount++;
        }
    }
    return groupsCount;
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n log n)** for sorting, **O(n)** for the two-pointer traversal.
- **Space Complexity:** **O(1)**.

### **Dry Run**
- Input: `A = [3, 1, 4, 2, 5], k = 6`

| Sorted Array | left | right | Check | Action | Groups Count | Remaining Array Elements |
|----------------|--------|--------|--------|---------|--------------|--------------------------|
| [1, 2, 3, 4, 5] | 0 | 4 | 1+5=6 ≤6 | Form group of 2 or 3? 1+5=6 ≤6 | 1,2,3,4 | 1 |
| 1,2,3,4 | 0 | 3 | 1+4=5 ≤6 | Form group of 3 (indices 0,1,4) | left=2, right=3 | 1,2,3,4 | 2 |
| 2 | left=2 | right=3 | 3+4=7 >6 | Form single with 4 | right=2 | 2,3 | 3 |
| Loop ends | | | | Total groups = 3 |

---

## Summary
| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Greedy with Sorting | **O(n log n)** | **O(1)** | Attempts to form largest possible groups starting from sorted array. |
| 2 | Two-Pointer Greedy | **O(n log n)** | **O(1)** | Uses largest and smallest elements to efficiently create valid groups. |

---

## Final Tips for Interviews
- Always sort the array first to facilitate greedy grouping.
- Think about pairing smallest and largest elements to maximize group formation.
- Consider multiple approaches to optimize for readability and efficiency.
- Practice similar grouping and partitioning problems for mastery.

---

**Happy Coding!**
