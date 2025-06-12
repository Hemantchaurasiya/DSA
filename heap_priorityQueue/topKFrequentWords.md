# Top K Frequent Elements

This guide provides a comprehensive overview of solving the **"Top K Frequent Elements"** problem, covering various approaches from brute-force to the most optimized solutions. Designed for quick revision and interview prep, each approach includes the core idea, algorithm, Java implementation, complexity analysis, and a dry run example.

---

## Problem Statement
Given an integer array `nums` and an integer `k`, return the `k` most frequent elements.

---

## Approach 1: Brute Force

### **Core Idea:**
Count the frequency of each element, then find the top `k` elements by repeatedly selecting the maximum frequency element.

---

### **Algorithm:**
1. Count the frequency of each element using a `HashMap`.
2. For `k` times:
   - Find the element with the highest frequency.
   - Add it to the result.
   - Remove that element from consideration (or set its frequency to 0).

---

### **Java Code:**
```java
import java.util.*;

public class TopKFrequentBruteForce {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums) {
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
        }

        int[] result = new int[k];

        for (int i = 0; i < k; i++) {
            int maxFreq = -1;
            int maxFreqNum = -1;
            for (Map.Entry<Integer, Integer> entry : freqMap.entrySet()) {
                if (entry.getValue() > maxFreq) {
                    maxFreq = entry.getValue();
                    maxFreqNum = entry.getKey();
                }
            }
            result[i] = maxFreqNum;
            freqMap.remove(maxFreqNum);
        }

        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - Counting frequencies: `O(N)`  
  - Finding top element `k` times: `O(k * N)` (each time scanning the map)  
  - Overall: **O(k * N)**

- **Space Complexity:**  
  - `HashMap` for frequency: `O(N)`  
  - Result array: `O(k)`

---

### **Dry Run (Example):**
- Input: `nums = [1,1,1,2,2,3]`, `k = 2`

| Step | freqMap                                | Max element found | Result array | Updated freqMap             |
|-------|----------------------------------------|---------------------|----------------|------------------------------|
| 1     | {1=3, 2=2, 3=1}                       | 1                   | []             | (no change)                 |
| 2     | {2=2, 3=1}                            | 2                   | [1]            | remove 1: {2=2, 3=1}        |

Output: `[1, 2]`

---

## Approach 2: Better Solution Using Sorting

### **Core Idea:**
Count frequencies, then sort elements based on their frequency in descending order, and pick the top `k`.

---

### **Algorithm:**
1. Count the frequency of each element with a `HashMap`.
2. Convert the map entries into a list.
3. Sort the list based on frequency in descending order.
4. Take the first `k` elements from the sorted list.

---

### **Java Code:**
```java
import java.util.*;

public class TopKFrequentSorting {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums)
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);

        List<Map.Entry<Integer, Integer>> list = new ArrayList<>(freqMap.entrySet());

        list.sort((a, b) -> b.getValue() - a.getValue()); // sort by frequency descending

        int[] result = new int[k];
        for (int i = 0; i < k; i++) {
            result[i] = list.get(i).getKey();
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - Counting frequencies: `O(N)`  
  - Sorting entries: `O(M log M)`, where `M` is the number of unique elements (≤ N)  
  - Selecting top `k`: `O(k)`  
  - Overall: **O(N log N)** in worst case (all elements unique)

- **Space Complexity:**  
  - `HashMap`: `O(N)`  
  - List for sorting: `O(N)`

---

### **Dry Run (Example):**
- Input: `nums = [1,1,1,2,2,3]`, `k=2`

| Step | freqMap                     | Sorted list (by freq)        | Result array |  
|-------|------------------------------|------------------------------|--------------|  
| 1     | {1=3, 2=2, 3=1}             | [(1,3), (2,2), (3,1)]        | []           |  
| 2     |                              | After sorting: [(1,3), (2,2), (3,1)] | [1,2]       |  

Output: `[1, 2]`

---

## Approach 3: Optimized Solution Using Heap (Priority Queue)

### **Core Idea:**
Use a min-heap (priority queue) of size `k` to keep track of top `k` elements while iterating over the frequency map.

---

### **Algorithm:**
1. Count the frequency of each element with a `HashMap`.
2. Use a **min-heap** (priority queue) to maintain the top `k` elements:
   - For each entry in the frequency map:
     - Add the entry to the heap.
     - If heap size exceeds `k`, remove the smallest frequency element.
3. The heap contains the `k` most frequent elements.
4. Extract elements from the heap to form the result.

---

### **Java Code:**
```java
import java.util.*;

public class TopKFrequentHeap {
    public int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freqMap = new HashMap<>();
        for (int num : nums)
            freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);

        PriorityQueue<Map.Entry<Integer, Integer>> minHeap = new PriorityQueue<>(
            (a, b) -> a.getValue() - b.getValue()
        );

        for (Map.Entry<Integer, Integer> entry : freqMap.entrySet()) {
            minHeap.offer(entry);
            if (minHeap.size() > k)
                minHeap.poll();
        }

        int[] result = new int[k];
        int index = 0;
        while (!minHeap.isEmpty()) {
            result[index++] = minHeap.poll().getKey();
        }

        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - Counting frequencies: `O(N)`  
  - Heap operations: `O(M log k)`, where `M` is the number of unique elements (≤ N)  
  - Overall: **O(N log k)**

- **Space Complexity:**  
  - `HashMap`: `O(N)`  
  - Heap: `O(k)`  

---

### **Dry Run (Example):**
- Input: `nums = [1,1,1,2,2,3]`, `k=2`

| Step | freqMap                     | Heap content (after each insertion)                      | Result after extraction |  
|-------|------------------------------|-----------------------------------------------------------|------------------------|  
| 1     | {1=3, 2=2, 3=1}             | Insert (1,3): [(1,3)]                                     |                        |  
| 2     |                              | Insert (2,2): [(2,2), (1,3)] (size=2, k=2)             |                        |  
| 3     |                              | Insert (3,1): [(2,2), (1,3), (3,1)] → remove smallest: (3,1) |            |  
| Final |                              | Heap: [(2,2), (1,3)] (top elements)                     | [1, 2]                 |  

Output: `[1, 2]`

---

## **Summary Table**

| Approach                   | Data Structures Used             | Time Complexity      | Space Complexity    | Best Use Case                          |
|----------------------------|----------------------------------|----------------------|---------------------|----------------------------------------|
| Brute Force                | HashMap + Linear Search          | O(k * N)            | O(N)                | Small input sizes, simplicity          |
| Sorting                    | HashMap + List + Sorting        | O(N log N)          | O(N)                | Moderate input sizes                   |
| Heap (Priority Queue)      | HashMap + Min-Heap             | O(N log k)          | O(N + k)            | Large inputs, when k is small         |

---

## Final Tips:
- For large `N` and small `k`, **Heap** approach is most efficient.
- When `k` is close to `N`, sorting may suffice.
- Always consider the constraints for optimal approach selection.

---

**End of Revision Note**
