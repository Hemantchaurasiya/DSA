# 92. Reverse Linked List II

---

## Problem Summary:

Given the head of a singly linked list and two integers `left` and `right`, reverse the nodes of the list from position `left` to position `right` (1-indexed), and return the modified list.

---

## Approach 1: Brute Force (Using Extra Space)

### **Core Idea:**

Extract the nodes to be reversed into a list or array, reverse that sublist, and then reconnect it back into the original list.

### **Algorithm:**

1. Traverse the list to reach the node at position `left`. Keep track of:
    - `prev` (node before `left`)
    - `start` (node at position `left`)
2. Collect nodes from position `left` to `right` into an array or list.
3. Reverse the array/list of nodes.
4. Reconnect the reversed nodes back into the list:
    - If `prev` exists, link `prev.next` to the first node of the reversed sublist.
    - Link the last node of the reversed sublist to the node at position `right + 1`.
5. Handle edge cases where `left` is 1 or `right` is at the end.

### **Java Code:**

```java
public ListNode reverseBetween(ListNode head, int left, int right) {
    if (head == null || left == right) return head;

    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy;

    // Step 1: Reach node before `left`
    for (int i = 1; i < left; i++) {
        prev = prev.next;
    }

    // Step 2: Collect nodes in sublist
    ListNode current = prev.next;
    List<ListNode> nodes = new ArrayList<>();
    for (int i = left; i <= right; i++) {
        nodes.add(current);
        current = current.next;
    }

    // Step 3: Reverse the list of nodes
    Collections.reverse(nodes);

    // Step 4: Reconnect reversed sublist
    ListNode tail = prev.next; // original start of sublist
    for (int i = 0; i < nodes.size(); i++) {
        prev.next = nodes.get(i);
        prev = prev.next;
    }
    // Connect tail to the node after `right`
    tail.next = current;

    return dummy.next;
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
Traversal to reach `left`, and reconnecting nodes takes linear time.
- **Space Complexity:** **O(n)**
Extra space for storing nodes in a list.

### **Dry Run:**

- Input: List: `1 -> 2 -> 3 -> 4 -> 5`, `left=2`, `right=4`
- Traversal:
    - Reach node at position 1 (`prev` at node 1)
    - Collect nodes 2, 3, 4 into a list
    - Reverse list: 4, 3, 2
    - Reconnect:
        - `1.next` points to 4
        - 4 -> 3 -> 2 -> 5
    - Final list: `1 -> 4 -> 3 -> 2 -> 5`

---

## Approach 2: In-Place Iterative Reversal (Optimal)

### **Core Idea:**

Perform an in-place reversal of the sublist between `left` and `right` by adjusting pointers, similar to reversing a linked list, but only within the specified range.

### **Algorithm:**

1. Create a dummy node pointing to `head` to handle edge cases easily.
2. Traverse to the node just before position `left` (`prev`).
3. Initialize `current` as `prev.next`.
4. Perform `right - left` iterations:
    - Reverse the node immediately after `current` by adjusting pointers.
    - Essentially, move the node after `current` to the beginning of the sublist.
5. Return the list starting from `dummy.next`.

### **Java Code:**

```java
public ListNode reverseBetween(ListNode head, int left, int right) {
    if (head == null || left == right) return head;

    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode prev = dummy;

    // Step 1: Reach node before `left`
    for (int i = 1; i < left; i++) {
        prev = prev.next;
    }

    // Step 2: Reverse sublist in-place
    ListNode current = prev.next;
    for (int i = 0; i < right - left; i++) {
        ListNode temp = current.next; // node to move
        current.next = temp.next;     // detach temp
        temp.next = prev.next;        // insert temp at start of sublist
        prev.next = temp;             // connect prev to temp
    }

    return dummy.next;
}

```

### **Complexity Analysis:**

- **Time Complexity:** **O(n)**
Only one pass to reach `left` and perform reversal.
- **Space Complexity:** **O(1)**
In-place reversal with constant extra space.

### **Dry Run:**

- Input: List: `1 -> 2 -> 3 -> 4 -> 5`, `left=2`, `right=4`
- Process:
    - Reach node at position 1 (`prev` at node 1)
    - Set `current` at node 2
    - Loop:
        - Move node 3 to after node 1:
            - Detach 3 (`current.next`)
            - Insert before `current`:
                - 2 points to 4
                - 1 points to 3
        - Next iteration:
            - Move node 4 to after node 1:
                - Detach 4
                - Insert after 1:
                    - 2 points to null
                    - 1 points to 4
    - Final list: `1 -> 4 -> 3 -> 2 -> 5`

---

## **Summary Table**

| Approach | Time Complexity | Space Complexity | Key Insight |
| --- | --- | --- | --- |
| Brute Force (Using extra space) | O(n) | O(n) | Store nodes in a list, reverse, then reconnect |
| In-Place Iterative Reversal | O(n) | O(1) | Adjust pointers within the list directly |

---

## **Final Notes:**

- The **in-place iterative method** is optimal in both time and space.
- Handle edge cases where `left=1` or `right` is at the end.
- This problem tests your understanding of linked list pointer manipulation and in-place algorithms.

---

This comprehensive guide should help in quick revision and interview preparation for **Reverse Linked List II**!
