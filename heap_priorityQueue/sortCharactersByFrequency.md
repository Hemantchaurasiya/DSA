# Sort Characters By Frequency

## Problem Statement
Given a string, sort the characters in descending order based on their frequency of occurrence. Return the resulting string with characters ordered from most frequent to least frequent.

---

## Approach 1: Brute Force (Counting and Sorting)

### **Core Idea**
Count the frequency of each character, then sort the characters by their frequency in descending order, and build the result string accordingly.

### **Algorithm**
1. Count the frequency of each character in the string using a hash map.
2. Extract the characters and their counts into a list of entries.
3. Sort the list based on counts in descending order.
4. Build the result string by appending each character multiplied by its count.
5. Return the final string.

### **Java Code**
```java
import java.util.*;

public String frequencySort(String s) {
    // Step 1: Count frequency of each character
    Map<Character, Integer> freqMap = new HashMap<>();
    for (char c : s.toCharArray()) {
        freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
    }

    // Step 2: Create a list of entries and sort by frequency
    List<Map.Entry<Character, Integer>> list = new ArrayList<>(freqMap.entrySet());
    list.sort((a, b) -> b.getValue() - a.getValue()); // Descending order

    // Step 3: Build the result string
    StringBuilder sb = new StringBuilder();
    for (Map.Entry<Character, Integer> entry : list) {
        char c = entry.getKey();
        int count = entry.getValue();
        for (int i = 0; i < count; i++) {
            sb.append(c);
        }
    }
    return sb.toString();
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n log n)**, due to sorting the list of unique characters, where `n` is the length of the string.
- **Space Complexity:**  
  **O(n)**, for the frequency map and the list of entries.

### **Dry Run**
| Input: "tree" |  
|----------------|  
| Count: {t=1, r=1, e=2} |  
| Sorted by frequency: [(e,2), (t,1), (r,1)] |  
| Result: "ee" + "t" + "r" → "eetr" |  

---

## Approach 2: Using a Max Heap (Priority Queue)

### **Core Idea**
Use a max-heap (priority queue) to order characters by their frequency, then repeatedly extract the most frequent character.

### **Algorithm**
1. Count the frequency of each character.
2. Insert all entries into a max-heap ordered by frequency.
3. Extract the top element from the heap:
   - Append the character multiplied by its count to the result.
   - Repeat until the heap is empty.
4. Return the constructed string.

### **Java Code**
```java
import java.util.*;

public String frequencySort(String s) {
    // Step 1: Count frequency
    Map<Character, Integer> freqMap = new HashMap<>();
    for (char c : s.toCharArray()) {
        freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
    }

    // Step 2: Max-heap based on frequency
    PriorityQueue<Map.Entry<Character, Integer>> maxHeap = new PriorityQueue<>(
        (a, b) -> b.getValue() - a.getValue()
    );

    maxHeap.addAll(freqMap.entrySet());

    // Step 3: Build the result
    StringBuilder sb = new StringBuilder();
    while (!maxHeap.isEmpty()) {
        Map.Entry<Character, Integer> entry = maxHeap.poll();
        char c = entry.getKey();
        int count = entry.getValue();
        for (int i = 0; i < count; i++) {
            sb.append(c);
        }
    }
    return sb.toString();
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n log n)**, due to inserting all characters into the priority queue.
- **Space Complexity:**  
  **O(n)**, for the frequency map and heap.

### **Dry Run**
| Input: "cccaaa" |  
|------------------|  
| Count: {c=3, a=3} |  
| Max-heap: [(c,3), (a,3)] |  
| Extract (c,3): append "ccc" |  
| Extract (a,3): append "aaa" |  
| Result: "cccaaa" |  

---

## Approach 3: Bucket Sort (Most Optimal)

### **Core Idea**
Since the maximum frequency of any character is at most the length of the string, create buckets where index = frequency. Place characters into buckets based on their counts, then iterate from the highest bucket to the lowest to form the result.

### **Algorithm**
1. Count the frequency of each character.
2. Create an array of buckets (`List<Character>[]`) where index = frequency.
3. For each character, add it to the bucket corresponding to its count.
4. Iterate over buckets from high to low frequency:
   - Append each character in the bucket, multiplied by its count, to the result.
5. Return the constructed string.

### **Java Code**
```java
import java.util.*;

public String frequencySort(String s) {
    // Step 1: Count frequency
    Map<Character, Integer> freqMap = new HashMap<>();
    for (char c : s.toCharArray()) {
        freqMap.put(c, freqMap.getOrDefault(c, 0) + 1);
    }

    // Step 2: Initialize buckets
    int n = s.length();
    List<Character>[] buckets = new ArrayList[n + 1];
    for (int i = 0; i <= n; i++) {
        buckets[i] = new ArrayList<>();
    }

    // Step 3: Fill buckets
    for (Map.Entry<Character, Integer> entry : freqMap.entrySet()) {
        buckets[entry.getValue()].add(entry.getKey());
    }

    // Step 4: Build result by iterating buckets from high to low
    StringBuilder result = new StringBuilder();
    for (int i = n; i >= 0; i--) {
        for (char c : buckets[i]) {
            for (int j = 0; j < i; j++) {
                result.append(c);
            }
        }
    }
    return result.toString();
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n)**, counting frequencies and placing characters into buckets.
- **Space Complexity:**  
  **O(n)**, for the buckets and frequency map.

### **Dry Run**
| Input: "aabbcc" |  
|------------------|  
| Count: {a=2, b=2, c=2} |  
| Buckets: index 2 -> [a,b,c], index 1 -> [] |  
| Result: "aabbcc" (or any order of "a", "b", "c" within the bucket) |  

---

## **Summary Table**

| Approach | Core Idea | Time Complexity | Space Complexity | Best Use Case |
|------------|--------------|-------------------|------------------|--------------|
| Brute Force | Count, sort characters | O(n log n) | O(n) | Small strings or simple implementation |
| Max Heap | Use a priority queue to sort by frequency | O(n log n) | O(n) | When you want a priority-based approach |
| Bucket Sort | Use frequency buckets for linear time | O(n) | O(n) | Large strings, optimal performance |

---

## **Final Tips**
- For large input strings, **Bucket Sort** offers the most efficient solution.
- If simplicity is preferred, **Sorting** method is straightforward.
- Use **Priority Queue** when you need a dynamic top-k solution, but for this problem, Bucket Sort is optimal.

---

**Happy Coding!**
