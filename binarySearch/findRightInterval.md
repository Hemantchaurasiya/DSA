# Find Right Interval

This guide provides a comprehensive overview of solving the **"Find Right Interval"** problem, covering multiple approaches from brute-force to optimized solutions. It is structured to aid in interview preparation and quick revision.

---

## Problem Statement

Given a list of **intervals**, for each interval, find the **index of the interval** that **starts immediately after** the current interval **ends**. If no such interval exists, return -1.

**Input:** An array of intervals, e.g., `[[1,2],[2,3],[3,4]]`

**Output:** An array of indices representing the "right interval" for each interval.

---

## Approach 1: Brute Force

### Core Idea

For each interval, scan all other intervals to find the one with the **smallest starting point** that is **greater than or equal to** the current interval's end.

### Algorithm

1. Loop through each interval `i`.
2. For each `i`, loop through all other intervals `j`.
3. Check if `intervals[j].start >= intervals[i].end`.
4. Keep track of the minimal such start to determine the right interval.
5. Record the index of this interval; if none found, record -1.

### Java Code

```java
public int[] findRightInterval(int[][] intervals) {
    int n = intervals.length;
    int[] result = new int[n];

    for (int i = 0; i < n; i++) {
        int minStart = Integer.MAX_VALUE;
        int index = -1;
        for (int j = 0; j < n; j++) {
            if (intervals[j][0] >= intervals[i][1] && intervals[j][0] < minStart) {
                minStart = intervals[j][0];
                index = j;
            }
        }
        result[i] = index;
    }
    return result;
}

```

### Complexity Analysis

- **Time Complexity:** **O(n^2)**
Because of nested loops over all intervals.
- **Space Complexity:** **O(n)**
For the result array.

### Dry Run

| Intervals | Result Initialization | i=0 | j=0 | j=1 | j=2 | Result after iteration |
| --- | --- | --- | --- | --- | --- | --- |
| [[1,2],[2,3],[3,4]] | result = [0,0,0] | i=0, end=2 | j=0, start=1 (not >=2) | j=1, start=2 (>=2) -> minStart=2, index=1 | j=2, start=3 (>=2), minStart=2, so ignore | result[0]=1 |
| Repeat similarly for other indices. |  |  |  |  |  |  |

---

## Approach 2: Better Solution Using Sorting + Binary Search

### Core Idea

Sort the intervals based on their start times. For each interval, perform a binary search to find the **smallest start time** that is **greater than or equal to** the current interval’s end.

### Algorithm

1. Create an array of pairs `(start, index)` for all intervals.
2. Sort this array based on the start times.
3. For each interval:
    - Use binary search on the sorted array to find the **lower bound** of `intervals[i][1]`.
    - If found, the corresponding index is the right interval.
    - Otherwise, -1.

### Java Code

```java
import java.util.Arrays;

public int[] findRightInterval(int[][] intervals) {
    int n = intervals.length;
    int[] result = new int[n];

    // Create an array of (start, index)
    int[][] starts = new int[n][2];
    for (int i = 0; i < n; i++) {
        starts[i][0] = intervals[i][0];
        starts[i][1] = i;
    }

    // Sort based on start times
    Arrays.sort(starts, (a, b) -> a[0] - b[0]);

    // For each interval, binary search for the right interval
    for (int i = 0; i < n; i++) {
        int target = intervals[i][1];
        int index = binarySearch(starts, target);
        result[i] = index;
    }

    return result;
}

private int binarySearch(int[][] starts, int target) {
    int low = 0, high = starts.length - 1;
    int ans = -1;

    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (starts[mid][0] >= target) {
            ans = starts[mid][1];
            high = mid - 1; // Search on the left for a smaller start
        } else {
            low = mid + 1;
        }
    }
    return ans;
}

```

### Complexity Analysis

- **Time Complexity:** **O(n log n)**
Sorting takes O(n log n), binary search for each interval takes O(log n).
- **Space Complexity:** **O(n)**
For auxiliary data structures.

### Dry Run

Suppose `intervals = [[1,2], [2,3], [3,4]]`

Sorted starts: `[(1,0), (2,1), (3,2)]`

- For interval `[1,2]`:
    - Search for start >= 2:
        - Binary search finds `(2,1)` -> index=1
- For `[2,3]`:
    - Search for start >= 3:
        - Finds `(3,2)` -> index=2
- For `[3,4]`:
    - Search for start >= 4:
        - Not found -> -1

Result: `[1, 2, -1]`

---

## Approach 3: Most Optimal Using Sorting + Binary Search (with Built-in Methods)

This is essentially the same as Approach 2 but emphasizes the use of language features (e.g., TreeMap in Java) for cleaner implementation.

### Core Idea

Use a **TreeMap** to map start times to indices. For each interval, perform a `ceilingKey()` search for the interval's end.

### Algorithm

1. Populate a `TreeMap<Integer, Integer>` with key as start time and value as index.
2. For each interval:
    - Use `ceilingKey()` to find the smallest key >= interval's end.
    - If exists, return the mapped index; otherwise, -1.

### Java Code

```java
import java.util.TreeMap;

public int[] findRightInterval(int[][] intervals) {
    int n = intervals.length;
    int[] result = new int[n];
    TreeMap<Integer, Integer> map = new TreeMap<>();

    // Populate TreeMap
    for (int i = 0; i < n; i++) {
        map.put(intervals[i][0], i);
    }

    for (int i = 0; i < n; i++) {
        Integer key = map.ceilingKey(intervals[i][1]);
        result[i] = (key != null) ? map.get(key) : -1;
    }

    return result;
}

```

### Complexity Analysis

- **Time Complexity:** **O(n log n)** due to TreeMap insertions and queries.
- **Space Complexity:** **O(n)** for the map.

---

## Final Notes

- The brute-force approach is simple but inefficient for large inputs.
- Sorting + binary search reduces the complexity significantly.
- Utilizing data structures like TreeMap can make the solution cleaner and more elegant.
- Always consider input constraints to choose the most optimal approach.

---

**Happy Coding!**
