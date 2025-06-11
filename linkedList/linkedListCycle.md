# 141. Linked List Cycle

---

## Problem Summary:

Given a linked list, determine if it has a cycle in it. A cycle exists if any node in the list is visited again by traversing the `next` pointers.

---

## Approach 1: Brute Force (Using Hashing)

### **Core Idea:**

Keep track of visited nodes using a hash set. If we revisit a node, it indicates a cycle.

### **Algorithm:**

1. Initialize an empty hash set to store visited nodes.
2. Traverse the linked list from the head.
3. For each node:
    - If the node is already in the hash set, a cycle exists → return `true`.
    - Otherwise, add the node to the hash set.
4. If traversal reaches the end (`null`), no cycle is present → return `false`.

### **Java Code:**

```java
public boolean hasCycle(ListNode head) {
    Set<ListNode> visited = new HashSet<>();
    ListNode current = head;
    while (current != null) {
        if (visited.contains(current)) {
            return true; // cycle detected
        }
        visited.add(current);
        current = current.next;
    }
    return false; // no cycle
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
Each node visited once.
- **Space Complexity:** **O(n)**
Hash set stores all nodes in the worst case.

### **Dry Run:**

- Input: List with nodes: 1 → 2 → 3 → 4 → 2 (cycle back to node with value 2)
- Traversal:
    - Visit node 1 → store in set
    - Visit node 2 → store in set
    - Visit node 3 → store in set
    - Visit node 4 → store in set
    - Next node is node 2 again, which is already in set → **detect cycle**, return `true`.

---

## Approach 2: Floyd’s Cycle Detection Algorithm (Tortoise and Hare)

### **Core Idea:**

Use two pointers moving at different speeds (`slow` and `fast`). If there's a cycle, the two pointers will eventually meet.

### **Algorithm:**

1. Initialize two pointers:
    - `slow` at `head`
    - `fast` at `head`
2. Move `slow` by one step and `fast` by two steps in each iteration.
3. If at any point:
    - `fast` or `fast.next` becomes `null`, no cycle exists → return `false`.
    - `slow` equals `fast`, cycle detected → return `true`.

### **Java Code:**

```java
public boolean hasCycle(ListNode head) {
    if (head == null || head.next == null) return false;

    ListNode slow = head;
    ListNode fast = head;

    while (fast != null && fast.next != null) {
        slow = slow.next;          // move 1 step
        fast = fast.next.next;     // move 2 steps
        if (slow == fast) {
            return true; // cycle detected
        }
    }
    return false; // no cycle
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
In the worst case, `slow` and `fast` pointers traverse the list once.
- **Space Complexity:** **O(1)**
No extra data structures used, only pointers.

### **Dry Run:**

- Input: List with nodes: 1 → 2 → 3 → 4 → 2 (cycle back to node with value 2)
- Initialization:
    - `slow` = 1, `fast` = 1
- Iteration 1:
    - `slow` moves to 2
    - `fast` moves to 3
- Iteration 2:
    - `slow` moves to 3
    - `fast` moves to 2 (since `fast` was at 3, `fast.next.next` is 2)
- Iteration 3:
    - `slow` moves to 4
    - `fast` moves to 4 (since `fast` was at 2, `fast.next.next` is 4)
- `slow` == `fast` at node with value 4 → **Cycle detected**, return `true`.

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Key Insight |
| --- | --- | --- | --- |
| Hashing | O(n) | O(n) | Store visited nodes to detect revisit |
| Floyd’s Cycle Detection (Tortoise & Hare) | O(n) | O(1) | Two pointers at different speeds will meet if cycle exists |

---

## **Final Notes:**

- The **Floyd’s Cycle Detection** is the most optimal approach with constant space.
- Always check for edge cases like an empty list (`null`) or a list with only one node.
- Cycle detection is crucial in linked list problems to prevent infinite loops.

---

This guide provides a comprehensive understanding of detecting cycles in linked lists, perfectly suited for interview prep and quick revision!
