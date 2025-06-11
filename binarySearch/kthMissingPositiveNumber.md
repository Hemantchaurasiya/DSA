# Kth Missing Positive Number

---

## Problem Overview

Given an **array of sorted positive integers** `arr` and an integer `k`, find the **kth positive integer** that **is missing** from the array.

---

## Approach 1: Brute Force

### Core Idea

Check each positive number starting from 1, and count how many missing numbers we encounter until we reach the `kth` missing number.

### Algorithm

1. Initialize a counter for missing numbers (`missingCount = 0`).
2. Initialize a variable `num` starting from 1.
3. Use a set or hash structure to quickly check if a number is in `arr`.
4. Loop infinitely:
    - If `num` is **not** in `arr`, increment `missingCount`.
    - If `missingCount == k`, return `num`.
    - Increment `num` and repeat.

### Java Code

```java
public int findKthPositive(int[] arr, int k) {
    Set<Integer> set = new HashSet<>();
    for (int num : arr) {
        set.add(num);
    }
    int num = 1;
    int missingCount = 0;
    while (true) {
        if (!set.contains(num)) {
            missingCount++;
            if (missingCount == k) {
                return num;
            }
        }
        num++;
    }
}

```

### Complexity Analysis

- **Time Complexity:** O(N + M), where N is the number of elements in `arr`, and M is the number of missing numbers to reach `k`. In worst case, M could be large.
- **Space Complexity:** O(N) for the hash set.

### Dry Run

Suppose `arr = [2, 3, 4, 7, 11]`, `k = 5`.

| Step | `num` | Is `num` in `arr`? | Missing Count | Action | Return? |
| --- | --- | --- | --- | --- | --- |
| 1 | 1 | No | 1 | missingCount=1 | No |
| 2 | 2 | Yes | 1 | no change | No |
| 3 | 3 | Yes | 1 | no change | No |
| 4 | 4 | Yes | 1 | no change | No |
| 5 | 5 | No | 2 | missingCount=2 | No |
| 6 | 6 | No | 3 | missingCount=3 | No |
| 7 | 7 | Yes | 3 | no change | No |
| 8 | 8 | No | 4 | missingCount=4 | No |
| 9 | 9 | No | 5 | missingCount=5, return 9 | Yes |

---

## Approach 2: Using Binary Search (Most Optimal)

### Core Idea

Use the **sorted nature** of the array to leverage binary search and determine how many numbers are missing **up to** a certain point.

- The count of missing numbers **up to** `arr[mid]` is `arr[mid] - (mid + 1)`.
- If this count is **less** than `k`, we need to look further right.
- Otherwise, we look left to find the smallest index where missing count >= k.

### Algorithm

1. Initialize `low = 0`, `high = arr.length - 1`.
2. While `low <= high`:
    - Calculate `mid = low + (high - low) / 2`.
    - Compute `missing = arr[mid] - (mid + 1)`.
    - If `missing < k`, move `low = mid + 1`.
    - Else, move `high = mid - 1`.
3. After the loop, the answer is `low + k` (since `low` is the count of numbers where missing count >= k).

### Java Code

```java
public int findKthPositive(int[] arr, int k) {
    int low = 0, high = arr.length - 1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        int missing = arr[mid] - (mid + 1);
        if (missing < k) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    // After the loop, low is the position where missing count >= k
    return low + k;
}

```

### Complexity Analysis

- **Time Complexity:** O(log N), where N is the size of `arr`, due to binary search.
- **Space Complexity:** O(1), no extra space used apart from variables.

### Dry Run

Suppose `arr = [2, 3, 4, 7, 11]`, `k = 5`.

| Step | `low` | `high` | `mid` | `arr[mid]` | `missing` | Condition | Action |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 4 | 2 | 4 | 4 - 3=1 | missing < k? | Yes, low=3 |
| 2 | 3 | 4 | 3 | 7 | 7 - 4=3 | missing < 5? | Yes, low=4 |
| 3 | 4 | 4 | 4 | 11 | 11 - 5=6 | missing >= 5 | high=3 (exit loop) |

Post loop, `low=4`. The kth missing number is `low + k = 4 + 5 = 9`.

---

## Summary

| Approach | Core Idea | Time Complexity | Space Complexity | Best For |
| --- | --- | --- | --- | --- |
| Brute Force | Check each number sequentially, count missing | O(N + M) | O(N) | Small constraints, simplicity |
| Binary Search | Leverage sorted array to find missing count efficiently | O(log N) | O(1) | Large constraints, optimal performance |

---

## Final Tips

- Use binary search for optimal solutions when the array is sorted.
- Understand the relationship between array indices and missing counts: `arr[mid] - (mid + 1)`.
- Handle edge cases where `k` exceeds the total missing numbers within the array, then the answer is `arr[arr.length - 1] + (k - missingCount)`.

---

This revision note provides a comprehensive overview suitable for quick review and interview prep for the **Kth Missing Positive Number** problem.
