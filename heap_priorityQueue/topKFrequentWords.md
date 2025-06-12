# Top K Frequent Words

This comprehensive guide covers multiple approaches to solving the **"Top K Frequent Words"** problem, starting from brute-force to the most efficient solutions. Each approach includes the core idea, algorithm steps, Java implementation, complexity analysis, and a dry run example for quick and effective revision suited for interviews.

---

## Problem Statement
Given a list of words, return the `k` most frequent words. If two words have the same frequency, the word with the smaller lexicographical order should come first.

---

## Approach 1: Brute Force

### **Core Idea:**
Count the frequency of each word, then repeatedly select the word with the highest frequency, resolving ties using lex order.

---

### **Algorithm:**
1. Count the frequency of each word using a `HashMap`.
2. Initialize an empty list for results.
3. For `k` times:
   - Find the word with the highest frequency (if tie, lex order).
   - Add that word to the result.
   - Remove it from consideration (or reduce count).

---

### **Java Code:**
```java
import java.util.*;

public class TopKFrequentWordsBruteForce {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> freqMap = new HashMap<>();
        for (String word : words) {
            freqMap.put(word, freqMap.getOrDefault(word, 0) + 1);
        }
        List<String> result = new ArrayList<>();

        for (int i = 0; i < k; i++) {
            String maxWord = null;
            int maxFreq = -1;
            for (Map.Entry<String, Integer> entry : freqMap.entrySet()) {
                if (entry.getValue() > maxFreq || 
                    (entry.getValue() == maxFreq && (maxWord == null || entry.getKey().compareTo(maxWord) < 0))) {
                    maxFreq = entry.getValue();
                    maxWord = entry.getKey();
                }
            }
            result.add(maxWord);
            freqMap.remove(maxWord);
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - Counting frequencies: `O(N)`  
  - For each of the `k` iterations, scanning entire `HashMap`: `O(N)`  
  - Total: **O(k * N)**

- **Space Complexity:**  
  - `HashMap`: `O(N)`  
  - Result list: `O(k)`

---

### **Dry Run (Example):**
- Input: `words = ["i", "love", "leetcode", "i", "love", "coding"]`, `k=2`

| Step | freqMap                                    | Max word found | Result list | Updated freqMap                        |
|-------|--------------------------------------------|----------------|--------------|----------------------------------------|
| 1     | {i=2, love=2, leetcode=1, coding=1}       | "i" (tie-breaker) | []           | (remainder after removing "i")       |
| 2     | {love=2, leetcode=1, coding=1}             | "love"        | ["i"]        | (remainder after removing "love")    |

Output: `["i", "love"]`

---

## Approach 2: Sorting

### **Core Idea:**
Count the frequencies, then sort the words first by decreasing frequency, then lex order for ties.

---

### **Algorithm:**
1. Count word frequencies with a `HashMap`.
2. Convert the map entries into a list.
3. Sort the list:
   - Primary key: frequency (descending)
   - Secondary key: lex order (ascending)
4. Select the first `k` words from the sorted list.

---

### **Java Code:**
```java
import java.util.*;

public class TopKFrequentWordsSorting {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> freqMap = new HashMap<>();
        for (String word : words)
            freqMap.put(word, freqMap.getOrDefault(word, 0) + 1);

        List<Map.Entry<String, Integer>> list = new ArrayList<>(freqMap.entrySet());

        list.sort((a, b) -> {
            if (b.getValue().equals(a.getValue())) {
                return a.getKey().compareTo(b.getKey());
            } else {
                return b.getValue() - a.getValue();
            }
        });

