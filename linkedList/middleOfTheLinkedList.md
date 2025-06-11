# 876. Middle of the Linked List

---

## Problem Summary:

Given a non-empty singly linked list, return the middle node of the linked list. If there are two middle nodes, return the second middle node.

---

## Approach 1: Brute Force (Using Length Calculation)

### **Core Idea:**

First, traverse the entire linked list to determine its length. Then, traverse again up to the middle node based on the length.

### **Algorithm:**

1. Initialize a variable `length` to 0.
2. Traverse the list from head to end, incrementing `length` for each node.
3. Calculate the middle position as `length / 2` (integer division).
4. Traverse again from the head node up to the middle position.
5. Return the node at that position.

### **Java Code:**

```java
public ListNode middleNode(ListNode head) {
    int length = 0;
    ListNode current = head;
    // Step 1 & 2: Calculate length of list
    while (current != null) {
        length++;
        current = current.next;
    }
    // Step 3: Find middle position
    int mid = length / 2;
    current = head;
    // Step 4: Traverse to middle node
    for (int i = 0; i < mid; i++) {
        current = current.next;
    }
    return current;
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
Two traversals over the list, each linear in the number of nodes.
- **Space Complexity:** **O(1)**
No extra space used apart from pointers and counters.

### **Dry Run:**

- Input: `[1 -> 2 -> 3 -> 4 -> 5]`
- Length calculation:
    - Nodes: 1, 2, 3, 4, 5 → `length = 5`.
- Middle position: `5 / 2 = 2`.
- Second traversal:
    - Move 2 steps starting from head:
        - Step 1: node with value 1
        - Step 2: node with value 2
- **Output:** Node with value 3 (since zero-based index `2` corresponds to the third node, which is the middle for odd length).

---

## Approach 2: Slow and Fast Pointers (Optimized Solution)

### **Core Idea:**

Use two pointers (`slow` and `fast`). `slow` moves one step at a time, while `fast` moves two steps. When `fast` reaches the end, `slow` will be at the middle.

### **Algorithm:**

1. Initialize two pointers, `slow` and `fast`, both pointing to the head.
2. Move `slow` by one node and `fast` by two nodes in each iteration.
3. Continue until `fast` reaches the end (`null`) or `fast.next` is `null`.
4. When loop ends, `slow` points to the middle node.
5. Return `slow`.

### **Java Code:**

```java
public ListNode middleNode(ListNode head) {
    ListNode slow = head;
    ListNode fast = head;
    while (fast != null && fast.next != null) {
        slow = slow.next;          // move 1 step
        fast = fast.next.next;     // move 2 steps
    }
    return slow;
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
Single traversal with `fast` and `slow`.
- **Space Complexity:** **O(1)**
Constant extra space.

### **Dry Run:**

- Input: `[1 -> 2 -> 3 -> 4 -> 5]`
- Initial:
    - `slow` = 1, `fast` = 1
- Iteration 1:
    - `slow` moves to 2
    - `fast` moves to 3
- Iteration 2:
    - `slow` moves to 3
    - `fast` moves to 5
- Next iteration:
    - `fast.next` is `null`, so loop ends.
- **Output:** Node with value 3, which is the middle node.

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Key Insight |
| --- | --- | --- | --- |
| Brute Force (Length Calculation) | O(n) | O(1) | Two passes: measure length, then find middle |
| Slow & Fast Pointers | O(n) | O(1) | One pass: two pointers moving at different speeds |

---

## Final Notes:

- The **Slow and Fast Pointers** approach is the most efficient and widely used for such problems.
- Always consider edge cases, such as a list with a single node.
- For even length lists, this method returns the second middle node (as per problem statement).

---

This comprehensive guide should help you understand the problem deeply and prepare effectively for interviews!
