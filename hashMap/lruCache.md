# LRU Cache

This guide covers the problem of implementing an **LRU (Least Recently Used) Cache**, a common data structure problem that tests understanding of caching mechanisms and efficient data retrieval/update operations. We will explore multiple approaches, from brute-force to the most optimal solution, along with explanations, code snippets, and dry runs.

---

## 1. Brute Force Approach

### **Core Idea:**
Maintain a list or array to store cache entries, and on each access, move the accessed element to the front to indicate recent use. When the cache exceeds capacity, remove the least recently used item from the end.

### **Algorithm:**
- Use a list (or array) to store cache items.
- For `get(key)`:
  - Search the list for the key.
  - If found, move the item to the front (mark as recently used).
  - Return its value.
- For `put(key, value)`:
  - Search for the key in the list.
  - If found, update its value and move to the front.
  - Else, insert at the front.
  - If size exceeds capacity, remove the last item.

### **Java Code:**

```java
import java.util.*;

class LRUCache {
    private int capacity;
    private LinkedList<Integer> cache;
    private Map<Integer, Integer> map; // key to value

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.cache = new LinkedList<>();
        this.map = new HashMap<>();
    }

    public int get(int key) {
        if (!map.containsKey(key))
            return -1;
        // Move key to front
        cache.remove((Integer) key);
        cache.addFirst(key);
        return map.get(key);
    }

    public void put(int key, int value) {
        if (map.containsKey(key)) {
            cache.remove((Integer) key);
        } else if (cache.size() == capacity) {
            int leastUsed = cache.removeLast();
            map.remove(leastUsed);
        }
        cache.addFirst(key);
        map.put(key, value);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - `get`: O(n) (due to list search)  
  - `put`: O(n) (search and removal in list)  
  - Overall inefficient for large n.
- **Space Complexity:** O(capacity), for storing cache entries and list.

### **Dry Run:**
Suppose capacity=2, sequence of operations:
```
put(1, 10)
put(2, 20)
get(1)
put(3, 30)
```

- After `put(1, 10)`: cache = [1], map = {1:10}
- After `put(2, 20)`: cache = [2, 1], map = {1:10, 2:20}
- `get(1)`: returns 10, move 1 to front: cache = [1, 2]
- `put(3, 30)`: cache full, remove last (2): cache = [1], map={1:10}
  - Add 3: cache = [3, 1], map={1:10, 3:30}

---

## 2. Better Solution: Using HashMap + Doubly Linked List

### **Core Idea:**
Combine a HashMap for O(1) access and a Doubly Linked List to keep track of usage order.  
- **HashMap:** key → node (which contains key, value)
- **Doubly Linked List:** Most recently used at the head, least recently used at the tail.

### **Algorithm:**
- **Get(key):**
  - Check if key exists in HashMap.
  - If not, return -1.
  - Move corresponding node to the head of DLL.
  - Return node's value.
- **Put(key, value):**
  - If key exists, update value and move node to head.
  - Else, create a new node, insert at head.
  - If capacity exceeded, remove node from tail and delete from HashMap.

### **Java Code:**

```java
class LRUCache {
    private class DLinkedNode {
        int key, value;
        DLinkedNode prev, next;
        DLinkedNode() {}
        DLinkedNode(int key, int value) {
            this.key = key;
            this.value = value;
        }
    }

    private final int capacity;
    private Map<Integer, DLinkedNode> cache = new HashMap<>();
    private DLinkedNode head, tail;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        head = new DLinkedNode();
        tail = new DLinkedNode();
        head.next = tail;
        tail.prev = head;
    }

    private void addNode(DLinkedNode node) {
        // Always add new node right after head
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    private void removeNode(DLinkedNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }

    private void moveToHead(DLinkedNode node) {
        removeNode(node);
        addNode(node);
    }

    private DLinkedNode popTail() {
        DLinkedNode res = tail.prev;
        removeNode(res);
        return res;
    }

    public int get(int key) {
        DLinkedNode node = cache.get(key);
        if (node == null) return -1;
        moveToHead(node);
        return node.value;
    }

    public void put(int key, int value) {
        DLinkedNode node = cache.get(key);
        if (node == null) {
            DLinkedNode newNode = new DLinkedNode(key, value);
            cache.put(key, newNode);
            addNode(newNode);
            if (cache.size() > capacity) {
                DLinkedNode tailNode = popTail();
                cache.remove(tailNode.key);
            }
        } else {
            node.value = value;
            moveToHead(node);
        }
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  - `get`: O(1) (hash map lookup + DLL move)  
  - `put`: O(1) (hash map insert/update + DLL operations)
- **Space Complexity:** O(capacity), for storing cache nodes and hash map.

### **Dry Run:**
Capacity=2, operations:
```
put(1, 10)
put(2, 20)
get(1)       // returns 10, move 1 to head
put(3, 30)  // evicts key 2
```

- After `put(1, 10)`: list = [1], cache={1: node}
- After `put(2, 20)`: list = [2, 1], cache={1: node, 2: node}
- `get(1)`: returns 10, move 1 to head: list=[1,2]
- `put(3,30)`: capacity exceeded, remove tail (key 2): list=[1], cache={1: node}
  - Add 3: list=[3,1], cache={1: node, 3: node}

---

## 3. Most Optimal Solution: HashMap + Doubly Linked List

This is the standard, most efficient approach, providing **O(1)** for both `get` and `put` operations, and is the preferred solution in interviews.

---

## Summary

| Approach | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|---------------------|-------------------|--------------------|--------|
| Brute Force | List / Array | O(n) | O(capacity) | Not efficient for large inputs |
| HashMap + Doubly Linked List | HashMap + DLL | O(1) | O(capacity) | Most optimal and widely used |

---

## Final Tips for Interviews:
- Use HashMap + Doubly Linked List for optimal solutions.
- Ensure `get` and `put` operations are O(1).
- Handle edge cases: capacity zero, non-existent keys.
- Practice dry runs for understanding cache eviction policies.

---

This concludes the comprehensive revision for **LRU Cache**. Happy coding!
