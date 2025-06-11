# Median of Two Sorted Arrays

This guide covers multiple approaches to solve the **Median of Two Sorted Arrays** problem, progressing from brute-force to the most efficient solution. It is designed for quick revision and interview preparation.

---

## Problem Statement

Given two sorted arrays `nums1` and `nums2` of size `m` and `n`, find the median of the combined sorted array in **O(log(min(m, n)))** time.

---

## 1. Brute Force Approach

### Core Idea

Merge both arrays into a single sorted array and find the median directly.

### Algorithm

1. Merge the two sorted arrays into one sorted array.
2. If the total length is odd, the median is the middle element.
3. If even, the median is the average of the two middle elements.

### Java Code

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int[] merged = new int[nums1.length + nums2.length];
    int i = 0, j = 0, k = 0;

    // Merge process
    while (i < nums1.length && j < nums2.length) {
        if (nums1[i] < nums2[j]) {
            merged[k++] = nums1[i++];
        } else {
            merged[k++] = nums2[j++];
        }
    }

    // Copy remaining elements
    while (i < nums1.length) {
        merged[k++] = nums1[i++];
    }
    while (j < nums2.length) {
        merged[k++] = nums2[j++];
    }

    int n = merged.length;
    if (n % 2 == 1) {
        return merged[n / 2];
    } else {
        return (merged[(n / 2) - 1] + merged[n / 2]) / 2.0;
    }
}

```

### Complexity Analysis:

- **Time Complexity:** **O(m + n)** — Merging takes linear time.
- **Space Complexity:** **O(m + n)** — Additional space for the merged array.

### Dry Run

- Input: `nums1 = [1, 3]`, `nums2 = [2]`
- Merging: `[1, 2, 3]`
- Total length = 3 (odd)
- Median = element at index `1` → `2`

---

## 2. Better Solution: Partial Merge or Two-Pointer Approach

### Core Idea

Use two pointers to traverse both arrays and only find the median without fully merging.

### Algorithm

1. Use two pointers `i` and `j` for `nums1` and `nums2`.
2. Traverse until reaching the middle position(s).
3. Keep track of the last two elements seen during traversal.
4. Compute median based on total length being odd or even.

### Java Code

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int n1 = nums1.length, n2 = nums2.length;
    int total = n1 + n2;
    boolean isOdd = (total % 2 != 0);
    int midIndex = total / 2;

    int i = 0, j = 0;
    int count = 0;
    int prev = 0, curr = 0;

    while (count <= midIndex) {
        prev = curr;
        if (i < n1 && (j >= n2 || nums1[i] < nums2[j])) {
            curr = nums1[i++];
        } else {
            curr = nums2[j++];
        }
        count++;
    }

    if (isOdd) {
        return curr;
    } else {
        return (prev + curr) / 2.0;
    }
}

```

### Complexity Analysis:

- **Time Complexity:** **O(m + n)** — Traversal until the middle point.
- **Space Complexity:** **O(1)** — Only variables used.

### Dry Run

- Input: `nums1 = [1, 3]`, `nums2 = [2]`
- Traverse:
    - Step 1: `prev=0`, `curr=1` (i=1, j=0)
    - Step 2: `prev=1`, `curr=2` (j=1, j=1)
    - Middle index = 1
- Since total length is odd (3), median = `curr` = `2`.

---

## 3. Most Optimal Solution: Binary Search (Partition Method)

### Core Idea

Use binary search on the smaller array to find a partition where:

- Elements on the left of the partition are less than or equal to elements on the right.
- The partition divides the combined array into two halves with median properties.

### Intuition

- Partition both arrays such that:
    - Left sides contain the first half elements
    - Right sides contain the second half elements
- Adjust partition boundaries using binary search to satisfy the median conditions.

### Algorithm

1. Ensure `nums1` is the smaller array.
2. Set search range for partition `i` in `nums1`.
3. For each partition in `nums1`, compute corresponding partition in `nums2`.
4. Check if:
    - `maxLeftX <= minRightY`
    - `maxLeftY <= minRightX`
5. If true, compute median based on max of left sides and min of right sides.
6. Adjust partitions using binary search if conditions are not met.

### Java Code

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    if (nums1.length > nums2.length) {
        return findMedianSortedArrays(nums2, nums1);
    }

    int n1 = nums1.length, n2 = nums2.length;
    int low = 0, high = n1;

    while (low <= high) {
        int i = (low + high) / 2;
        int j = (n1 + n2 + 1) / 2 - i;

        int maxLeftX = (i == 0) ? Integer.MIN_VALUE : nums1[i - 1];
        int minRightX = (i == n1) ? Integer.MAX_VALUE : nums1[i];

        int maxLeftY = (j == 0) ? Integer.MIN_VALUE : nums2[j - 1];
        int minRightY = (j == n2) ? Integer.MAX_VALUE : nums2[j];

        if (maxLeftX <= minRightY && maxLeftY <= minRightX) {
            if ((n1 + n2) % 2 == 0) {
                return (Math.max(maxLeftX, maxLeftY) + Math.min(minRightX, minRightY)) / 2.0;
            } else {
                return Math.max(maxLeftX, maxLeftY);
            }
        } else if (maxLeftX > minRightY) {
            high = i - 1;
        } else {
            low = i + 1;
        }
    }
    throw new IllegalArgumentException("Input arrays are not sorted");
}

```

### Complexity Analysis:

- **Time Complexity:** **O(log(min(m, n)))** — binary search on the smaller array.
- **Space Complexity:** **O(1)** — only constant space used.

### Dry Run

- Input: `nums1 = [1, 3]`, `nums2 = [2, 4]`
- Step 1: Ensure `nums1` is smaller.
- Step 2: Binary search on `nums1`:
    - i=1, j=2-1=1
    - `maxLeftX=1`, `minRightX=3`
    - `maxLeftY=2`, `minRightY=4`
    - Conditions met:
        - `maxLeftX <= minRightY` → `1 <= 4`
        - `maxLeftY <= minRightX` → `2 <= 3`
    - Total length even, median = `(max(1,2)+min(3,4))/2 = (2+3)/2 = 2.5`

---

## Summary Table

| Approach | Time Complexity | Space Complexity | Suitability |
| --- | --- | --- | --- |
| Brute Force | O(m + n) | O(m + n) | Simple, but inefficient for large inputs |
| Partial Merge / Two Pointers | O(m + n) | O(1) | Slightly optimized, still linear traversal |
| Binary Search (Partition) | O(log(min(m, n))) | O(1) | Most optimal, suitable for large datasets |

---

## Final Tips:

- Always verify input arrays are sorted.
- Use binary search on the smaller array for efficiency.
- When implementing the binary search approach, handle edge cases carefully (e.g., empty arrays, all elements in one array).

---

This comprehensive guide should help you understand and implement the **Median of Two Sorted Arrays** problem efficiently in interviews and practice sessions!
