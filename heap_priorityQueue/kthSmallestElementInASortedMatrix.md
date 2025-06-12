# Kth Largest Element in an Array

This problem asks us to find the **kth largest element** in an unsorted array. The challenge is to do so efficiently, balancing time and space complexities.

---

## 1. Brute Force Approach

### **Core Idea:**
Sort the array in descending order and pick the element at index `k-1`.

### **Algorithm:**
1. Sort the array in descending order.
2. Return the element at index `k-1`.

### **Java Code:**
```java
import java.util.Arrays;

public class KthLargest {
    public int findKthLargest(int[] nums, int k) {
        Arrays.sort(nums); // Sort in ascending order
        int n = nums.length;
        return nums[n - k]; // kth largest element
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n log n)` due to sorting.
- **Space Complexity:** `O(1)` or `O(n)` depending on the sorting implementation (generally O(1) for in-place sorts).

### **Dry Run:**
- Input: `nums = [3, 2, 1, 5, 6, 4], k=2`
- Sorted array: `[1, 2, 3, 4, 5, 6]`
- kth largest: `nums[6 - 2] = nums[4] = 5`

---

## 2. Better Solution: Using Min-Heap (Priority Queue)

### **Core Idea:**
Maintain a min-heap of size `k`. Iterate through the array, and for each element:
- If the heap size is less than `k`, add the element.
- Else, compare with the root (smallest in heap). If larger, replace it.

This ensures the heap always contains the **k largest elements**, and the smallest among them is the kth largest overall.

### **Algorithm:**
1. Initialize a min-heap (priority queue) of size `k`.
2. Iterate through each number in the array:
   - Add the number to the heap if size < k.
   - Else, compare with the root; if larger, replace the root.
3. After iteration, the root of the heap is the kth largest element.

### **Java Code:**
```java
import java.util.PriorityQueue;

public class KthLargest {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>(); // Min-heap
        for (int num : nums) {
            if (minHeap.size() < k) {
                minHeap.offer(num);
            } else if (num > minHeap.peek()) {
                minHeap.poll();
                minHeap.offer(num);
            }
        }
        return minHeap.peek();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n log k)` since each insertion/deletion in the heap takes `O(log k)`, and we do this for all `n` elements.
- **Space Complexity:** `O(k)` for the heap.

### **Dry Run:**
- Input: `nums = [3, 2, 1, 5, 6, 4], k=2`
- Step-by-step:
  
| Element | Heap Contents (min-heap) | Action                                |
|----------|--------------------------|----------------------------------------|
| 3        | [3]                      | Insert (size < k)                     |
| 2        | [2, 3]                   | Insert                                |
| 1        | [2, 3]                   | 1 < min (2), ignore                   |
| 5        | [2, 3] → replace 2 with 5 | 5 > 2, replace 2                    |
|          | [3, 5]                   | Updated heap                          |
| 6        | [3, 5] → replace 3 with 6 | 6 > 3, replace 3                   |
|          | [5, 6]                   | Final heap                            |
| 4        | [5, 6]                   | 4 < min (5), ignore                   |

- **Result:** The root of heap = `5` (the 2nd largest element).

---

## 3. Optimal Solution: Using Quickselect Algorithm

### **Core Idea:**
**Quickselect** is a selection algorithm based on the QuickSort partitioning method. It aims to find the `k`th largest element in average `O(n)` time by partially sorting the array.

### **Intuition:**
- Partition the array around a pivot.
- Determine the position of the pivot after partitioning.
- If the pivot's position matches the desired index (`n - k`), return it.
- Else, recurse into the appropriate partition.

### **Algorithm:**
1. Define `quickSelect(nums, left, right, k_smallest)`:
   - Select a random pivot.
   - Partition the array around the pivot.
   - If pivot index == `k_smallest`, return `nums[pivot_index]`.
   - Else recurse into the left or right partition accordingly.
2. Call `quickSelect(nums, 0, n-1, n - k)`.

### **Java Code:**
```java
import java.util.Random;

public class KthLargest {
    private Random rand = new Random();

    public int findKthLargest(int[] nums, int k) {
        int n = nums.length;
        return quickSelect(nums, 0, n - 1, n - k);
    }

    private int quickSelect(int[] nums, int left, int right, int k_smallest) {
        if (left == right) {
            return nums[left];
        }

        int pivotIndex = left + rand.nextInt(right - left + 1);
        pivotIndex = partition(nums, left, right, pivotIndex);

        if (pivotIndex == k_smallest) {
            return nums[pivotIndex];
        } else if (pivotIndex < k_smallest) {
            return quickSelect(nums, pivotIndex + 1, right, k_smallest);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, k_smallest);
        }
    }

    private int partition(int[] nums, int left, int right, int pivotIndex) {
        int pivotValue = nums[pivotIndex];
        // Move pivot to end
        swap(nums, pivotIndex, right);
        int storeIndex = left;

        for (int i = left; i < right; i++) {
            if (nums[i] > pivotValue) { // For kth largest, compare > 
                swap(nums, storeIndex, i);
                storeIndex++;
            }
        }
        // Move pivot to its final place
        swap(nums, storeIndex, right);
        return storeIndex;
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(n)` on average, but worst-case `O(n^2)` (rare with random pivot).
- **Space Complexity:** `O(1)` (in-place partitioning).

### **Dry Run:**
- Input: `nums = [3, 2, 1, 5, 6, 4], k=2`
- `n=6`, so `k_smallest = 6 - 2 = 4`
- Random pivot chosen, say index 2 (`nums[2]=1`)
- Partition around 1:
  - Elements > 1 move left: `[3, 2, 1, 5, 6, 4]` after partitioning, 1 ends up at position 2.
  - Since `2 < 4`, recurse into the right subarray `[5, 6, 4]`.
- Repeat process until the pivot's position matches `k_smallest`.
- Ultimately, find the element at position `4`, which is the 2nd largest.

---

# Summary Table

| Approach                 | Data Structure/Strategy | Time Complexity | Space Complexity | Remarks                                    |
|--------------------------|--------------------------|-----------------|------------------|--------------------------------------------|
| Brute Force              | Sorting                  | `O(n log n)`   | `O(1)` or `O(n)` | Simpler but less efficient for large n   |
| Min-Heap (Priority Queue)| Heap (size k)            | `O(n log k)`   | `O(k)`           | Efficient for large arrays with small k  |
| Quickselect (Partition)  | In-place partitioning   | `O(n)` on average | `O(1)`       | Most optimal, but worst-case `O(n^2)`    |

---

## Final Tips:
- Use **sorting** for simplicity and small constraints.
- Use **min-heap** when `k` is small relative to `n`.
- Use **Quickselect** for the best average performance, especially on large datasets.

---

**Happy Revising!**
