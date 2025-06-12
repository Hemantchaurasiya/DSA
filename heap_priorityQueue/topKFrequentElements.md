# Top K Frequent Elements

## Problem Statement
Given an integer array `nums` and an integer `k`, return the **k most frequent elements** in the array.

---

## Approach 1: Brute Force (Counting and Sorting)

### **Core Idea**
Count the frequency of each element, then sort the elements based on their frequencies, and pick the top `k`.

### **Algorithm**
1. Traverse the array and build a frequency map: `Map<Integer, Integer>` where key = element, value = frequency.
2. Extract the entries (element, frequency) into a list.
3. Sort the list in descending order based on the frequency.
4. Select the first `k` elements from the sorted list.
5. Return the list of these elements.

### **Java Code**
```java
import java.util.*;

public int[] topKFrequent(int[] nums, int k) {
    // Step 1: Count frequencies
    Map<Integer, Integer> freqMap = new HashMap<>();
    for (int num : nums) {
        freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
    }

    // Step 2: Create a list of entries and sort
    List<Map.Entry<Integer, Integer>> list = new ArrayList<>(freqMap.entrySet());
    list.sort((a, b) -> b.getValue() - a.getValue()); // Descending order

    // Step 3: Collect top k elements
    int[] result = new int[k];
    for (int i = 0; i < k; i++) {
        result[i] = list.get(i).getKey();
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n log n)**, due to sorting the entries based on frequency.
- **Space Complexity:**  
  **O(n)**, for the frequency map and auxiliary list.

### **Dry Run**
| Input: nums = [1,1,1,2,2,3], k=2 |  
|----------------------------------|  
| Count: {1=3, 2=2, 3=1} |  
| Sorted by frequency: [(1,3), (2,2), (3,1)] |  
| Top 2: [1, 2] |  

---

## Approach 2: Using a Min-Heap (Priority Queue)

### **Core Idea**
Maintain a min-heap of size `k` to keep track of the top `k` elements based on their frequency.

### **Algorithm**
1. Count the frequency of each element.
2. Use a min-heap (`PriorityQueue`) that sorts elements by their frequency.
3. Iterate over the entries:
   - Add each entry to the heap.
   - If heap size exceeds `k`, remove the smallest element.
4. The heap now contains the top `k` frequent elements.
5. Extract elements from the heap to form the result.

### **Java Code**
```java
import java.util.*;

public int[] topKFrequent(int[] nums, int k) {
    // Step 1: Count frequencies
    Map<Integer, Integer> freqMap = new HashMap<>();
    for (int num : nums) {
        freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
    }

    // Step 2: Min-heap based on frequency
    PriorityQueue<Map.Entry<Integer, Integer>> minHeap = new PriorityQueue<>(Comparator.comparingInt(Map.Entry::getValue));

    // Step 3: Maintain heap of size k
    for (Map.Entry<Integer, Integer> entry : freqMap.entrySet()) {
        minHeap.offer(entry);
        if (minHeap.size() > k) {
            minHeap.poll();
        }
    }

    // Step 4: Extract top k elements
    int[] result = new int[k];
    int index = 0;
    while (!minHeap.isEmpty()) {
        result[index++] = minHeap.poll().getKey();
    }
    // Optional: reverse the array if needed
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n log k)**, since inserting into the heap takes `O(log k)` and we do it for all entries.
- **Space Complexity:**  
  **O(n)**, for the frequency map and heap.

### **Dry Run**
| Input: nums = [1,1,1,2,2,3], k=2 |  
|----------------------------------|  
| Count: {1=3, 2=2, 3=1} |  
| Insert (1,3): heap=[(1,3)] |  
| Insert (2,2): heap=[(2,2),(1,3)] |  
| Insert (3,1): heap=[(2,2),(1,3)] (after poll) |  
| Result: [2, 1] (order may vary) |  

---

## Approach 3: Bucket Sort (Most Optimal)

### **Core Idea**
Since the maximum frequency of any element cannot exceed the length of the array, we can use bucket sort:
- Create buckets where index = frequency.
- Place elements into buckets based on their count.
- Traverse buckets from highest to lowest to get the top `k`.

### **Algorithm**
1. Count the frequency of each element.
2. Create an array of lists (`List<Integer>[]`) where index represents frequency.
3. For each element:
   - Add it to the bucket corresponding to its frequency.
4. Traverse buckets from high to low:
   - Collect elements until `k` elements are gathered.

### **Java Code**
```java
import java.util.*;

public int[] topKFrequent(int[] nums, int k) {
    // Step 1: Count frequencies
    Map<Integer, Integer> freqMap = new HashMap<>();
    for (int num : nums) {
        freqMap.put(num, freqMap.getOrDefault(num, 0) + 1);
    }

    // Step 2: Initialize buckets
    int n = nums.length;
    List<Integer>[] buckets = new ArrayList[n + 1];
    for (int i = 0; i <= n; i++) {
        buckets[i] = new ArrayList<>();
    }

    // Step 3: Fill buckets
    for (Map.Entry<Integer, Integer> entry : freqMap.entrySet()) {
        buckets[entry.getValue()].add(entry.getKey());
    }

    // Step 4: Gather top k elements from buckets
    List<Integer> resultList = new ArrayList<>();
    for (int i = n; i >= 0 && resultList.size() < k; i--) {
        if (!buckets[i].isEmpty()) {
            resultList.addAll(buckets[i]);
        }
    }

    // Convert list to array
    int[] result = new int[k];
    for (int i = 0; i < k; i++) {
        result[i] = resultList.get(i);
    }
    return result;
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n)**, counting frequencies and placing elements into buckets.
- **Space Complexity:**  
  **O(n)**, for the buckets and the frequency map.

### **Dry Run**
| Input: nums = [1,1,1,2,2,3], k=2 |  
|----------------------------------|  
| Count: {1=3, 2=2, 3=1} |  
| Buckets: index 3 -> [1], index 2 -> [2], index 1 -> [3] |  
| From high to low: pick from index 3 and index 2 |  
| Result: [1, 2] |  

---

## **Summary Table**

| Approach | Core Idea | Time Complexity | Space Complexity | Best Use Case |
|------------|--------------|-------------------|------------------|--------------|
| Brute Force | Count, sort, pick top | O(n log n) | O(n) | Small datasets, simplicity |
| Min-Heap | Keep top `k` with a heap | O(n log k) | O(n) | Larger datasets, when `k` is small |
| Bucket Sort | Count-based bucketing | O(n) | O(n) | Large datasets, optimal efficiency |

---

## Final Tips
- If `k` is small relative to `n`, use **Min-Heap**.
- For maximum efficiency, especially with large input sizes, **Bucket Sort** is ideal.
- Always consider the problem constraints to choose the best approach.

---

**Happy Coding!**
