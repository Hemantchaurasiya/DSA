# Kth Largest Element in an Array

This comprehensive guide covers multiple approaches to find the **Kth largest element** in an array, suitable for interview preparation and quick revision. We progress from brute-force to the most optimized solutions.

---

## 1. Brute Force Approach

### **Core Idea**
Sort the array in descending order and pick the element at index `k-1`. This is straightforward but not efficient for large datasets.

### **Algorithm**
1. Sort the array in descending order.
2. Return the element at index `k-1`.

### **Java Code**
```java
import java.util.Arrays;

public class KthLargest {
    public int findKthLargest(int[] nums, int k) {
        Arrays.sort(nums); // sorts in ascending order
        return nums[nums.length - k]; // kth largest is at this position
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(n log n)` due to sorting.
- **Space Complexity:**  
  `O(1)` if sorting in-place, or `O(n)` depending on the sorting implementation.

---

### **Dry Run with Sample Input**
| Input Array                 | [3, 2, 1, 5, 6, 4], K=2 |
|------------------------------|-------------------------|
| Sorted Array (ascending)     | [1, 2, 3, 4, 5, 6]     |
| kth largest element          | `nums[6 - 2] = nums[4] = 5` |

---

## 2. Better Solution: Max Heap

### **Core Idea**
Use a max heap (priority queue) to efficiently extract the largest elements. Pop from the heap `k-1` times; the next element is the kth largest.

### **Algorithm**
1. Insert all elements into a max heap.
2. Remove the maximum element `k-1` times.
3. The next element at the top of the heap is the kth largest.

### **Java Code**
```java
import java.util.PriorityQueue;

public class KthLargest {
    public int findKthLargest(int[] nums, int k) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
        for (int num : nums) {
            maxHeap.offer(num);
        }
        for (int i = 0; i < k - 1; i++) {
            maxHeap.poll();
        }
        return maxHeap.peek();
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(n + k log n)` — building the heap takes `O(n)`, and removing `k-1` elements costs `O(k log n)`.
- **Space Complexity:**  
  `O(n)` for the heap.

---

### **Dry Run with Sample Input**
| Input Array                 | [3, 2, 1, 5, 6, 4], K=2 |
|------------------------------|-------------------------|
| Max Heap after insertion     | [6, 5, 4, 3, 2, 1]     |
| Remove the largest `k-1=1` time | Remove 6 → heap: [5, 4, 3, 2, 1] |
| Next top (kth largest)       | 5                       |

---

## 3. Most Optimal Solution: **QuickSelect Algorithm**

### **Core Idea**
Use the QuickSelect algorithm, which is a selection algorithm based on the QuickSort partitioning idea. It partitions the array around a pivot such that elements greater than pivot are on one side, and smaller are on the other, enabling us to find the kth largest in average `O(n)` time.

### **Algorithm**
1. Convert `k` to target index: `target = nums.length - k`.
2. Use partitioning (similar to QuickSort):
   - Choose a pivot.
   - Rearrange elements so that all greater than pivot are on one side.
   - Determine the pivot's position.
3. If pivot's position matches `target`, return pivot.
4. Else, recurse into the appropriate side.

### **Java Code**
```java
public class KthLargest {
    public int findKthLargest(int[] nums, int k) {
        int target = nums.length - k;
        return quickSelect(nums, 0, nums.length - 1, target);
    }
    
    private int quickSelect(int[] nums, int left, int right, int target) {
        int pivotIndex = partition(nums, left, right);
        if (pivotIndex == target) {
            return nums[pivotIndex];
        } else if (pivotIndex < target) {
            return quickSelect(nums, pivotIndex + 1, right, target);
        } else {
            return quickSelect(nums, left, pivotIndex - 1, target);
        }
    }
    
    private int partition(int[] nums, int left, int right) {
        int pivot = nums[right];
        int i = left;
        for (int j = left; j < right; j++) {
            if (nums[j] >= pivot) { // for kth largest, use >=
                swap(nums, i, j);
                i++;
            }
        }
        swap(nums, i, right);
        return i;
    }
    
    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  Average `O(n)`, Worst `O(n^2)` in the worst case (rare with good pivot selection).
- **Space Complexity:**  
  `O(1)` (in-place partitioning).

---

### **Dry Run with Sample Input**
| Input Array                 | [3, 2, 1, 5, 6, 4], K=2 |
|------------------------------|-------------------------|
| target index = 4 (since `6-2=4`) | -                   |
| First partition (pivot=4)     | Rearrange to: [6, 5, 4, 3, 2, 1] (pivot 4 at index 2) |
| pivot index=2 < target=4      | recurse right side [3, 2, 1] |
| Second partition (pivot=1)    | Rearrange: [6, 5, 4, 3, 2, 1], pivot=1 at index 5 |
| pivot index=5 > target=4      | recurse left side [3, 2] |
| Partition with pivot=2        | Array: [3, 2], pivot=2 at index 1 |
| index=1 < target=4            | recurse right [ ] (no more elements) |
| Final answer: 5 (element at index 4) | - |

---

## **Summary Table**

| Approach               | Best for             | Time Complexity | Space Complexity | Notes                             |
|------------------------|----------------------|-----------------|------------------|----------------------------------|
| Brute Force           | Small datasets       | `O(n log n)`   | `O(1)`           | Sorting the array                |
| Max Heap              | Moderate datasets    | `O(n + k log n)` | `O(n)`         | PriorityQueue implementation     |
| QuickSelect (Optimal) | Large datasets, average case | `O(n)` average | `O(1)`     | In-place, efficient for large data |

---

This structured revision provides a clear understanding of multiple approaches to the **Kth Largest Element in an Array** problem, including their intuition, implementation, and complexity considerations.
