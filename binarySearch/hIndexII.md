# H-Index II

The **H-Index II** problem asks for the researcher's H-Index given a sorted array of citations (ascending order). The H-Index is defined as the maximum value `h` such that the researcher has at least `h` papers with `h` or more citations.

---

## Problem Statement:

Given an array of citations sorted in ascending order, find the **H-Index**.

---

## Approach 1: Brute Force (Linear Search)

### **Core Idea:**

Iterate through each paper and check if the number of citations is at least as large as the number of papers with equal or greater citations.

### **Algorithm:**

1. Loop from the end of the array (highest citations) to the start.
2. For each citation `citations[i]`, compute the number of papers with citations >= `citations[i]` as `n - i`.
3. If `citations[i] >= n - i`, then `n - i` can be a candidate for H-Index.
4. Track the maximum such `n - i`.

### **Java Code:**

```java
public int hIndex(int[] citations) {
    int n = citations.length;
    int hIndex = 0;
    for (int i = 0; i < n; i++) {
        int papers = n - i; // Number of papers with citations >= citations[i]
        if (citations[i] >= papers) {
            hIndex = Math.max(hIndex, papers);
        }
    }
    return hIndex;
}

```

### **Complexity Analysis:**

- **Time Complexity:** O(n) — We traverse the array once.
- **Space Complexity:** O(1) — No extra space used.

### **Dry Run:**

- Input: `[0, 1, 3, 5, 6]`
- Iteration:
    - i=0: citations=0, papers=5 → 0 >= 5? No.
    - i=1: citations=1, papers=4 → 1 >= 4? No.
    - i=2: citations=3, papers=3 → 3 >= 3? Yes → hIndex=3.
    - i=3: citations=5, papers=2 → 5 >= 2? Yes → hIndex=3.
    - i=4: citations=6, papers=1 → 6 >= 1? Yes → hIndex=3.
- Final answer: **3**.

---

## Approach 2: Binary Search (Optimal Solution)

### **Core Idea:**

Use binary search on the sorted array to efficiently find the maximum `h` such that `citations[n - h] >= h`.

Because the array is sorted, we can leverage the properties to find the maximum `h` in `O(log n)`.

### **Algorithm:**

1. Initialize two pointers: `low=0` and `high=n-1`.
2. While `low <= high`:
    - Find `mid = low + (high - low) / 2`.
    - Let `h = n - mid` (candidate H-Index).
    - Check if `citations[mid] >= h`.
        - If yes, move `high = mid - 1` to try for a higher `h`.
        - If no, move `low = mid + 1` to look for a smaller `h`.
3. After the loop ends, the maximum `h` is `n - low`.

### **Java Code:**

```java
public int hIndex(int[] citations) {
    int n = citations.length;
    int low = 0, high = n - 1;

    while (low <= high) {
        int mid = low + (high - low) / 2;
        int h = n - mid;
        if (citations[mid] >= h) {
            high = mid - 1; // Try for a higher h
        } else {
            low = mid + 1; // Need a smaller h
        }
    }
    return n - low;
}

```

### **Complexity Analysis:**

- **Time Complexity:** O(log n) — Binary search reduces search space each iteration.
- **Space Complexity:** O(1) — No extra space used.

### **Dry Run:**

- Input: `[0, 1, 3, 5, 6]`
- `n=5`
- Iteration 1:
    - low=0, high=4
    - mid=2, citations=3, h=3
    - citations[mid]=3 >= h=3? Yes → high=1
- Iteration 2:
    - low=0, high=1
    - mid=0, citations=0, h=5
    - citations[mid]=0 >=5? No → low=1
- Iteration 3:
    - low=1, high=1
    - mid=1, citations=1, h=4
    - citations[mid]=1 >=4? No → low=2
- Loop ends: low=2
- H-Index= n - low= 5 - 2= 3

---

## Summary:

| Approach | Core Idea | Algorithm | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- |
| Brute Force | Check from the end for maximum h | Linear scan from end | O(n) | O(1) |
| Binary Search | Leverage sorted array for O(log n) search | Binary search for h | O(log n) | O(1) |

---

## Final Notes:

- Since the array is sorted, the **binary search approach** is preferred for efficiency.
- The problem showcases the importance of leveraging data order (sorting) to optimize search operations.
- Always analyze whether the data structure's properties (sorted/unsorted) can be exploited for efficient algorithms.

---

Feel free to ask for further clarifications or variations!
