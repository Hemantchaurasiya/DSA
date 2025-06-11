# 86. Partition List

---

## Problem Overview

Given the head of a linked list and a value `x`, **partition** the list so that:

- All nodes with values **less than** `x` come before nodes with values **greater than or equal to** `x`.
- The original relative order of nodes in each partition should be preserved.

**Goal:** Rearrange the list **in-place** to satisfy the above conditions.

---

## Approach 1: Brute Force (Using Extra Lists)

### Core Idea

Create two separate linked lists:

- One for nodes with values `< x`.
- One for nodes with values `>= x`.

Then, connect these two lists to form the partitioned list.

### Algorithm

1. Initialize two dummy nodes:
    - `lessHead` for nodes `< x`.
    - `greaterHead` for nodes `>= x`.
2. Traverse the original list:
    - For each node, append it to either the `less` list or the `greater` list based on its value.
3. Connect the end of the `less` list to the head of the `greater` list.
4. Terminate the list by setting the last node's `next` to `null`.
5. Return the head of the combined list (`lessHead.next`).

### Java Code

```java
public ListNode partition(ListNode head, int x) {
    ListNode lessHead = new ListNode(0);
    ListNode greaterHead = new ListNode(0);
    ListNode less = lessHead;
    ListNode greater = greaterHead;
    ListNode current = head;

    while (current != null) {
        if (current.val < x) {
            less.next = current;
            less = less.next;
        } else {
            greater.next = current;
            greater = greater.next;
        }
        current = current.next;
    }
    // Connect the less list to the greater list
    greater.next = null; // avoid cycle
    less.next = greaterHead.next;

    return lessHead.next;
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, traverse the list once.
- **Space Complexity:** **O(1)**, only pointers used, no extra space proportional to input size.

### Dry Run

Input:

```
List: 1 -> 4 -> 3 -> 2 -> 5 -> 2
x = 3

```

- `less` list: 1 -> 2 -> 2
- `greater` list: 4 -> 3 -> 5
- Final combined list: `1 -> 2 -> 2 -> 4 -> 3 -> 5`

---

## Approach 2: Optimized In-Place (Single Pass, No Extra Lists)

### Core Idea

Use **two pointers** to build the list in one traversal:

- Maintain a pointer `head` for the start of the list.
- Maintain a pointer `tail` for the end of the list.
- Re-arrange nodes by moving nodes `< x` before nodes `>= x`, maintaining original relative order.

*Note:* The approach is similar to the first but emphasizes merging in-place.

### Algorithm

Same as Approach 1, but explicitly emphasizing in-place rearrangement:

- Use two dummy nodes for less and greater parts.
- Append nodes directly while traversing.
- Connect at the end.

This is essentially the same as Approach 1, optimized for in-place operation.

---

## Approach 3: One-Pass Without Extra Lists (In-Place, Single Pointer)

This approach aims to reorder nodes **without creating separate lists**, by repositioning nodes as needed.

### Core Idea

Iterate through the list:

- When a node with value `< x` is found after the initial position, move it **before** the first node with value `>= x`.
- Maintain a marker for the boundary between `< x` and `>= x`.

### Algorithm

1. Use a dummy node pointing to `head`.
2. Maintain a pointer `prev` starting at dummy.
3. Traverse the list:
    - If `current.val >= x`, move `current` forward.
    - If `current.val < x`, move `current` node just after `prev`, updating pointers accordingly, and move `prev` forward.
4. Continue until end of list.

### Java Code

```java
public ListNode partition(ListNode head, int x) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy;
    ListNode current = head;

    while (current != null) {
        if (current.val < x) {
            if (prev.next == current) {
                // Already in correct position
                prev = current;
                current = current.next;
            } else {
                // Remove current node
                prev.next = current.next;
                // Insert current after dummy
                current.next = dummy.next;
                dummy.next = current;
                // Move current forward
                current = prev.next;
            }
        } else {
            prev = current;
            current = current.next;
        }
    }
    return dummy.next;
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, single traversal.
- **Space Complexity:** **O(1)**, no extra data structures.

---

## Summary

| Approach | Time Complexity | Space Complexity | Key Points |
| --- | --- | --- | --- |
| **Using Extra Lists** (Approach 1) | O(N) | O(1) | Simple, intuitive, uses dummy nodes. |
| **In-Place via Two Pointers** | O(N) | O(1) | Efficient, maintains order, no extra space. |
| **In-Place Reordering (Pointer manipulation)** | O(N) | O(1) | Reorders nodes directly, slightly more complex logic. |

---

## Final Tips for Interviews

- Understand the importance of preserving original relative order.
- Practice both approaches: with extra lists (simpler) and in-place (more optimal).
- Be cautious about setting the last node's `next` to `null` to avoid cycles.
- Handle edge cases: empty list, all nodes `< x`, all nodes `>= x`.

---

**Happy coding!**
