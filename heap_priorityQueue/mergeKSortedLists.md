# Merge k Sorted Lists

The problem involves merging **k** sorted linked lists into one sorted linked list. It is a classic problem that can be approached using various strategies, from brute-force to optimized solutions utilizing heaps.

---

## Problem Definition:
Given an array of `k` sorted linked lists, merge them into a single sorted linked list.

---

## Approach 1: Brute Force - Collect and Sort All Nodes

### **Core Idea:**
Extract all nodes from the `k` lists, store their values, sort them, and then reconstruct the sorted linked list.

### **Algorithm:**
1. Traverse each linked list and add all node values to an array.
2. Sort the array of values.
3. Create a new linked list from the sorted array.
4. Return the head of the new list.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        List<Integer> values = new ArrayList<>();
        for (ListNode list : lists) {
            while (list != null) {
                values.add(list.val);
                list = list.next;
            }
        }
        Collections.sort(values);
        ListNode dummy = new ListNode(0);
        ListNode current = dummy;
        for (int val : values) {
            current.next = new ListNode(val);
            current = current.next;
        }
        return dummy.next;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(N log N)`, where `N` is the total number of nodes across all lists. Sorting all nodes takes `O(N log N)`.
- **Space Complexity:** `O(N)` for storing node values.

### **Dry Run:**
Suppose:
```plaintext
lists = [
  [1 -> 4 -> 5],
  [1 -> 3 -> 4],
  [2 -> 6]
]
```
- Extracted values: `[1,4,5,1,3,4,2,6]`
- Sorted: `[1,1,2,3,4,4,5,6]`
- Reconstructed list: `1 -> 1 -> 2 -> 3 -> 4 -> 4 -> 5 -> 6`

---

## Approach 2: Divide and Conquer - Pairwise Merging

### **Core Idea:**
Merge the lists two at a time, reducing the total number of lists iteratively until only one remains.

### **Algorithm:**
1. Repeatedly merge pairs of lists:
   - Merge list 0 and 1, list 2 and 3, and so on.
2. Continue until only one list remains.
3. Return the merged list.

### **Java Code:**
```java
public class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        if (lists == null || lists.length == 0) return null;
        int interval = 1;
        int n = lists.length;
        while (interval < n) {
            for (int i = 0; i + interval < n; i += interval * 2) {
                lists[i] = mergeTwoLists(lists[i], lists[i + interval]);
            }
            interval *= 2;
        }
        return lists[0];
    }

    private ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;
        while (l1 != null && l2 != null) {
            if (l1.val < l2.val) {
                tail.next = l1;
                l1 = l1.next;
            } else {
                tail.next = l2;
                l2 = l2.next;
            }
            tail = tail.next;
        }
        if (l1 != null) tail.next = l1;
        if (l2 != null) tail.next = l2;
        return dummy.next;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(N log k)`, where `N` is total nodes, and `k` is number of lists. Each merge operation takes `O(N)` and is performed `log k` times.
- **Space Complexity:** `O(1)` (excluding recursion stack), as merging is done in-place.

### **Dry Run:**
For 3 lists:
```plaintext
List 1: 1 -> 4 -> 5
List 2: 1 -> 3 -> 4
List 3: 2 -> 6
```
- Merge List 1 and List 2:
  - Result: 1 -> 1 -> 3 -> 4 -> 4 -> 5
- Merge the above result with List 3:
  - Final: 1 -> 1 -> 2 -> 3 -> 4 -> 4 -> 5 -> 6

---

## Approach 3: Min-Heap (Priority Queue) - Most Optimal

### **Core Idea:**
Use a min-heap to always select the smallest current node among all lists:
- Initialize the heap with the head node of each list.
- Extract the minimum node, add it to the merged list.
- Add the next node from the list of the extracted node into the heap.
- Continue until the heap is empty.

### **Algorithm:**
1. Create a min-heap (priority queue) ordered by node values.
2. Insert the head of each list into the heap.
3. Pop the smallest node from the heap:
   - Add it to the result list.
   - If this node has a next node, insert it into the heap.
4. Repeat until heap is empty.
5. Return the head of the merged list.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public ListNode mergeKLists(ListNode[] lists) {
        PriorityQueue<ListNode> heap = new PriorityQueue<>(Comparator.comparingInt(node -> node.val));

        for (ListNode list : lists) {
            if (list != null) {
                heap.offer(list);
            }
        }

        ListNode dummy = new ListNode(0);
        ListNode tail = dummy;

        while (!heap.isEmpty()) {
            ListNode minNode = heap.poll();
            tail.next = minNode;
            tail = tail.next;
            if (minNode.next != null) {
                heap.offer(minNode.next);
            }
        }

        return dummy.next;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** `O(N log k)`, where `N` is total nodes, and `k` is the number of lists. Each insertion and removal from the heap takes `O(log k)`.
- **Space Complexity:** `O(k)` for the heap.

### **Dry Run:**
Suppose:
```plaintext
Lists:
  [1 -> 4 -> 5]
  [1 -> 3 -> 4]
  [2 -> 6]
```
- Initialize heap with `1`, `1`, and `2`.
- Extract min: `1` from first list:
  - Add `1` to result, insert next node (`4`) from same list.
- Heap now: `1`, `2`, `4`
- Extract min: `1` from second list:
  - Add `1`, insert next (`3`)
- Heap: `2`, `3`, `4`
- Extract min: `2`:
  - Add `2`, insert next (`6`)
- Heap: `3`, `4`, `6`
- Extract min: `3`:
  - Add `3`, insert next (`4`)
- Heap: `4`, `4`, `6`
- Continue until all nodes are processed.

---

## **Summary Table**

| Approach | Best For | Time Complexity | Space Complexity | Notes |
|--------------|------------|------------------|------------------|--------|
| Collect & Sort | Small data sets | `O(N log N)` | `O(N)` | Simple but inefficient for large data |
| Pairwise Merge | Moderate k, larger lists | `O(N log k)` | `O(1)` | Efficient divide-and-conquer approach |
| Min-Heap / Priority Queue | Large k, large N | `O(N log k)` | `O(k)` | Most efficient for large `k` |

---

## **Final Tips:**
- Use **brute-force sorting** only for small or simple cases.
- Use **divide and conquer** to reduce time complexity when `k` is large.
- Use **min-heap** for the most optimal solution, especially when dealing with large `k` and `N`.
- Always handle edge cases such as empty lists.

---

**Happy coding!**
