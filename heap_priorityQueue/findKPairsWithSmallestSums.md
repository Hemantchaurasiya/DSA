# Find K Pairs with Smallest Sums

This problem involves finding **k** pairs `(a, b)` from two sorted arrays such that the pairs have the **smallest sums**. It is a common problem that can be approached via brute-force to optimized solutions using heaps.

---

## Problem Definition:
Given two sorted arrays `nums1` and `nums2`, find the `k` pairs `(u, v)` with the smallest sum, where `u` is from `nums1` and `v` is from `nums2`.

---

## Approach 1: Brute Force - Generate All Pairs and Sort

### **Core Idea:**
Generate all possible pairs, calculate their sums, and select the smallest `k` pairs.

### **Algorithm:**
1. Iterate through every element in `nums1`.
2. For each element in `nums1`, iterate through every element in `nums2`.
3. Store each pair `(nums1[i], nums2[j])` along with their sum.
4. Sort all pairs based on their sum.
5. Select the first `k` pairs from the sorted list.
6. Return these pairs.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<int[]> allPairs = new ArrayList<>();
        for (int a : nums1) {
            for (int b : nums2) {
                allPairs.add(new int[]{a, b});
            }
        }
        allPairs.sort(Comparator.comparingInt(p -> p[0] + p[1]));

        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < Math.min(k, allPairs.size()); i++) {
            result.add(Arrays.asList(allPairs.get(i)[0], allPairs.get(i)[1]));
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(M * N * log(M * N))`, where `M` and `N` are the lengths of `nums1` and `nums2`, respectively, due to generating all pairs and sorting.
- **Space Complexity:** `O(M * N)` for storing all pairs.

### **Dry Run:**
Suppose:
```plaintext
nums1 = [1, 7, 11]
nums2 = [2, 4, 6]
k = 3
```
- Generate pairs:
  `(1,2)`, `(1,4)`, `(1,6)`, `(7,2)`, `(7,4)`, `(7,6)`, `(11,2)`, `(11,4)`, `(11,6)`
- Sums: 3, 5, 7, 9, 11, 13, 13, 15, 17
- Sorted pairs: `(1,2)`, `(1,4)`, `(7,2)`, ...
- Result: `[[1, 2], [1, 4], [7, 2]]`

---

## Approach 2: Min-Heap (Priority Queue) for K Smallest Pairs (Most Efficient)

### **Core Idea:**
Use a min-heap to efficiently select the next smallest pair without generating all pairs upfront.

### **Algorithm:**
1. Initialize a min-heap that stores pairs `(nums1[i], nums2[j])` along with their sum.
2. Insert the first pair `(nums1[0], nums2[0])` from each `nums1[i]` with `nums2[0]`.
   - To avoid duplicates, only insert pairs `(i, 0)` initially.
3. While the heap is not empty and we haven't collected `k` pairs:
   - Extract the minimum pair `(a, b)`.
   - Add this pair to the result.
   - If possible, insert the next pair `(i, j+1)` (i.e., move in `nums2`) into the heap.
4. Return the collected pairs.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return result;

        PriorityQueue<Pair> minHeap = new PriorityQueue<>(Comparator.comparingInt(p -> p.sum));

        // Initialize heap with pairs (i, 0)
        for (int i = 0; i < Math.min(nums1.length, k); i++) {
            minHeap.offer(new Pair(i, 0, nums1[i] + nums2[0]));
        }

        while (!minHeap.isEmpty() && result.size() < k) {
            Pair current = minHeap.poll();
            int i = current.i;
            int j = current.j;
            result.add(Arrays.asList(nums1[i], nums2[j]));
            if (j + 1 < nums2.length) {
                minHeap.offer(new Pair(i, j + 1, nums1[i] + nums2[j + 1]));
            }
        }
        return result;
    }

    static class Pair {
        int i, j, sum;
        Pair(int i, int j, int sum) {
            this.i = i;
            this.j = j;
            this.sum = sum;
        }
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(k log min(M, N))`, because each insertion/deletion in heap is `O(log min(M, N))`, and we do at most `k` such operations.
- **Space Complexity:** `O(min(M, N))` for the heap.

### **Dry Run:**
Suppose:
```plaintext
nums1 = [1, 7, 11]
nums2 = [2, 4, 6]
k = 3
```
- Initialize heap with `(0,0,3)`: `(1+2)=3`.
- Extract `(0,0)`: add `[1, 2]`, insert `(0,1)` with sum `1+4=5`.
- Extract `(0,1)`: add `[1, 4]`, insert `(0,2)` with sum `1+6=7`.
- Extract `(0,2)`: add `[1, 6]`.
- Result: `[[1, 2], [1, 4], [1, 6]]`.

---

## **Summary Table**

| Approach | Best For | Time Complexity | Space Complexity | Notes |
|--------------|------------|------------------|------------------|--------|
| Generate All & Sort | Small arrays | `O(M * N * log(M * N))` | `O(M * N)` | Simple but inefficient for large inputs |
| Min-Heap (Optimal) | Large arrays | `O(k log min(M, N))` | `O(min(M, N))` | Efficient and scalable |

---

## **Final Tips:**
- Use **brute-force** only for small input sizes.
- Use **min-heap** for large inputs to optimize performance.
- Limit the initial heap insertions to the minimum of `k` and array lengths to reduce overhead.
- Always verify edge cases such as empty input arrays or `k` being larger than total possible pairs.

---

**Happy coding!**
