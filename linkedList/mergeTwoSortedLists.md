# 21. Merge Two Sorted Lists

---

## Problem Overview

Given two **sorted singly linked lists**, merge them into one **sorted linked list**. Return the head of the merged list.

---

## Approach 1: Brute Force (Using Extra Space)

### Core Idea

- Traverse both lists and append all nodes to a collection (like an array).
- Sort the collection.
- Rebuild a linked list from the sorted array.

### Algorithm

1. Initialize an empty list `values`.
2. Traverse both linked lists, appending each node's value to `values`.
3. Sort `values`.
4. Create a new linked list from the sorted array.
5. Return the head of this new list.

### Java Code

```java
import java.util.*;

public class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        List<Integer> values = new ArrayList<>();
        while (l1 != null) {
            values.add(l1.val);
            l1 = l1.next;
        }
        while (l2 != null) {
            values.add(l2.val);
            l2 = l2.next;
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

### Complexity Analysis

- **Time Complexity:**
    - Traversing both lists: O(n + m)
    - Sorting the combined list: O((n + m) log(n + m))
    - Overall: **O((n + m) log(n + m))**
- **Space Complexity:**
    - Extra space for the array: O(n + m)

---

## Approach 2: Recursive Merging (Optimal for Simplicity and Clarity)

### Core Idea

- Recursively compare the heads of both lists.
- Append the smaller node to the result, then recurse on the remaining list.
- Continue until both lists are exhausted.

### Algorithm

1. If either list is empty, return the other list.
2. Compare the current nodes of `l1` and `l2`.
3. The node with the smaller value becomes the next node in the merged list.
4. Recursively merge the remaining nodes.

### Java Code

```java
public class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null) return l2;
        if (l2 == null) return l1;

        if (l1.val < l2.val) {
            l1.next = mergeTwoLists(l1.next, l2);
            return l1;
        } else {
            l2.next = mergeTwoLists(l1, l2.next);
            return l2;
        }
    }
}

```

### Complexity Analysis

- **Time Complexity:**
    - Each node is processed once: **O(n + m)**
- **Space Complexity:**
    - Call stack due to recursion: **O(n + m)**

---

## Approach 3: Iterative Merging (Most Preferred)

### Core Idea

- Use two pointers, `l1` and `l2`.
- Maintain a dummy node to simplify edge cases.
- Iteratively compare nodes and attach the smaller one to the merged list.
- Once one list is exhausted, attach the remaining part of the other list.

### Algorithm

1. Initialize a dummy node and a pointer `tail` pointing to dummy.
2. While both lists are not null:
    - Compare `l1.val` and `l2.val`.
    - Append the smaller node to `tail.next`.
    - Move the pointer (`l1` or `l2`) forward.
    - Advance `tail`.
3. After the loop, attach any remaining nodes from `l1` or `l2`.
4. Return `dummy.next`.

### Java Code

```java
public class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
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
        // Attach remaining nodes
        if (l1 != null) tail.next = l1;
        if (l2 != null) tail.next = l2;

        return dummy.next;
    }
}

```

### Complexity Analysis

- **Time Complexity:**
    - Each node is processed once: **O(n + m)**
- **Space Complexity:**
    - Constant extra space: **O(1)**

---

## Dry Run Example

### Input

```
l1: 1 -> 3 -> 5
l2: 2 -> 4 -> 6

```

### Iterative Approach Step-by-step

| Step | `l1` Node | `l2` Node | `tail.next` | List Status | Next `l1` | Next `l2` | `tail` moves to `tail.next` |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | 1 | 2 | 1 | 1 -> 2 -> 4 -> 6 (partial) | 3 | 2 | moves to 1 |
| 2 | 3 | 2 | 2 | 1 -> 2 -> 3 -> 5 (partial) | 3 | 4 | moves to 2 |
| 3 | 3 | 4 | 3 | 1 -> 2 -> 3 -> 4 -> 6 (partial) | 5 | 4 | moves to 3 |
| 4 | 5 | 4 | 4 | 1 -> 2 -> 3 -> 4 -> 5 (partial) | 5 | 6 | moves to 4 |
| 5 | 5 | 6 | 5 | 1 -> 2 -> 3 -> 4 -> 5 -> 6 | null | 6 | moves to 5 |
| 6 | 5 | 6 | 6 | List completed; attach remaining `l2` | null | null | End |

**Final Merged List:** `1 -> 2 -> 3 -> 4 -> 5 -> 6`

---

## Summary

| Approach | Best Use Case | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- |
| Brute Force | When simplicity is prioritized over efficiency | O((n + m) log(n + m)) | O(n + m) | Uses extra space, sorting step |
| Recursive | For concise and elegant code | O(n + m) | O(n + m) (call stack) | Recursive depth can be large |
| Iterative | Most efficient and preferred | O(n + m) | O(1) | Avoids recursion overhead |

---

## Final Tips for Interviews

- Prefer the **iterative approach** for optimal performance and space efficiency.
- Understand the **recursive approach** for clarity and elegance.
- Be prepared to discuss edge cases like empty lists.
- Practice dry runs to ensure understanding of pointer manipulations.

---

**Happy Coding!**
