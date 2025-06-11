# 206. Reverse Linked List

---

## Problem Summary:

Given the head of a singly linked list, reverse the list and return the new head.

---

## Approach 1: Iterative Method (Most Common and Efficient)

### **Core Idea:**

Use three pointers (`prev`, `curr`, `next`) to reverse the direction of the `next` pointers of each node iteratively, effectively reversing the linked list in-place.

### **Algorithm:**

1. Initialize:
    - `prev` as `null` (since the new tail will point to `null`)
    - `curr` as `head` (starting node)
2. Loop through the list until `curr` is `null`:
    - Store `curr.next` in `next` (to keep track of the remaining list)
    - Reverse the `next` pointer of `curr` to point to `prev`
    - Move `prev` to `curr`
    - Move `curr` to `next`
3. After the loop ends, `prev` points to the new head of the reversed list. Return `prev`.

### **Java Code:**

```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null;
    ListNode curr = head;
    while (curr != null) {
        ListNode next = curr.next; // store next node
        curr.next = prev;          // reverse pointer
        prev = curr;               // move prev forward
        curr = next;               // move curr forward
    }
    return prev; // new head
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
Each node is visited exactly once.
- **Space Complexity:** **O(1)**
Only a few pointers are used; no extra space needed.

### **Dry Run:**

- Input: `1 -> 2 -> 3 -> 4 -> null`
- Initialization:
    - `prev = null`, `curr = 1`
- Step-by-step:
    - Store `next = 2`
    - Reverse: `1.next = null`
    - Move `prev` to `1`, `curr` to `2`
- Next iteration:
    - Store `next = 3`
    - Reverse: `2.next = 1`
    - Move `prev` to `2`, `curr` to `3`
- Next iteration:
    - Store `next = 4`
    - Reverse: `3.next = 2`
    - Move `prev` to `3`, `curr` to `4`
- Next iteration:
    - Store `next = null`
    - Reverse: `4.next = 3`
    - Move `prev` to `4`, `curr` to `null`
- End:
    - Return `prev` pointing to node `4`

---

## Approach 2: Recursive Method

### **Core Idea:**

Recursively reverse the rest of the list beyond the current node, then adjust the pointers to reverse the current node.

### **Algorithm:**

1. Base case:
    - If `head` is `null` or `head.next` is `null`, return `head`.
2. Recursively reverse the sublist starting from `head.next`.
3. After recursion, set:
    - `head.next.next = head` (reverse the pointer)
    - `head.next = null` (to terminate the list)
4. Return the new head obtained from recursion.

### **Java Code:**

```java
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) {
        return head;
    }
    ListNode newHead = reverseList(head.next);
    head.next.next = head;
    head.next = null;
    return newHead;
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
Each node is processed once.
- **Space Complexity:** **O(n)** (due to recursion stack)

### **Dry Run:**

- Input: `1 -> 2 -> 3 -> 4 -> null`
- Recursive calls:
    - Reverse from `2 -> 3 -> 4`
    - Reverse from `3 -> 4`
    - Reverse from `4`
    - Base case: return `4`
- Unwinding:
    - Set `3.next.next = 3` (point `4.next` to `3`)
    - Set `3.next = null`
    - Repeat for previous calls, finally reversing the entire list.

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Key Insight |
| --- | --- | --- | --- |
| Iterative | O(n) | O(1) | Use three pointers to reverse in-place |
| Recursive | O(n) | O(n) | Recurse to end, then reverse pointers on unwind |

---

## **Final Notes:**

- The **iterative approach** is preferred for its constant space complexity.
- Recursive approach is elegant but may lead to stack overflow for very large lists.
- Always verify edge cases:
    - Empty list (`null`)
    - Single node list
- Reversing a linked list is a fundamental operation with multiple applications.

---

This guide provides a clear, concise overview of reversing a linked list for interview prep and quick revision!
