# LFU Cache

This guide provides a comprehensive overview of the **Least Frequently Used (LFU) Cache** problem — a cache eviction policy that removes the least frequently accessed items first. We will explore various approaches, from naive to optimal, with detailed explanations, code snippets, and dry runs.

---

## 1. Brute Force Approach

### **Approach Name:**  
**Naive Implementation**

### **Core Idea:**  
Track the frequency of each key. When capacity is exceeded, scan all cache entries to find the one with the lowest frequency (and oldest among those), then evict it.

### **Algorithm:**  
- Maintain a HashMap of key → (value, frequency, timestamp).
- On `get(key)`:
  - If key exists, increment its frequency and update timestamp.
  - Return the value.
- On `put(key, value)`:
  - If key exists, update value, increment frequency, update timestamp.
  - Else, insert new key with frequency=1.
  - If capacity exceeded, scan all entries to find the LFU (least frequency), and among those, the oldest. Remove it.

### **Java Code:**

```java
import java.util.*;

class LFUCache {
    private class Entry {
        int key, value, freq, timestamp;
        Entry(int key, int value, int freq, int timestamp) {
            this.key = key;
            this.value = value;
            this.freq = freq;
            this.timestamp = timestamp;
        }
    }

    private final int capacity;
    private Map<Integer, Entry> cache = new HashMap<>();
    private int time = 0;

    public LFUCache(int capacity) {
        this.capacity = capacity;
    }

    public int get(int key) {
        if (!cache.containsKey(key))
            return -1;
        Entry entry = cache.get(key);
        entry.freq++;
        entry.timestamp = time++;
        return entry.value;
    }

    public void put(int key, int value) {
        if (capacity == 0)
            return;
        if (cache.containsKey(key)) {
            Entry entry = cache.get(key);
            entry.value = value;
            entry.freq++;
            entry.timestamp = time++;
        } else {
            if (cache.size() >= capacity) {
                // Evict LFU
                Entry toRemove = null;
                for (Entry e : cache.values()) {
                    if (toRemove == null || e.freq < toRemove.freq ||
                        (e.freq == toRemove.freq && e.timestamp < toRemove.timestamp)) {
                        toRemove = e;
                    }
                }
                cache.remove(toRemove.key);
            }
            cache.put(key, new Entry(key, value, 1, time++));
        }
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  - `get`: O(1) (hash map lookup)  
  - `put`: O(N) (scanning all entries to find LFU) — inefficient for large cache sizes.
  
- **Space Complexity:** O(capacity), storing cache entries.

### **Dry Run:**

Suppose capacity=2:

Operations:
```
put(1, 10)        // cache: {1: (10, freq=1, timestamp=0)}
put(2, 20)        // cache: {1: (10,1,0), 2:(20,1,1)}
get(1)            // returns 10; freq of 1 becomes 2, timestamp updated
put(3, 30)        // cache is full; evict LFU:
                  // LFU candidates: key 2 (freq=1), key 1 (freq=2)
                  // Evict key 2
                  // cache: {1: (10,2,2), 3:(30,1,3)}
```

---

## 2. Better Solution: HashMap + Frequency Map + Min-Heap

### **Approach Name:**  
**Frequency Buckets with Min-Heap**

### **Core Idea:**  
Group keys by their frequency in a hash map. Keep track of the minimum frequency. When eviction is needed, remove the least frequently used key from the lowest frequency bucket, choosing the oldest among them.

### **Algorithm:**  
- Use a `HashMap<Integer, Node>` to store key → node (value, freq, timestamp).
- Use a `HashMap<Integer, LinkedHashSet<Integer>>` for frequency → keys at that frequency.
- Track `minFreq`.
- **On `get`:**  
  - Increase key's frequency, move key between frequency buckets.
  - Update `minFreq` if needed.
- **On `put`:**  
  - If key exists, update value, increase frequency.
  - Else, insert new key with freq=1, update `minFreq`.
  - If capacity exceeded, remove the oldest key from the `minFreq` bucket.

### **Java Code:**

```java
import java.util.*;

class LFUCache {
    private class Node {
        int key, value, freq;
        long timestamp;
        Node(int key, int value, int freq, long timestamp) {
            this.key = key;
            this.value = value;
            this.freq = freq;
            this.timestamp = timestamp;
        }
    }

    private int capacity;
    private long time = 0;
    private int minFreq = 1;
    private Map<Integer, Node> keyNodeMap = new HashMap<>();
    private Map<Integer, LinkedHashSet<Integer>> freqMap = new HashMap<>();

    public LFUCache(int capacity) {
        this.capacity = capacity;
    }

    public int get(int key) {
        if (!keyNodeMap.containsKey(key))
            return -1;
        Node node = keyNodeMap.get(key);
        updateNode(node);
        return node.value;
    }

    public void put(int key, int value) {
        if (capacity == 0)
            return;

        if (keyNodeMap.containsKey(key)) {
            Node node = keyNodeMap.get(key);
            node.value = value;
            updateNode(node);
        } else {
            if (keyNodeMap.size() >= capacity) {
                // Evict LFU key
                LinkedHashSet<Integer> minFreqSet = freqMap.get(minFreq);
                int evictKey = minFreqSet.iterator().next();
                minFreqSet.remove(evictKey);
                if (minFreqSet.isEmpty())
                    freqMap.remove(minFreq);
                keyNodeMap.remove(evictKey);
            }
            Node newNode = new Node(key, value, 1, time++);
            keyNodeMap.put(key, newNode);
            freqMap.computeIfAbsent(1, k -> new LinkedHashSet<>()).add(key);
            minFreq = 1;
        }
    }