        List<String> result = new ArrayList<>();
        for (int i = 0; i < k; i++) {
            result.add(list.get(i).getKey());
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - Counting frequencies: `O(N)`  
  - Sorting entries: `O(M log M)`, where `M` is the number of unique words (`≤ N`)  
  - Selecting top `k`: `O(k)`  
  - Overall: **O(N log N)** in worst case

- **Space Complexity:**  
  - `HashMap`: `O(N)`  
  - List of entries: `O(N)`

---

### **Dry Run (Example):**
- Input: `words = ["i", "love", "leetcode", "i", "love", "coding"]`, `k=2`

| Step | freqMap                                    | Sorted list (by freq, lex order)                       | Result list |  
|-------|--------------------------------------------|---------------------------------------------------------|--------------|  
| 1     | {i=2, love=2, leetcode=1, coding=1}       | [(i,2), (love,2), (coding,1), (leetcode,1)]             | []           |  
| 2     |                                              | Sorted by freq then lex: [(i,2), (love,2), (coding,1), (leetcode,1)] | ["i", "love"] |  

Output: `["i", "love"]`

---

## Approach 3: Using a Heap (Priority Queue)

### **Core Idea:**
Use a min-heap to keep track of top `k` words based on frequency and lex order, efficiently handling large input.

---

### **Algorithm:**
1. Count word frequencies with a `HashMap`.
2. Use a **priority queue** (min-heap) with a custom comparator:
   - Compare by frequency ascending
   - If tie, compare lex order descending (to pop the smaller lex word last)
3. For each entry:
   - Add to heap
   - If size exceeds `k`, remove the smallest (top of min-heap)
4. Extract elements from the heap into a list, reversing order to get most frequent first.

---

### **Java Code:**
```java
import java.util.*;

public class TopKFrequentWordsHeap {
    public List<String> topKFrequent(String[] words, int k) {
        Map<String, Integer> freqMap = new HashMap<>();
        for (String word : words)
            freqMap.put(word, freqMap.getOrDefault(word, 0) + 1);

        PriorityQueue<Map.Entry<String, Integer>> minHeap = new PriorityQueue<>(
            (a, b) -> {
                if (a.getValue().equals(b.getValue())) {
                    return b.getKey().compareTo(a.getKey()); // reverse lex order for tie
                } else {
                    return a.getValue() - b.getValue();
                }
            }
        );

        for (Map.Entry<String, Integer> entry : freqMap.entrySet()) {
            minHeap.offer(entry);
            if (minHeap.size() > k)
                minHeap.poll();
        }

        List<String> result = new LinkedList<>();
        while (!minHeap.isEmpty()) {
            result.add(0, minHeap.poll().getKey()); // insert at front for correct order
        }
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - Counting frequencies: `O(N)`  
  - Heap operations: `O(M log k)` (M = number of unique words)  
  - Overall: **O(N log k)**

- **Space Complexity:**  
  - `HashMap`: `O(N)`  
  - Heap: `O(k)`

---

### **Dry Run (Example):**
- Input: `words = ["i", "love", "leetcode", "i", "love", "coding"]`, `k=2`

| Step | freqMap                                    | Heap contents after each insertion                        | Final extraction                          |  
|-------|--------------------------------------------|------------------------------------------------------------|-------------------------------------------|  
| 1     | {i=2, love=2, leetcode=1, coding=1}       | Insert ("i",2): [(i,2)]                                   |                                           |  
| 2     |                                              | Insert ("love",2): [(i,2), (love,2)]                       |                                           |  
| 3     |                                              | Insert ("leetcode",1): [(i,2), (love,2), (leetcode,1)] → remove smallest |        |  
| 4     |                                              | Insert ("coding",1): same process                          |                                           |  
| Final |                                              | Heap contains top 2 words with highest frequency and lex order | ["i", "love"] |

Output: `["i", "love"]`

---

## **Summary Table**

| Approach                   | Data Structures Used                  | Time Complexity      | Space Complexity    | When to Use                                |
|----------------------------|---------------------------------------|----------------------|---------------------|--------------------------------------------|
| Brute Force                | HashMap + Linear search               | O(k * N)            | O(N)                | Small datasets, simple implementation      |
| Sorting                    | HashMap + List + Sorting              | O(N log N)          | O(N)                | Moderate datasets or when sorting is acceptable |
| Heap (Priority Queue)      | HashMap + Min-Heap                    | O(N log k)          | O(N + k)            | Large datasets, small k, performance critical |

---

## Final Tips:
- For large inputs with small `k`, **Heap** is optimal.
- When `k` approaches the size of the dataset, **sorting** might be simpler.
- Always consider lexicographical order tie-breaks.

---

**End of Revision Note**
