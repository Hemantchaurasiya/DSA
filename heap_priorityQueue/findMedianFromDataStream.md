# Find Median from Data Stream

The problem involves designing a data structure that can efficiently return the **median** of a stream of integers at any point. The data should support adding new numbers and retrieving the median in optimal time.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Maintain Sorted List**

### **Core Idea:**  
Store all numbers in a list, keep it sorted after each insertion, and directly access the median.

### **Algorithm:**  
1. Initialize an empty list.  
2. For each new number:
   - Insert it into the list at the correct position to keep the list sorted (using binary search).  
   - The median is then:
     - If odd number of elements: middle element.
     - If even number of elements: average of two middle elements.

### **Java Code:**  
```java
import java.util.ArrayList;
import java.util.Collections;

public class MedianFinder {
    private ArrayList<Integer> list;

    public MedianFinder() {
        list = new ArrayList<>();
    }

    public void addNum(int num) {
        int insertIndex = Collections.binarySearch(list, num);
        if (insertIndex < 0) insertIndex = -insertIndex - 1;
        list.add(insertIndex, num);
    }

    public double findMedian() {
        int n = list.size();
        if (n % 2 == 1) {
            return list.get(n / 2);
        } else {
            return (list.get(n / 2 - 1) + list.get(n / 2)) / 2.0;
        }
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:**  
  - `addNum`: `O(n)` for insertion (since shifting elements).  
  - `findMedian`: `O(1)`.  
- **Space Complexity:** `O(n)` for storing all elements.

### **Dry Run:**  
- Input: add 1, add 5, add 2, add 10  
- List after each insertion: `[1]`, `[1, 5]`, `[1, 2, 5]`, `[1, 2, 5, 10]`  
- Medians after each insertion: 1, 3.0, 2, 3.5

---

## 2. Better Solution: Using Two Heaps (Optimal Solution)

### **Approach Name:**  
**Two Heaps (Max-Heap + Min-Heap)**

### **Core Idea:**  
Maintain two heaps:
- **Max-heap** for the lower half of numbers.
- **Min-heap** for the upper half of numbers.

Balance the heaps so that:
- The size difference is at most 1.
- The median can be extracted directly:
  - If odd total elements, median is the top of the heap with more elements.
  - If even, median is the average of the tops of both heaps.

### **Algorithm:**  
1. Initialize a max-heap (`low`) and a min-heap (`high`).  
2. For each new number:
   - Add it to the appropriate heap (`low` or `high`) based on comparison with current median.
   - Rebalance heaps if size difference exceeds 1.
3. To find median:
   - If total number of elements is odd, median is top of the larger heap.
   - If even, median is average of tops of both heaps.

### **Java Code:**  
```java
import java.util.PriorityQueue;

public class MedianFinder {
    private PriorityQueue<Integer> low;  // Max-heap (store smaller half)
    private PriorityQueue<Integer> high; // Min-heap (store larger half)

    public MedianFinder() {
        low = new PriorityQueue<>((a, b) -> b - a);
        high = new PriorityQueue<>();
    }

    public void addNum(int num) {
        if (low.isEmpty() || num <= low.peek()) {
            low.offer(num);
        } else {
            high.offer(num);
        }

        // Rebalance heaps
        if (low.size() > high.size() + 1) {
            high.offer(low.poll());
        } else if (high.size() > low.size()) {
            low.offer(high.poll());
        }
    }

    public double findMedian() {
        if (low.size() > high.size()) {
            return low.peek();
        } else {
            return (low.peek() + high.peek()) / 2.0;
        }
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:**  
  - `addNum`: `O(log n)` due to heap insertions and rebalancing.  
  - `findMedian`: `O(1)`.  
- **Space Complexity:** `O(n)`.

### **Dry Run:**  
- Input: add 1, add 5, add 2, add 10  
- After each addition:
  - Add 1: low = [1], high = []
  - Add 5: low = [1], high = [5]
  - Add 2: low = [2, 1], high = [5]
  - Add 10: low = [2, 1], high = [5, 10] (rebalance if needed)
- Medians: after each insertion: 1, (1+5)/2=3.0, 2, (2+5)/2=3.5

---

## 3. **Most Optimal Solution: Balanced Heaps + Lazy Balancing**

The second approach described above (two heaps) is the **most efficient and commonly used** in real-world scenarios for streaming median problems. It provides:
- **Fast insertions (`O(log n)`)**
- **Instant median retrieval (`O(1)`)**

This approach ensures the data structure scales well with large data streams.

---

# Summary Table

| Approach                                              | Strategy                                              | Time Complexity                       | Space Complexity | Remarks                                              |
|-------------------------------------------------------|------------------------------------------------------|----------------------------------------|------------------|-------------------------------------------------------|
| Maintain Sorted List                                | Insert at sorted position, then get median          | `O(n)` per insertion                   | `O(n)`           | Simple but inefficient for large streams             |
| Two Heaps (Max-Heap + Min-Heap)                       | Balance two heaps for median in `O(log n)`         | `O(log n)` per insertion               | `O(n)`           | Most efficient for streaming median                   |

---

## Final Tips:
- Use **two heaps** for the best trade-off between speed and complexity.
- Always rebalance heaps after each insertion.
- The median for odd total elements is the top of the larger heap.
- For even total elements, median is the average of heap tops.

---

**Happy Revising!**
