# Kth Largest Element in a Stream

This guide provides a comprehensive overview of approaches to solve **Kth Largest Element in a Stream** problem, starting from naive methods to the most efficient solutions. It includes the core idea, step-by-step algorithms, Java implementations, complexity analysis, and dry run examples for quick understanding and revision.

---

## Problem Overview
- You are given:
  - An integer `k`.
  - A stream of numbers (initially an array).
- You need to process the stream such that:
  - After each insertion, you can retrieve the **kth largest element** among all elements seen so far.
- The goal is to efficiently support dynamic insertions and retrieval of the kth largest element.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Naive Recalculation**

### **Core Idea:**  
Recompute the entire sorted list of elements each time a new element is added, then pick the kth largest.

### **Algorithm:**
1. Maintain a list of all elements seen so far.
2. When a new element arrives:
   - Insert it into the list.
   - Sort the list in descending order.
   - The kth largest element is at index `k-1`.
3. Return the kth largest element.

### **Java Code:**
```java
import java.util.ArrayList;
import java.util.Collections;

public class KthLargestStream {
    private ArrayList<Integer> list;
    private int k;

    public KthLargestStream(int k, int[] initial) {
        this.k = k;
        list = new ArrayList<>();
        for (int num : initial) {
            list.add(num);
        }
    }

    public int add(int val) {
        list.add(val);
        Collections.sort(list, Collections.reverseOrder());
        return list.get(k - 1);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(n log n)` per insertion, due to sorting the list of all elements after each insertion.
- **Space Complexity:**  
  `O(n)` for storing all elements.

---

### **Dry Run with Sample Input**
| Initial Array | [4, 5, 8, 2], k=3 |
|----------------|------------------|
| Insert 3: | List becomes [4, 5, 8, 2, 3], sort: [8, 5, 4, 3, 2], 3rd largest=4 |
| Insert 10: | List: [8, 5, 4, 3, 2, 10], sort: [10, 8, 5, 4, 3, 2], 3rd largest=5 |

---

## 2. Better Solution: Min Heap of Size `k`

### **Approach Name:**  
**Heap-Based Optimization**

### **Core Idea:**  
Maintain a min-heap of size `k` containing the top `k` elements seen so far.  
- The root of the min-heap is the **kth largest** element.
- When a new element arrives:
  - If the heap size is less than `k`, insert the element.
  - If the new element is larger than the root, replace the root with the new element.
- The root always represents the kth largest element.

### **Algorithm:**
1. Initialize a min-heap.
2. Insert the first `k` elements from the initial array into the heap.
3. For each subsequent element:
   - If it is larger than the heap's root, replace the root with this element.
4. After each insertion, the root of the heap is the kth largest element.

### **Java Code:**
```java
import java.util.PriorityQueue;

public class KthLargestStream {
    private PriorityQueue<Integer> minHeap;
    private int k;

    public KthLargestStream(int k, int[] initial) {
        this.k = k;
        minHeap = new PriorityQueue<>();
        for (int num : initial) {
            add(num);
        }
    }

    public int add(int val) {
        if (minHeap.size() < k) {
            minHeap.offer(val);
        } else if (val > minHeap.peek()) {
            minHeap.poll();
            minHeap.offer(val);
        }
        return minHeap.peek();
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(log k)` per insertion, since each `add()` involves heap operations of size at most `k`.
- **Space Complexity:**  
  `O(k)` for maintaining the heap.

---

### **Dry Run with Sample Input**
| Initial Array | [4, 5, 8, 2], k=3 |
|----------------|------------------|
| Insert 4: | Heap: [4] |
| Insert 5: | Heap: [4,5] |
| Insert 8: | Heap: [4,5,8], root=4 (3rd largest) |
| Insert 2: | 2<4, ignore; root=4 |
| Insert 10: | 10>4, replace 4 with 10, heap: [5,8,10], root=5 (3rd largest) |

---

## 3. Most Efficient Solution: Max Heap + Size `k`

### **Core Idea:**  
- Use a **min-heap of size `k`** as above.
- **The root always holds the kth largest element**.
- This approach ensures quick insertions and retrievals after initial setup.

### **Summary:**
- Initialize the min-heap with the first `k` elements.
- For each new element:
  - If larger than the heap's root, replace the root.
- The root is the answer for the kth largest after each insertion.

---

## **Summary Table**

| Approach                           | Best Use Case                                | Time Complexity                     | Space Complexity | Notes                                           |
|-----------------------------------|----------------------------------------------|-------------------------------------|------------------|------------------------------------------------|
| Naive sorting after each insertion | Very small datasets                         | `O(n log n)` per insertion          | `O(n)`           | Not scalable for large streams                |
| Min Heap with size `k`             | Large streams with frequent updates        | `O(log k)` per insertion            | `O(k)`           | Efficient for large `k` and frequent updates |

---

## **Key Takeaways**
- Brute-force recalculates the entire sorted list each time, inefficient for large streams.
- Using a min heap of size `k` is optimal for dynamic, real-time retrieval of the kth largest element.
- Maintain a fixed-size heap to achieve fast insertions and retrievals.

This concludes the detailed revision for **Kth Largest Element in a Stream**, covering multiple approaches with their intuition, implementation, and complexities.
