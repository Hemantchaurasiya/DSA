# 142. Linked List Cycle II

---

## Problem Summary:

Given a linked list that contains a cycle, find the node where the cycle begins. If there is no cycle, return `null`.

---

## Approach 1: Brute Force (Using Hashing)

### **Core Idea:**

Traverse the linked list and keep track of visited nodes in a hash set. When you encounter a node that is already visited, that node is the start of the cycle.

### **Algorithm:**

1. Initialize an empty hash set to store visited nodes.
2. Traverse the list starting from `head`.
3. For each node:
    - If the node is already in the hash set, return that node (start of cycle).
    - Else, add the node to the hash set.
4. If traversal reaches `null`, return `null` (no cycle).

### **Java Code:**

```java
public ListNode detectCycle(ListNode head) {
    Set<ListNode> visited = new HashSet<>();
    ListNode current = head;
    while (current != null) {
        if (visited.contains(current)) {
            return current; // start of cycle
        }
        visited.add(current);
        current = current.next;
    }
    return null; // no cycle
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
Each node is visited once.
- **Space Complexity:** **O(n)**
Hash set stores all visited nodes.

### **Dry Run:**

- Input: List: `1 -> 2 -> 3 -> 4 -> 2` (cycle back to node with value 2).
- Traversal:
    - Visit node 1 → store in set
    - Visit node 2 → store in set
    - Visit node 3 → store in set
    - Visit node 4 → store in set
    - Next node is node 2 again, already in set → **detect start of cycle at node 2**.

---

## Approach 2: Floyd’s Cycle Detection (Tortoise and Hare)

### **Core Idea:**

Use two pointers (`slow` and `fast`). `slow` moves one step at a time, `fast` moves two steps. When they meet, a cycle exists. To find the start node, reset one pointer to the head and move both one step at a time until they meet again; that node is the start of the cycle.

### **Algorithm:**

1. Initialize `slow` and `fast` pointers at `head`.
2. Move `slow` by one step, `fast` by two steps.
3. If `fast` or `fast.next` becomes `null`, return `null` (no cycle).
4. If `slow` == `fast`, cycle detected:
    - Reset `slow` to `head`.
    - Move both `slow` and `fast` one step at a time.
    - The node where they meet again is the start of the cycle.
5. Return the node where they meet.

### **Java Code:**

```java
public ListNode detectCycle(ListNode head) {
    if (head == null || head.next == null) return null;

    ListNode slow = head;
    ListNode fast = head;

    // Detect cycle
    while (fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;
        if (slow == fast) {
            // Cycle detected, find entry point
            slow = head;
            while (slow != fast) {
                slow = slow.next;
                fast = fast.next;
            }
            return slow; // start of cycle
        }
    }
    return null; // no cycle
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
At most two traversals: one for detection, one for locating start.
- **Space Complexity:** **O(1)**
No extra space used.

### **Dry Run:**

- Input: List: `1 -> 2 -> 3 -> 4 -> 2` (cycle back to node with value 2).
- Initialization:
    - `slow = 1`, `fast = 1`
- Iteration 1:
    - `slow` moves to 2
    - `fast` moves to 3
- Iteration 2:
    - `slow` moves to 3
    - `fast` moves to 2
- Iteration 3:
    - `slow` moves to 4
    - `fast` moves to 4 (since `fast` was at 2, `fast.next.next` is 4)
    - `slow` == `fast` at node 4 → cycle detected.
- To find start:
    - Reset `slow` to head (`1`)
    - Move both one step:
        - `slow` to 2
        - `fast` to 2
    - Both pointers meet at node with value 2 → **start of cycle**.

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Key Insight |
| --- | --- | --- | --- |
| Hashing | O(n) | O(n) | Store visited nodes; revisit indicates start of cycle |
| Floyd’s Cycle Detection (Tortoise & Hare) | O(n) | O(1) | Two pointers at different speeds; meeting point indicates cycle start |

---

## **Final Notes:**

- The **Floyd’s Cycle Detection** approach is optimal with constant space.
- Always handle edge cases like an empty list or a single node without a cycle.
- This problem is a classic application of cycle detection algorithms in linked lists.

---

This guide provides a comprehensive understanding of detecting and locating the start of a cycle in a linked list, perfect for interview prep and quick revision!
