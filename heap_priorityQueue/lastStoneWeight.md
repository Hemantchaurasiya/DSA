# Last Stone Weight

This guide details multiple approaches to solve the **Last Stone Weight** problem, progressing from naive to optimal solutions. It includes the core idea, step-by-step algorithms, Java implementations, complexity analysis, and dry run examples for effective understanding and quick revision.

---

## Problem Overview
Given an array of stones where each stone's weight is a positive integer, repeatedly select the two heaviest stones, smash them together, and replace them according to the rules:
- If the two stones have equal weight, both are destroyed.
- If they have different weights, the smaller stone is destroyed, and the larger stone's weight reduces by the smaller's weight.
Continue until at most one stone remains. Return its weight or 0 if no stones remain.

---

## 1. Brute Force Approach

### **Core Idea**
Simulate the process by repeatedly selecting the two largest stones, smashing them, and updating the list until one or zero stones remain.

### **Algorithm**
1. While the list has more than one stone:
   - Find the two largest stones.
   - Remove them from the list.
   - If they are equal, both are destroyed.
   - If not, insert the difference back into the list.
2. Return the remaining stone's weight or 0 if none.

### **Java Code**
```java
import java.util.ArrayList;
import java.util.Collections;

public class LastStoneWeight {
    public int lastStoneWeight(int[] stones) {
        ArrayList<Integer> list = new ArrayList<>();
        for (int stone : stones) {
            list.add(stone);
        }

        while (list.size() > 1) {
            Collections.sort(list);
            int y = list.remove(list.size() - 1); // largest
            int x = list.remove(list.size() - 1); // second largest
            if (x != y) {
                list.add(y - x);
            }
        }

        return list.isEmpty() ? 0 : list.get(0);
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(n^3 log n)` in the worst case due to sorting in every iteration (`O(n log n)` per iteration, and up to `n` iterations).
- **Space Complexity:**  
  `O(n)` for the list storage.

---

### **Dry Run with Sample Input**
| Initial stones | [2, 7, 4, 1, 8, 1] |
|------------------|--------------------|
| Sorted list     | [1, 1, 2, 4, 7, 8] |
| Remove top two (8,7) | Smash: 8-7=1, list: [1, 1, 2, 4, 1] |
| Sorted list     | [1, 1, 1, 2, 4] |
| Remove top two (4,2) | Smash: 4-2=2, list: [1, 1, 1, 2] |
| Sorted list     | [1, 1, 1, 2] |
| Remove (2,1) | Smash: 2-1=1, list: [1, 1, 1] |
| Sorted list     | [1, 1, 1] |
| Remove (1,1) | Smash: 1-1=0, discard both, list: [1] |
| Only one stone left | 1 |

---

## 2. Better Solution: Max Heap (Priority Queue)

### **Core Idea**
Use a max heap to efficiently retrieve the two largest stones at each step, reducing the time complexity.

### **Algorithm**
1. Insert all stones into a max-heap.
2. While the heap has more than one stone:
   - Extract the two largest stones.
   - If they are different, insert their difference back into the heap.
3. Return the remaining stone's weight or 0 if empty.

### **Java Code**
```java
import java.util.PriorityQueue;

public class LastStoneWeight {
    public int lastStoneWeight(int[] stones) {
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>((a, b) -> b - a);
        for (int stone : stones) {
            maxHeap.offer(stone);
        }

        while (maxHeap.size() > 1) {
            int y = maxHeap.poll(); // largest
            int x = maxHeap.poll(); // second largest
            if (x != y) {
                maxHeap.offer(y - x);
            }
        }

        return maxHeap.isEmpty() ? 0 : maxHeap.peek();
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(n log n)` due to inserting all stones (`O(n)`) and up to `n-1` extract/insert operations (`O(log n)` each).
- **Space Complexity:**  
  `O(n)` for the heap storage.

---

### **Dry Run with Sample Input**
| Initial stones | [2, 7, 4, 1, 8, 1] |
|------------------|--------------------|
| Max heap after insertion | [8, 7, 4, 1, 1, 2] |
| Extract (8,7), smash: 1, insert 1 | Heap: [4, 2, 1, 1, 1] |
| Extract (4,2), smash: 2, insert 2 | Heap: [2, 1, 1, 1] |
| Extract (2,1), smash: 1, insert 1 | Heap: [1, 1, 1] |
| Extract (1,1), smash: 0, discard | Heap: [1] |
| Remaining stone: 1 | - |

---

## 3. Most Optimal Solution: **Max Heap + Early Termination**

### **Core Idea**
The max-heap approach is already optimal in average `O(n log n)` time, which is suitable for most constraints. For further optimization, if input sizes are large, consider heuristics or early termination checks (not always necessary).

### **Summary**
- Use a max heap for efficient retrieval of largest stones.
- Continue until one or zero stones remain.
- Return the last stone or 0.

---

## **Summary Table**

| Approach                | Best Use Case               | Time Complexity      | Space Complexity | Notes                                         |
|-------------------------|------------------------------|----------------------|------------------|----------------------------------------------|
| Brute Force            | Small datasets               | `O(n^3 log n)`       | `O(n)`           | Inefficient for large input sizes          |
| Max Heap (Priority Queue) | Most practical, scalable    | `O(n log n)`        | `O(n)`           | Efficient and straightforward implementation |

---

This revision covers all major methods to solve **Last Stone Weight**, highlighting their intuition, implementation, and complexity, enabling quick understanding and effective preparation for interviews.
