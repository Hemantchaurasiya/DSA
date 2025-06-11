# 148. Sort List

---

## Problem Overview

Given the head of a singly linked list, sort it in **ascending order** and return the sorted list. The challenge is to perform the sorting **in-place** with **O(1)** space complexity and **O(n log n)** time complexity.

---

## Approaches to Solve the Problem

### 1. Brute Force: Convert to Array & Sort

### Core Idea:

Convert the linked list into an array, sort the array, then reconstruct the linked list from the sorted array.

### Algorithm:

- Traverse the linked list and store all node values in an array.
- Use a built-in sorting algorithm (like `Arrays.sort()`).
- Iterate over the linked list again, updating each node's value with the sorted array values.
- Alternatively, reconstruct the list from the sorted array.

### Java Code:

```java
public ListNode sortList(ListNode head) {
    if (head == null || head.next == null) return head;

    List<Integer> values = new ArrayList<>();
    ListNode current = head;

    // Convert linked list to array
    while (current != null) {
        values.add(current.val);
        current = current.next;
    }

    // Sort the array
    Collections.sort(values);

    // Reassign sorted values to linked list
    current = head;
    for (int val : values) {
        current.val = val;
        current = current.next;
    }

    return head;
}

```

### Complexity Analysis:

- **Time Complexity:** **O(n log n)** due to sorting.
- **Space Complexity:** **O(n)** for storing values in an array.

---

### 2. Better Solution: Merge Sort on Linked List

### Core Idea:

Use **top-down** merge sort directly on the linked list, which sorts the list in **O(n log n)** time without extra space for arrays.

### Algorithm:

- Use **fast and slow pointers** to find the middle of the list.
- Recursively sort the **left** and **right** halves.
- Merge the two sorted halves.

### Java Code:

```java
public ListNode sortList(ListNode head) {
    if (head == null || head.next == null) return head;

    // Find the middle node
    ListNode mid = getMiddle(head);
    ListNode rightHead = mid.next;
    mid.next = null; // Split list into two halves

    // Recursive sort
    ListNode left = sortList(head);
    ListNode right = sortList(rightHead);

    // Merge sorted halves
    return merge(left, right);
}

private ListNode getMiddle(ListNode head) {
    ListNode slow = head;
    ListNode fast = head.next;
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow;
}

private ListNode merge(ListNode l1, ListNode l2) {
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

### Complexity Analysis:

- **Time Complexity:** **O(n log n)** because merge sort divides the list log n times and merges in linear time.
- **Space Complexity:** **O(1)** (ignoring recursion stack space) since sorting is done in place.

---

### 3. Optimized Solution: Bottom-Up Iterative Merge Sort

### Core Idea:

Implement **bottom-up merge sort** iteratively to avoid recursion and further optimize space usage.

### Algorithm:

- Determine the length of the list.
- Start with sublists of size 1, then merge pairs to create sorted sublists of size 2.
- Double sublist size each iteration until the entire list is sorted.

### Java Code:

```java
public ListNode sortList(ListNode head) {
    if (head == null || head.next == null) return head;

    // Calculate length
    int length = 0;
    ListNode current = head;
    while (current != null) {
        length++;
        current = current.next;
    }

    ListNode dummy = new ListNode(0);
    dummy.next = head;

    for (int size = 1; size < length; size <<= 1) {
        ListNode prev = dummy;
        current = dummy.next;
        while (current != null) {
            // Left sublist
            ListNode left = current;
            // Right sublist
            ListNode right = split(left, size);
            // Next part
            current = split(right, size);
            // Merge left and right
            prev.next = merge(left, right);
            while (prev.next != null) {
                prev = prev.next;
            }
        }
    }
    return dummy.next;
}

// Split the list after 'size' nodes
private ListNode split(ListNode head, int size) {
    for (int i = 1; head != null && i < size; i++) {
        head = head.next;
    }
    if (head == null) return null;
    ListNode second = head.next;
    head.next = null;
    return second;
}

private ListNode merge(ListNode l1, ListNode l2) {
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
    tail.next = (l1 != null) ? l1 : l2;
    return dummy.next;
}

```

### Complexity Analysis:

- **Time Complexity:** **O(n log n)** — similar to recursive merge sort, but iterative.
- **Space Complexity:** **O(1)** (no recursion stack).

---

## Dry Run: Example with Input List

Input: **[4 -> 2 -> 1 -> 3]**

---

### Step-by-step for Recursive Merge Sort:

| Step | Action | List State | Variables/Comments |
| --- | --- | --- | --- |
| Initial | Input list | 4 -> 2 -> 1 -> 3 |  |
| Find middle | slow=4, fast=2 | mid=2 | splits into 4->2 and 1->3 |
| Recursive sort left (4->2) |  |  |  |
| Find middle | mid=4 | mid=2 |  |
| Sort left (4) | single node | 4 | base case, return 4 |
| Sort right (2) | single node | 2 | base case, return 2 |
| Merge (4,2) |  | 2 -> 4 | sorted left half |
| Recursive sort right (1->3) |  |  |  |
| Find middle | mid=1 | mid=3 |  |
| Sort left (1) | base case | 1 |  |
| Sort right (3) | base case | 3 |  |
| Merge (1,3) |  | 1 -> 3 | sorted right half |
| Final merge | merge (2->4, 1->3) | 1 -> 2 -> 3 -> 4 | fully sorted list |

---

## Summary

- **Brute force**: Convert list to array, sort, reconstruct. Easy but uses extra space.
- **Top-down merge sort**: Efficient in-place sorting with recursive divide and conquer.
- **Bottom-up merge sort**: Iterative, avoids recursion, optimal space usage.

---

## Final Tips for Interviews:

- Prioritize the **merge sort** approach for linked lists due to its **O(n log n)** time and **O(1)** space.
- Be comfortable with **finding the middle** using slow/fast pointers.
- Practice **merging two sorted linked lists** efficiently.
- Always consider edge cases: empty list, single element list.

---

**End of Revision Note**