    private void updateNode(Node node) {
        // Remove from current freq set
        int oldFreq = node.freq;
        LinkedHashSet<Integer> set = freqMap.get(oldFreq);
        set.remove(node.key);
        if (set.isEmpty()) {
            freqMap.remove(oldFreq);
            if (minFreq == oldFreq)
                minFreq++;
        }
        // Add to new freq set
        node.freq++;
        freqMap.computeIfAbsent(node.freq, k -> new LinkedHashSet<>()).add(node.key);
        node.timestamp = time++;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  - `get`: O(1) (hash map + linked hash set operations)  
  - `put`: O(1) (hash map + linked hash set operations)  
- **Space Complexity:** O(capacity), for storing all cache entries and frequency buckets.

---

## 3. Most Optimal Solution: HashMap + Frequency List + Double Linked List

### **Approach Name:**  
**O(1) LFU Cache (Using Double Linked List for each frequency)**

### **Core Idea:**  
Maintain a hash map of key to node, and a hash map of frequency to a double linked list of nodes.  
- Each list maintains order of insertion to determine the oldest for eviction.
- Track the minimum frequency.
- On access/update, move node to the next higher frequency list.
- Evict from the list corresponding to `minFreq`.

### **Algorithm:**  
- Use a `HashMap<Integer, Node>` for key → node.
- Use a `HashMap<Integer, DoubleLinkedList>` for frequency → list of nodes.
- Keep track of `minFreq`.
- **On `get`:**  
  - Increase node's frequency, move it to the corresponding list.
  - Update `minFreq` as needed.
- **On `put`:**  
  - If key exists, update value and move node up.
  - Else, create new node at freq=1.
  - Evict from `minFreq` list if capacity exceeded.

### **Implementation:**

```java
import java.util.*;

class LFUCache {
    private class Node {
        int key, value, freq;
        Node prev, next;
        Node(int key, int value) {
            this.key = key;
            this.value = value;
            this.freq = 1;
        }
    }

    private class DLinkedList {
        Node head, tail;
        DLinkedList() {
            head = new Node(0, 0);
            tail = new Node(0, 0);
            head.next = tail;
            tail.prev = head;
        }

        void addNode(Node node) {
            node.next = head.next;
            head.next.prev = node;
            head.next = node;
            node.prev = head;
        }

        void removeNode(Node node) {
            node.prev.next = node.next;
            node.next.prev = node.prev;
        }

        boolean isEmpty() {
            return head.next == tail;
        }

        Node removeLast() {
            if (isEmpty())
                return null;
            Node last = tail.prev;
            removeNode(last);
            return last;
        }
    }

    private int capacity, minFreq;
    private Map<Integer, Node> keyNodeMap = new HashMap<>();
    private Map<Integer, DLinkedList> freqMap = new HashMap<>();

    public LFUCache(int capacity) {
        this.capacity = capacity;
        this.minFreq = 1;
    }

    public int get(int key) {
        if (!keyNodeMap.containsKey(key))
            return -1;
        Node node = keyNodeMap.get(key);
        updateNode(node);
        return node.value;
    }

    public void put(int key, int value) {
        if (capacity == 0)
            return;
        if (keyNodeMap.containsKey(key)) {
            Node node = keyNodeMap.get(key);
            node.value = value;
            updateNode(node);
        } else {
            if (keyNodeMap.size() >= capacity) {
                DLinkedList minFreqList = freqMap.get(minFreq);
                Node evictNode = minFreqList.removeLast();
                keyNodeMap.remove(evictNode.key);
            }
            Node newNode = new Node(key, value);
            keyNodeMap.put(key, newNode);
            freqMap.computeIfAbsent(1, k -> new DLinkedList()).addNode(newNode);
            minFreq = 1;
        }
    }

    private void updateNode(Node node) {
        int oldFreq = node.freq;
        // Remove from old freq list
        DLinkedList oldList = freqMap.get(oldFreq);
        oldList.removeNode(node);
        if (oldList.isEmpty() && oldFreq == minFreq)
            minFreq++;
        // Add to new freq list
        node.freq++;
        freqMap.computeIfAbsent(node.freq, k -> new DLinkedList()).addNode(node);
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**  
  - `get`: O(1)  
  - `put`: O(1)  
- **Space Complexity:** O(capacity), for all nodes and frequency lists.

---

## **Summary Table**

| Approach | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|---------------------|-------------------|--------------------|--------|
| Naive | HashMap + scanning all entries | O(N) for get/put | O(capacity) | Not scalable |
| Frequency Buckets + Min-Heap | HashMap + LinkedHashSet + Priority Queue | O(1) amortized | O(capacity) | Better, but complex |
| HashMap + Double Linked List per freq | HashMap + Double Linked List | O(1) | O(capacity) | Most optimal |

---

## **Final Tips for Interviews:**
- Understand the importance of frequency tracking and order within the same frequency.
- Use appropriate data structures: hash maps, linked lists, doubly linked lists, and heaps.
- Focus on maintaining O(1) time complexity for `get` and `put`.
- Practice implementing the most optimized approach with careful updates to `minFreq`.

---

This concludes the detailed revision for **LFU Cache**. Mastering these approaches will help you handle cache eviction strategies efficiently in interviews!
