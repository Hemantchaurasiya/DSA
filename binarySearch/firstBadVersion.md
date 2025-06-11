# First Bad Version

---

## Problem Overview

Given `n` versions [1, 2, 3, ..., n], where **some version** is the first **bad version** that causes all subsequent versions to be bad, find the **first bad version**.

**Constraints:**

- You are given an API `bool isBadVersion(version)` which returns whether the version is bad.
- The versions are ordered sequentially.
- The goal is to minimize the number of calls to `isBadVersion`.

---

## Approach 1: Brute Force

### Core Idea

Check each version sequentially from 1 to n until you find the first bad version.

### Algorithm

1. Loop from version 1 to n.
2. For each version, call `isBadVersion`.
3. Return the first version where `isBadVersion` returns `true`.

### Java Code

```java
public int firstBadVersion(int n) {
    for (int i = 1; i <= n; i++) {
        if (isBadVersion(i)) {
            return i;
        }
    }
    return -1; // Should not reach here if there's at least one bad version
}

```

### Complexity Analysis

- **Time Complexity:** **O(n)**, because in the worst case, we check all versions.
- **Space Complexity:** **O(1)**, no extra space used.

### Dry Run

| Input | n=5, First bad version = 4 |
| --- | --- |
| Iteration | `i` |
| 1 | 1 |
| 2 | 2 |
| 3 | 3 |
| 4 | 4 |

---

## Approach 2: Binary Search (Optimized Solution)

### Core Idea

Use binary search to reduce the number of calls to `isBadVersion`. Since the versions are ordered, the first bad version divides the array into two parts:

- All versions before it are good.
- From it onwards, all are bad.

### Algorithm

1. Initialize `left = 1`, `right = n`.
2. While `left < right`:
    - Find `mid = left + (right - left) / 2`.
    - Call `isBadVersion(mid)`.
    - If `mid` is bad, then the first bad version is at `mid` or before; set `right = mid`.
    - Else, set `left = mid + 1`.
3. After the loop, `left` will point to the first bad version.

### Java Code

```java
public int firstBadVersion(int n) {
    int left = 1, right = n;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (isBadVersion(mid)) {
            right = mid;
        } else {
            left = mid + 1;
        }
    }
    return left;
}

```

### Complexity Analysis

- **Time Complexity:** **O(log n)**, because binary search halves the search space each iteration.
- **Space Complexity:** **O(1)**, constant space.

### Dry Run

| Input | n=10, First bad version = 7 |
| --- | --- |
| Initial | left=1, right=10 |
| Iteration 1 | mid=5 |
| Iteration 2 | mid=8 |
| Iteration 3 | mid=7 |
| Loop ends | left=7, right=7 |

---

## Approach 3: Recursive Binary Search (Optional)

### Core Idea

Similar to iterative binary search but implemented recursively.

### Algorithm

- Define a recursive function with `low` and `high`.
- Base case: when `low >= high`, return `low`.
- Recursively search in the half where the first bad version lies based on `isBadVersion(mid)`.

### Java Code

```java
public int firstBadVersion(int n) {
    return binarySearch(1, n);
}

private int binarySearch(int low, int high) {
    if (low >= high) {
        return low;
    }
    int mid = low + (high - low) / 2;
    if (isBadVersion(mid)) {
        return binarySearch(low, mid);
    } else {
        return binarySearch(mid + 1, high);
    }
}

```

### Complexity Analysis

- **Time Complexity:** **O(log n)**
- **Space Complexity:** **O(log n)** due to recursion stack.

---

## Summary

| Approach | Core Idea | Best Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | Sequential search | **O(n)** | **O(1)** | Simplest, but inefficient for large `n` |
| Binary Search | Divide and conquer | **O(log n)** | **O(1)** | Most optimal solution |
| Recursive Binary Search | Divide and conquer (recursive) | **O(log n)** | **O(log n)** | Similar to iterative, with recursion stack |

---

## Final Tips for Interviews

- Always aim for the optimal solution using binary search.
- Understand the problem constraints to choose the best approach.
- Be ready to explain how binary search reduces the search space and improves efficiency.
- Practice implementing both iterative and recursive binary search.

---

**End of Revision Note**
