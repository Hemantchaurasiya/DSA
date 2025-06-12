# Minimum Cost to Connect Sticks

This guide provides a detailed overview of approaches to solve the **Minimum Cost to Connect Sticks** problem. It covers naive, intermediate, and optimal solutions, including explanations, Java implementations, complexity analyses, and dry runs for quick revision and interview preparation.

---

## Problem Overview
- **Input:** An array of integers representing sticks' lengths.
- **Output:** The minimum total cost to connect all sticks into one, where the cost to connect two sticks is the sum of their lengths.
- **Goal:** Minimize total connection cost.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Repeatedly connect the two smallest sticks until one remains**

### **Core Idea:**  
Always connect the two shortest sticks to minimize incremental cost, repeating until only one stick remains.

### **Algorithm:**
1. While more than one stick remains:
   - Find the two smallest sticks.
   - Connect them, adding the sum to total cost.
   - Remove the two sticks and add their sum back into the list.
2. Return the accumulated total cost.

### **Java Code:**
```java
import java.util.*;

public class MinCostToConnectSticks {
    public int connectSticks(int[] sticks) {
        List<Integer> list = new ArrayList<>();
        for (int stick : sticks) {
            list.add(stick);
        }
        int totalCost = 0;
        while (list.size() > 1) {
            Collections.sort(list);
            int first = list.remove(0);
            int second = list.remove(0);
            int cost = first + second;
            totalCost += cost;
            list.add(cost);
        }
        return totalCost;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(n^2 log n)` due to repeated sorting for each connection.
- **Space Complexity:**  
  `O(n)` for storing the sticks in a list.

---

### **Dry Run with Sample Input**
| Input | [2, 4, 3] |
|--------|------------|
| Step 1 | Sort: [2, 3, 4] |
| Connect 2 & 3 | Cost = 5; List after: [4, 5] |
| Connect 4 & 5 | Cost = 9; Total = 5 + 9 = 14 |
| Result | 14 |

---

## 2. Better Solution: Min-Heap (Priority Queue)

### **Approach Name:**  
**Use a Min-Heap to efficiently get the two smallest sticks**

### **Core Idea:**  
Use a min-heap to always extract the two smallest sticks efficiently, reducing the overall time complexity.

### **Algorithm:**
1. Insert all stick lengths into a min-heap.
2. Initialize total cost = 0.
3. While the heap size > 1:
   - Extract the two smallest sticks.
   - Sum them and add to total cost.
   - Insert the sum back into the heap.
4. Return total cost.

### **Java Code:**
```java
import java.util.*;

public class MinCostToConnectSticks {
    public int connectSticks(int[] sticks) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int stick : sticks) {
            minHeap.offer(stick);
        }

        int totalCost = 0;
        while (minHeap.size() > 1) {
            int first = minHeap.poll();
            int second = minHeap.poll();
            int cost = first + second;
            totalCost += cost;
            minHeap.offer(cost);
        }
        return totalCost;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(n log n)` due to heap insertions and extractions.
- **Space Complexity:**  
  `O(n)` for the heap.

---

### **Dry Run with Sample Input**
| Input | [2, 4, 3] |
|--------|------------|
| Initial heap | [2, 3, 4] |
| Extract 2 & 3 | Cost=5; Total=5; Heap=[4, 5] |
| Extract 4 & 5 | Cost=9; Total=14; Heap=[9] |
| Result | 14 |

---

## 3. Most Optimal Solution: Greedy with Min-Heap

### **Approach Name:**  
**Efficient greedy connection using min-heap**

### **Core Idea:**  
The min-heap method is already optimal in terms of greedy strategy for minimal total cost, leveraging efficient extraction of smallest sticks.

### **Algorithm:**  
Same as approach 2 (since it is the most efficient and optimal):

1. Build a min-heap with all sticks.
2. Repeatedly connect the two smallest sticks, accumulate cost.
3. Insert the new stick (sum) back into the heap until only one remains.
4. Return total cost.

---

### **Java Code (Same as Approach 2):**
```java
import java.util.*;

public class MinCostToConnectSticks {
    public int connectSticks(int[] sticks) {
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        for (int stick : sticks) {
            minHeap.offer(stick);
        }

        int totalCost = 0;
        while (minHeap.size() > 1) {
            int first = minHeap.poll();
            int second = minHeap.poll();
            int cost = first + second;
            totalCost += cost;
            minHeap.offer(cost);
        }
        return totalCost;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(n log n)` — dominated by heap operations.
- **Space Complexity:**  
  `O(n)` for the heap.

---

## **Summary Table**

| Approach                                   | Use Case                          | Time Complexity        | Space Complexity | Key Insights                                   |
|--------------------------------------------|----------------------------------|------------------------|------------------|------------------------------------------------|
| Repeated Sorting                          | Small datasets                   | `O(n^2 log n)`        | `O(n)`           | Inefficient for large input                   |
| Min-Heap (Priority Queue)                  | Large datasets, efficient access| `O(n log n)`         | `O(n)`           | Efficient, greedy solution                     |
| Optimal (Heap-based greedy)                | Large, real-world datasets      | `O(n log n)`         | `O(n)`           | Most efficient, standard solution             |

---

## **Key Takeaways**
- Use a min-heap (priority queue) for efficient extraction of smallest sticks.
- The greedy approach ensures minimal total cost.
- Sorting-based solutions are less efficient but easier to implement for small data.
- The heap approach is optimal for large input sizes.

This completes the detailed revision for **Minimum Cost to Connect Sticks**, covering approaches, algorithms, implementations, and complexities.
