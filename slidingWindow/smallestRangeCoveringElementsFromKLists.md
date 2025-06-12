# Smallest Range Covering Elements from K Lists

---

## Problem Overview
Given `k` sorted lists, find the **smallest range** that includes **at least one number from each list**.

**Example:**
```
Input: [[4,10,15,24,26], [0,9,12,20], [5,18,22,30]]
Output: [20, 24]
```
This range covers:
- 24 from the first list,
- 20 from the second list,
- 22 from the third list.

---

## Approach 1: Brute Force (Check All Possible Ranges)

### **Core Idea**
- Generate all possible ranges from the combined elements.
- For each range, verify if it contains at least one element from each list.
- Track and select the smallest such range.

### **Algorithm**
1. Combine all elements from all lists into a single list along with their list indices.
2. Sort this combined list.
3. Use two pointers (`left`, `right`) to slide over the sorted list.
4. Maintain a count of how many lists are covered within the current window.
5. Expand `right` until all lists are covered.
6. Contract `left` to find the minimal range that still covers all lists.
7. Keep track of the minimum range found.

### **Java Code**
```java
import java.util.*;

public int[] smallestRange(List<List<Integer>> nums) {
    int k = nums.size();
    List<int[]> allNumbers = new ArrayList<>();
    for (int i = 0; i < k; i++) {
        for (int num : nums.get(i)) {
            allNumbers.add(new int[]{num, i});
        }
    }
    Collections.sort(allNumbers, Comparator.comparingInt(a -> a[0]));
    int left = 0, minRangeStart = 0, minRangeEnd = Integer.MAX_VALUE;
    int count = 0;
    int[] counts = new int[k];
    int covered = 0;

    for (int right = 0; right < allNumbers.size(); right++) {
        int listIdx = allNumbers.get(right)[1];
        counts[listIdx]++;
        if (counts[listIdx] == 1) {
            covered++;
        }
        while (covered == k) {
            int start = allNumbers.get(left)[0];
            int end = allNumbers.get(right)[0];
            if (end - start < minRangeEnd - minRangeStart) {
                minRangeStart = start;
                minRangeEnd = end;
            }
            // Shrink window
            int leftIdx = allNumbers.get(left)[1];
            counts[leftIdx]--;
            if (counts[leftIdx] == 0) {
                covered--;
            }
            left++;
        }
    }
    return new int[]{minRangeStart, minRangeEnd};
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(N log N)**, where `N` is total number of elements across all lists (sorting step dominates).
- **Space Complexity:** **O(N)** for storing combined list and counts.

---

## Approach 2: Min-Heap (Efficient Sliding Window over K Sorted Lists)

### **Core Idea**
- Use a **min-heap** to keep track of the current minimum element among the chosen elements from each list.
- Maintain an index pointer for each list to know the current element.
- Track the maximum among the current elements to determine the current range.
- Move the pointer in the list with the current minimum element to try to find a smaller range.

### **Algorithm**
1. Initialize a **min-heap**.
2. For each list, insert its first element into the heap along with list index and element index.
3. Track the current maximum element among the heap's elements.
4. The current range is from the heap's minimum element to the current maximum.
5. Update the answer if the current range is smaller.
6. Remove the minimum element from the heap:
   - If possible, insert the next element from the same list into the heap.
   - Update the maximum if the inserted element is larger.
7. Repeat until any list is exhausted.

### **Java Code**
```java
import java.util.*;

public int[] smallestRange(List<List<Integer>> nums) {
    PriorityQueue<int[]> minHeap = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
    int max = Integer.MIN_VALUE;
    int start = 0, end = Integer.MAX_VALUE;

    // Initialize heap with first element of each list
    for (int i = 0; i < nums.size(); i++) {
        int val = nums.get(i).get(0);
        minHeap.offer(new int[]{val, i, 0}); // {value, listIndex, elementIndex}
        max = Math.max(max, val);
    }

    while (true) {
        int[] minVal = minHeap.peek();
        int minNum = minVal[0], listIdx = minVal[1], elemIdx = minVal[2];

        if (max - minNum < end - start) {
            start = minNum;
            end = max;
        }

        // Move pointer in the list with current min element
        minHeap.poll();
        if (elemIdx + 1 == nums.get(listIdx).size()) {
            break; // Reached end of one list
        }
        int nextVal = nums.get(listIdx).get(elemIdx + 1);
        minHeap.offer(new int[]{nextVal, listIdx, elemIdx + 1});
        max = Math.max(max, nextVal);
    }
    return new int[]{start, end};
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(N log k)**, where `N` is total number of elements and `k` is the number of lists.
- **Space Complexity:** **O(k)** for the heap.

---

## Summary

| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Brute Force with Sorting | **O(N log N)** | **O(N)** | Checks all possible ranges via sorted array. |
| 2 | Min-Heap Sliding Window | **O(N log k)** | **O(k)** | Efficiently finds minimal range by maintaining a window over k sorted lists. |

---

## Final Tips for Interviews
- Recognize the problem as merging k sorted lists and tracking the current minimal and maximal elements.
- Use **priority queues (heaps)** to efficiently manage the current elements.
- Always consider the trade-offs between sorting all elements vs. maintaining a sliding window with a heap.
- For large `k`, the heap-based approach is more optimal.

---

**Happy Coding!**
