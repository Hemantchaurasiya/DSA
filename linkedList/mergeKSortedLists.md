# 23. Merge k Sorted Lists

---

## Problem Overview

Given **k** sorted linked lists, merge them into a single sorted linked list.

---

## Approaches to Solve the Problem

### 1. **Brute Force Approach**

### Core Idea

- Collect all nodes from the **k** lists into a single array.
- Sort the array.
- Reconstruct the linked list from the sorted array.

### Algorithm

1. Initialize an empty list `values`.
2. Traverse each linked list, appending all node values into `values`.
3. Sort `values`.
4. Create a new linked list using the sorted values.
5. Return the head of the new linked list.

### Java Code

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public ListNode mergeKLists(ListNode[] lists) {
    List<Integer> values = new ArrayList<>();
    // Step 1 & 2: Collect all node values
    for (ListNode head : lists) {
        while (head != null) {
            values.add(head.val);
            head = head.next;
        }
    }
    // Step 3: Sort the collected values
    Collections.sort(values);
    // Step 4: Create a new sorted linked list
    ListNode dummy = new ListNode(0);
    ListNode current = dummy;
    for (int val : values) {
        current.next = new ListNode(val);
        current = current.next;
    }
    return dummy.next;
}

```

### Complexity Analysis

- **Time Complexity:**
    - Collecting all nodes: O(N), where N is total nodes across all lists.
    - Sorting: O(N log N).
    - Reconstructing linked list: O(N).
    - **Total:** O(N log N).
- **Space Complexity:**
    - O(N) for storing node values.

---

### 2. **Better Solution: Min-Heap / Priority Queue**

### Core Idea

- Use a min-heap to efficiently get the smallest node among the current nodes of each list.
- Always pick the smallest node, attach it to the merged list, and move forward in that list.

### Algorithm

1. Initialize a min-heap (priority queue).
2. Push the head of each list into the min-heap.
3. Create a dummy node to start the merged list.
4. While the heap is not empty:
    - Extract the smallest node.
    - Append it to the merged list.
    - If the extracted node has a next node, push it into the heap.
5. Return the next node of the dummy.

### Java Code

```java
import java.util.PriorityQueue;

public ListNode mergeKLists(ListNode[] lists) {
    PriorityQueue<ListNode> minHeap = new PriorityQueue<>(
        (a, b) -> a.val - b.val
    );
    // Step 2: Push initial nodes
    for (ListNode node : lists) {
        if (node != null) {
            minHeap.offer(node);
        }
    }
    ListNode dummy = new ListNode(0);
    ListNode current = dummy;

    // Step 4: Process the heap
    while (!minHeap.isEmpty()) {
        ListNode smallest = minHeap.poll();
        current.next = smallest;
        current = current.next;
        if (smallest.next != null) {
            minHeap.offer(smallest.next);
        }
    }
    return dummy.next;
}

```

### Complexity Analysis

- **Time Complexity:**
    - Inserting each node into heap: O(N log k), where N is total nodes, k is number of lists.
        - Each insertion/extraction: O(log k).
- **Space Complexity:**
    - O(k) for heap storage.

---

### 3. **Most Optimal Solution: Divide and Conquer (Merge Pairs)**

### Core Idea

- Repeatedly merge pairs of lists until only one list remains.
- This reduces the problem size exponentially, leading to efficient merging.

### Algorithm

1. Repeat until only one list remains:
    - Merge pairs of lists in the array.
    - Replace the pairs with their merged list.
2. Use a helper function to merge two sorted lists.
3. Return the final merged list.

### Java Code

```java
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
    ListNode current = dummy;
    while (l1 != null && l2 != null) {
        if (l1.val < l2.val) {
            current.next = l1;
            l1 = l1.next;
        } else {
            current.next = l2;
            l2 = l2.next;
        }
        current = current.next;
    }
    if (l1 != null) current.next = l1;
    if (l2 != null) current.next = l2;
    return dummy.next;
}

```

### Complexity Analysis

- **Time Complexity:**
    - Each merge of two lists costs O(N) in total, and there are log k levels of merging.
    - Total: O(N log k).
- **Space Complexity:**
    - O(1) extra space (excluding recursion stack), as merging is done in-place.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Best Use Case |
| --- | --- | --- | --- | --- |
| Brute Force | Collect, sort, reconstruct | O(N log N) | O(N) | Small datasets or simplicity |
| Min-Heap | Use min-heap for efficient min extraction | O(N log k) | O(k) | Large number of lists, moderate total nodes |
| Divide & Conquer | Repeated pairwise merge | O(N log k) | O(1) | Large k, optimized performance |

---

## Final Tips

- For **interview** scenarios, the **divide and conquer** method is generally preferred due to its optimal time complexity.
- Understanding how to merge two sorted lists efficiently is fundamental.
- Use priority queues for cleaner code and better performance when k is large.

---

**End of Revision Note**
