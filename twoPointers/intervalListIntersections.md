# Interval List Intersections

---

## Problem Statement:
Given two lists of **non-overlapping, sorted intervals**, return the **intersection** of these two interval lists. Each list consists of intervals in the form `[start, end]`. The goal is to find all overlapping intervals between the two lists.

---

## Approach 1: Brute Force (Compare All Pairs)

### Core Idea:
Check every interval in list A against every interval in list B to find all overlaps.

### Algorithm:
1. For each interval in list A:
   - For each interval in list B:
     - Check if the two intervals overlap.
     - If they do, compute the intersection and add it to the result.
2. Return the list of all intersections.

### Java Code:
```java
public List<int[]> intervalIntersection(int[][] A, int[][] B) {
    List<int[]> result = new ArrayList<>();
    for (int[] a : A) {
        for (int[] b : B) {
            // Check for overlap
            int start = Math.max(a[0], b[0]);
            int end = Math.min(a[1], b[1]);
            if (start <= end) {
                result.add(new int[]{start, end});
            }
        }
    }
    return result;
}
```

### Complexity Analysis:
- **Time Complexity:** O(M * N), where M and N are the lengths of the two lists, as every pair is compared.
- **Space Complexity:** O(K), where K is the number of intersections found (for output storage).

### Dry Run:
- `A = [[0, 2], [5, 10], [13, 23], [24, 25]]`
- `B = [[1, 5], [8, 12], [15, 24], [25, 26]]`

**Steps:**
- Compare `[0, 2]` with each B interval:
  - Overlap with `[1, 5]` → `[1, 2]`
- Compare `[5, 10]` with each B interval:
  - Overlap with `[1, 5]` → `[5, 5]`
  - Overlap with `[8, 12]` → `[8, 10]`
- And so on...

---

## Approach 2: Two-Pointer Technique (Optimal & Efficient)

### Core Idea:
Use two pointers to traverse both lists simultaneously, exploiting their sorted and non-overlapping properties to find intersections efficiently.

### Algorithm:
1. Initialize two pointers: `i = 0` for list A, `j = 0` for list B.
2. While `i < len(A)` and `j < len(B)`:
   - Find the **overlap** between `A[i]` and `B[j]`:
     - `start = max(A[i][0], B[j][0])`
     - `end = min(A[i][1], B[j][1])`
   - If `start <= end`, this is an intersection → add `[start, end]` to result.
   - Move the pointer that has the smaller endpoint:
     - If `A[i][1] < B[j][1]`, increment `i`.
     - Else, increment `j`.
3. Continue until one list is exhausted.
4. Return the results.

### Java Code:
```java
public List<int[]> intervalIntersection(int[][] A, int[][] B) {
    List<int[]> result = new ArrayList<>();
    int i = 0, j = 0;
    while (i < A.length && j < B.length) {
        int start = Math.max(A[i][0], B[j][0]);
        int end = Math.min(A[i][1], B[j][1]);
        if (start <= end) {
            result.add(new int[]{start, end});
        }
        // Move the pointer with the smaller end value
        if (A[i][1] < B[j][1]) {
            i++;
        } else {
            j++;
        }
    }
    return result;
}
```

### Complexity Analysis:
- **Time Complexity:** O(M + N), as each interval in both lists is processed at most once.
- **Space Complexity:** O(K), for the output list of intersections.

### Dry Run:
- `A = [[0, 2], [5, 10], [13, 23], [24, 25]]`
- `B = [[1, 5], [8, 12], [15, 24], [25, 26]]`

**Steps:**
| Step | `A[i]` | `B[j]` | Overlap? | Intersection | Move Pointer | Explanation |
|-------|---------|---------|----------|----------------|----------------|--------------|
| Initial | [0, 2] | [1, 5] | Yes | [1, 2] | i++ | End of A[0] < B[0], move i |
| Next | [5, 10] | [1, 5] | Yes | [5, 5] | j++ | End of B[0], move j |
| Next | [5, 10] | [8, 12] | Yes | [8, 10] | i++ | End of A[1], move i |
| Next | [13, 23] | [8, 12] | No | - | j++ | No overlap, move j |
| Next | [13, 23] | [15, 24] | Yes | [15, 23] | i++ | End of A[2], move i |
| Next | [24, 25] | [15, 24] | Yes | [24, 24] | j++ | End of B[2], move j |
| Next | [24, 25] | [25, 26] | Yes | [25, 25] | i++ | End of B[2], move i |

---

## Summary & Best Practices:
- The **two-pointer approach** is optimal for sorted, non-overlapping interval lists.
- Always compare current intervals and move the pointer that ends first.
- Handle edge cases where one list is exhausted before the other.
- Use the `max` of starts and `min` of ends to determine overlaps.

---

## Final Tips:
- Recognize sorted interval properties to optimize solutions.
- Practice both iterative two-pointer and brute-force methods.
- Visualize interval overlaps to build intuition.
- Write clean, efficient code with clear pointer updates.

Happy coding!
