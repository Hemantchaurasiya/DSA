# Remove Nth Node From End of Linked List

---

## Problem Statement:

Given the head of a linked list, remove the **Nth** node from the end of the list and return its head.

---

## Approach 1: Brute Force

### **Core Idea:**

Calculate the total length of the linked list, then find the (length - N + 1)th node from the start and remove it.

### **Algorithm:**

1. Traverse the linked list to **calculate the total length** (`len`).
2. Find the position of the node to remove from the start: `pos = len - N + 1`.
3. Traverse again to the node just **before the target node** (`pos - 1`).
4. Remove the target node by adjusting the `next` pointer of the previous node.
5. Handle edge cases:
    - If the node to delete is the head (i.e., `pos == 1`), update `head`.

### **Java Code:**

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    int length = 0;
    ListNode temp = head;
    // Step 1: Count total length
    while (temp != null) {
        length++;
        temp = temp.next;
    }
    // Step 2: Find position from start
    int targetPos = length - n + 1;
    // Handle removal at head
    if (targetPos == 1) {
        return head.next;
    }
    // Step 3: Traverse to node before target
    temp = head;
    for (int i = 1; i < targetPos - 1; i++) {
        temp = temp.next;
    }
    // Step 4: Remove target node
    temp.next = temp.next.next;
    return head;
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(L)**, where `L` is the length of the linked list (two traversals: one for length calculation, one for removal).
- **Space Complexity:** **O(1)**, no extra space used.

### **Dry Run:**

| Input List | N | Length Calculation | Length | Target Position | Removal Step | Final List |
| --- | --- | --- | --- | --- | --- | --- |
| 1->2->3->4->5 | 2 | Count nodes -> 5 | 5 | 5 - 2 + 1 = 4 | Remove 4th node (which is 4) | 1->2->3->5 |

---

## Approach 2: Two-Pointer (Optimal Solution)

### **Core Idea:**

Use two pointers (`fast` and `slow`) with a gap of `N` nodes between them. When `fast` reaches the end, `slow` points to the node **before** the node to remove.

### **Algorithm:**

1. Initialize both pointers to a dummy node that points to `head`. The dummy node simplifies edge cases, especially when deleting the head.
2. Move `fast` pointer `N + 1` steps ahead.
3. Move both `fast` and `slow` pointers **together** until `fast` reaches the end (`null`).
4. At this point, `slow.next` is the node to delete.
5. Remove the node by updating `slow.next = slow.next.next`.
6. Return `dummy.next` as new head.

### **Java Code:**

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
    // Dummy node to handle edge cases
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode fast = dummy;
    ListNode slow = dummy;

    // Step 1: Move fast pointer n+1 steps ahead
    for (int i = 0; i <= n; i++) {
        fast = fast.next;
    }

    // Step 2: Move both pointers until fast reaches end
    while (fast != null) {
        fast = fast.next;
        slow = slow.next;
    }

    // Step 3: Delete the target node
    slow.next = slow.next.next;

    return dummy.next;
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(L)**, only one pass through the list.
- **Space Complexity:** **O(1)**, no extra space apart from pointers.

### **Dry Run:**

| Input List | N | Dummy -> 1->2->3->4->5 | Fast moves | Slow moves | End of loop | Node to delete | Final List |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1->2->3->4->5 | 2 | start | move fast 3 steps ahead (to node 3) | move both until fast reaches null | fast at null, slow at node 3 | delete node 4 | 1->2->3->5 |

---

## Summary:

| Approach | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- |
| Brute Force | O(L) | O(1) | Two passes, straightforward |
| Two-Pointer | O(L) | O(1) | Single pass, optimal for large lists |

---

## Final Tips:

- Use a **dummy node** to simplify edge cases (like deleting the head).
- The **two-pointer method** is preferred for optimality.
- Always consider edge cases:
    - List with only one node.
    - Removing the head node.
    - `N` equals list length.

---

This comprehensive guide should help in quick revision and understanding for interviews related to linked list manipulations involving removing the Nth node from the end.
