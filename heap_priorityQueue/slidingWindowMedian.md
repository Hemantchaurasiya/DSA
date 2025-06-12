# Sliding Window Median

This guide provides a detailed overview of solving the **Sliding Window Median** problem, covering approaches from brute-force to optimal solutions, along with explanations, code snippets, and dry runs for better understanding and quick revision.

---

## Problem Statement
Given an array `nums` and an integer `k`, find the median of every sliding window of size `k` as it moves from left to right across the array.

**Example:**
```plaintext
Input: nums = [1,3,-1,-3,5,3,6,7], k=3
Output: [1,-1,-1,3,5,6]
```
(Note: The medians are the middle elements when the window is sorted.)

---

## 1. Brute Force Approach

### **Approach Name:**  
**Brute Force**

### **Core Idea:**  
Sort each window of size `k` and find the median directly.

### **Algorithm:**  
1. Loop through the array from index `0` to `n - k`.
2. For each window, extract the subarray.
3. Sort the subarray.
4. Find the median:
   - If `k` is odd, median is the middle element.
   - If `k` is even, median is the average of the two middle elements.
5. Store the median in the result list.

### **Java Code:**
```java
import java.util.Arrays;

public double[] medianSlidingWindowBruteForce(int[] nums, int k) {
    int n = nums.length;
    double[] result = new double[n - k + 1];

    for (int i = 0; i <= n - k; i++) {
        int[] window = Arrays.copyOfRange(nums, i, i + k);
        Arrays.sort(window);
        if (k % 2 == 1) {
            result[i] = window[k / 2];
        } else {
            result[i] = (window[k / 2 - 1] + window[k / 2]) / 2.0;
        }
    }
    return result;
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n * k log k)`  
  Sorting each window takes `O(k log k)`, repeated for `n - k + 1` windows.
- **Space Complexity:** `O(k)` for temporary window array.

### **Dry Run:**
Input: `nums = [1,3,-1,-3,5,3,6,7]`, `k=3`

| Window Index | Window Elements | Sorted Window | Median | Result Array |
|----------------|-------------------|-----------------|---------|--------------|
| 0              | [1, 3, -1]       | [-1, 1, 3]     | 1       | [1]          |
| 1              | [3, -1, -3]      | [-3, -1, 3]    | -1      | [1, -1]      |
| 2              | [-1, -3, 5]      | [-3, -1, 5]    | -1      | [1, -1, -1]  |
| 3              | [-3, 5, 3]       | [-3, 3, 5]     | 3       | [1, -1, -1, 3] |
| 4              | [5, 3, 6]        | [3, 5, 6]      | 5       | [1, -1, -1, 3, 5] |
| 5              | [3, 6, 7]        | [3, 6, 7]      | 6       | [1, -1, -1, 3, 5, 6] |

---

## 2. Better Solution: Using Two Heaps (Balanced Data Structure)

### **Approach Name:**  
**Using Two Heaps (Max Heap and Min Heap)**

### **Core Idea:**  
Maintain two heaps:
- A **max-heap** for the smaller half of the numbers.
- A **min-heap** for the larger half.

Balance the heaps so that:
- The size difference is at most 1.
- The median is either the top of one heap or the average of the tops.

### **Algorithm:**  
1. Use two heaps:
   - Max-heap (`left`) for the smaller half.
   - Min-heap (`right`) for the larger half.
2. For each new element:
   - Insert into one of the heaps based on comparison.
   - Balance the heaps so that their sizes differ at most by 1.
3. When the window exceeds size `k`, remove the element that's sliding out of the window:
   - Determine which heap the element belongs to and remove it.
   - Rebalance heaps.
4. After processing each window, compute the median:
   - If total size is odd, median is top of the larger heap.
   - If even, median is the average of the tops.

### **Java Code (using `PriorityQueue`):**
```java
import java.util.*;

public class SlidingWindowMedian {
    private PriorityQueue<Integer> maxHeap; // Smaller half
    private PriorityQueue<Integer> minHeap; // Larger half
    private Map<Integer, Integer> delayed; // To handle lazy removals
    private int windowSize;

    public SlidingWindowMedian() {
        maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        minHeap = new PriorityQueue<>();
        delayed = new HashMap<>();
    }

    private void addNum(int num) {
        if (maxHeap.isEmpty() || num <= maxHeap.peek()) {
            maxHeap.offer(num);
        } else {
            minHeap.offer(num);
        }
        balanceHeaps();
    }

    private void removeNum(int num) {
        // Mark number as delayed (lazy removal)
        delayed.put(num, delayed.getOrDefault(num, 0) + 1);
        // Remove from heaps if present at top
        if (!maxHeap.isEmpty() && num <= maxHeap.peek()) {
            pruneHeap(maxHeap);
        } else {
            pruneHeap(minHeap);
        }
        balanceHeaps();
    }

    private void pruneHeap(PriorityQueue<Integer> heap) {
        while (!heap.isEmpty() && delayed.getOrDefault(heap.peek(), 0) > 0) {
            int num = heap.poll();
            delayed.put(num, delayed.get(num) - 1);
            if (delayed.get(num) == 0) {
                delayed.remove(num);
            }
        }
    }

    private void balanceHeaps() {
        if (maxHeap.size() > minHeap.size() + 1) {
            minHeap.offer(maxHeap.poll());
            pruneHeap(maxHeap);
        } else if (minHeap.size() > maxHeap.size()) {
            maxHeap.offer(minHeap.poll());
            pruneHeap(minHeap);
        }
    }

    public double getMedian() {
        if (maxHeap.size() > minHeap.size()) {
            return maxHeap.peek();
        } else {
            return (maxHeap.peek() + minHeap.peek()) / 2.0;
        }
    }

    public double[] medianSlidingWindow(int[] nums, int k) {
        this.windowSize = k;
        double[] result = new double[nums.length - k + 1];

        for (int i = 0; i < nums.length; i++) {
            addNum(nums[i]);
            if (i >= k - 1) {
                result[i - k + 1] = getMedian();
                removeNum(nums[i - k + 1]);
            }
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n log k)`  
  Each insertion and removal from heaps takes `O(log k)`.  
- **Space Complexity:** `O(k)` for the heaps and lazy removal map.

### **Dry Run:**
Input: `nums = [1,3,-1,-3,5,3,6,7]`, `k=3`

- Insert elements while maintaining heaps.
- Remove outgoing elements lazily.
- Compute medians after each window.

---

## 3. Most Optimal Solution: Using Balanced Data Structure + Lazy Removal

This approach is an extension of the two-heaps method, optimized with lazy deletion to handle removals efficiently, achieving `O(n log k)` time complexity.

---

## **Summary Table**

| Approach                                | Time Complexity | Space Complexity | Key Insight                                           |
|----------------------------------------|-------------------|---------------------|-------------------------------------------------------|
| Brute Force                          | `O(n * k log k)`  | `O(k)`              | Sort each window individually.                        |
| Two Heaps with Lazy Removal          | `O(n log k)`      | `O(k)`              | Use two heaps to maintain median efficiently.        |

---

## Final Tips for Interviews:
- Understand median calculation via two heaps and lazy deletion.
- Practice balancing heaps after insertions/removals.
- Recognize that the key challenge is maintaining the median efficiently.
- Use dry runs to develop intuition on heap operations and lazy deletion.

---

Revisit this guide for quick revision and to solidify your understanding of Sliding Window Median!
