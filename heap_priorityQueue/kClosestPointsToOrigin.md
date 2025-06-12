# K Closest Points to Origin

This guide provides a comprehensive overview of approaches to solve the **K Closest Points to Origin** problem. It covers naive methods, intermediate solutions, and the most optimal approach, including step-by-step algorithms, Java code, complexity analysis, and dry run examples for quick understanding and effective revision.

---

## Problem Overview
- **Input:**
  - An array of points `points[][]`, where each point is represented as `[x, y]`.
  - An integer `k`.
- **Output:**
  - The `k` points closest to the origin `(0,0)` based on Euclidean distance.

- **Note:** The Euclidean distance is given by `√(x² + y²)`. Since comparing distances involves only relative comparisons, we can compare `x² + y²` to avoid floating-point operations.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Calculate and Sort All Distances**

### **Core Idea:**  
Compute the distance of each point from the origin, sort all points based on these distances, and select the first `k` points.

### **Algorithm:**
1. For each point, compute `distanceSquared = x² + y²`.
2. Store points along with their distance in an array or list.
3. Sort the list based on `distanceSquared`.
4. Return the first `k` points from the sorted list.

### **Java Code:**
```java
import java.util.*;

public class KClosestPoints {
    public int[][] kClosest(int[][] points, int k) {
        Arrays.sort(points, Comparator.comparingInt(p -> p[0] * p[0] + p[1] * p[1]));
        return Arrays.copyOfRange(points, 0, k);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(n log n)` due to sorting `n` points.
- **Space Complexity:**  
  `O(1)` or `O(n)` depending on sorting implementation (sorting in-place).

---

### **Dry Run with Sample Input**
| Points | [(1,3), (-2,2), (5,8), (0,1)] | k=2 |
|---------|--------------------------------|-----|
| Distances squared | [10, 8, 89, 1] | |
| Sorted points based on distance | [(0,1), (-2,2), (1,3), (5,8)] | |
| First 2 points | [(0,1), (-2,2)] | |

---

## 2. Better Solution: Max Heap of Size `k`

### **Approach Name:**  
**Maintain a Min-Heap of size `k`**

### **Core Idea:**  
Iterate through all points:
- Maintain a max-heap of size `k` based on the distance.
- For each point:
  - If the heap size < `k`, insert the point.
  - Else compare current point's distance with the heap's maximum (root). If smaller, replace the root.
- At the end, the heap contains the `k` closest points.

### **Algorithm:**
1. Initialize a max-heap (priority queue) that sorts points based on their distance from origin.
2. For each point:
   - Add the point to the heap if size < `k`.
   - Else, compare with the root; if current point is closer, replace the root.
3. After processing all points, the heap contains the `k` closest points.

### **Java Code:**
```java
import java.util.*;

public class KClosestPoints {
    public int[][] kClosest(int[][] points, int k) {
        PriorityQueue<int[]> maxHeap = new PriorityQueue<>((a, b) -> {
            int distA = a[0]*a[0] + a[1]*a[1];
            int distB = b[0]*b[0] + b[1]*b[1];
            return distB - distA; // max heap based on distance
        });

        for (int[] point : points) {
            if (maxHeap.size() < k) {
                maxHeap.offer(point);
            } else {
                int[] farthest = maxHeap.peek();
                int distFarthest = farthest[0]*farthest[0] + farthest[1]*farthest[1];
                int distCurrent = point[0]*point[0] + point[1]*point[1];
                if (distCurrent < distFarthest) {
                    maxHeap.poll();
                    maxHeap.offer(point);
                }
            }
        }

        int[][] result = new int[k][2];
        int index = 0;
        while (!maxHeap.isEmpty()) {
            result[index++] = maxHeap.poll();
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  `O(n log k)` because each insertion/deletion in the heap takes `O(log k)`, done for `n` points.
- **Space Complexity:**  
  `O(k)` for the heap.

---

### **Dry Run with Sample Input**
| Points | [(1,3), (-2,2), (5,8), (0,1)] | k=2 |
|---------|------------------------------|-----|
| Process (1,3): | Heap: [(1,3)] |
| Process (-2,2): | Heap: [(1,3), (-2,2)] | size=k=2, no removal needed |
| Process (5,8): | Distance=64, farthest in heap: (1,3), distance=10 | 64 > 10, ignore |
| Process (0,1): | Distance=1, less than farthest (1,3), remove (1,3) and add (0,1) | Heap: [(-2,2), (0,1)] |
| Final heap points | [(-2,2), (0,1)] |

---

## 3. Most Optimal Solution: Using a Min Heap with Fixed Size `k` (Priority Queue)

### **Core Idea:**  
- Similar to the second approach, but using a min-heap to keep track of the `k` closest points.
- **This approach is often the standard solution** for the problem.

### **Algorithm Summary:**
- Use a min-heap keyed by distance.
- Insert all points into the heap.
- Extract points until only `k` closest remain, or maintain a heap of size `k` by replacing larger distances.

---

## **Summary Table**

| Approach                                | Best Use Case                                | Time Complexity             | Space Complexity | Notes                              |
|----------------------------------------|----------------------------------------------|------------------------------|------------------|-----------------------------------|
| Sorting all points                   | Small datasets                                | `O(n log n)`                | `O(1)` or `O(n)` | Simpler but less efficient for large n |
| Max-heap of size `k`                   | Large datasets, need efficiency             | `O(n log k)`                | `O(k)`           | Efficient for large inputs       |

---

## **Key Takeaways**
- Sorting is simple but less scalable.
- Using a max-heap of size `k` is the most efficient for large datasets.
- Comparing squared distances avoids floating-point inaccuracies.
- Maintaining a fixed-size heap ensures optimal performance.

This completes the detailed revision for **K Closest Points to Origin**, covering multiple approaches with their intuition, implementation, and complexities.
