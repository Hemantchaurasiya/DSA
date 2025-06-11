# 237. Delete Node in a Linked List

---

## Problem Overview

Given a node **(not the tail)** in a singly linked list, delete that node from the list. You are **not given access to the head** of the list, only to the node to be deleted.

---

## Approach 1: **Brute Force (Not feasible here due to constraints)**

### Core Idea

- Since the problem restricts access only to the node to be deleted, a brute-force approach (like traversing from head) isn't possible unless the head is known.
- Typically, this problem is designed with the constraint that **only the node to be deleted** is given.
- **However**, in a general linked list scenario, if the head was available, you would need to traverse from head to find the node before the target node to delete it.

### Algorithm

- Not applicable here because the problem states we only have access to the node to be deleted.

---

## Approach 2: **Delete by Copying Data (Optimal Approach)**

### Core Idea

- Since we only have access to the node to be deleted, we can't access the previous node.
- Instead, **copy the data from the next node into the current node**, then delete the next node.
- Effectively, the node takes the value of its successor, and the successor is removed from the list.

### Algorithm

1. **Copy the data** from `node.next` into `node`.
2. **Link `node.next`** to `node.next.next`, effectively removing the next node.
3. **Return** (no need to explicitly return anything; the node is modified in-place).

### Java Code

```java
public class ListNode {
    int val;
    ListNode next;
    ListNode(int val) {
        this.val = val;
        this.next = null;
    }
}

public void deleteNode(ListNode node) {
    if (node == null || node.next == null) {
        // Cannot delete if node is null or the last node
        return;
    }
    node.val = node.next.val;          // Copy data from next node
    node.next = node.next.next;        // Bypass the next node
}

```

### Complexity Analysis

- **Time Complexity:** **O(1)**
    
    Constant time operation: copying data and relinking pointers.
    
- **Space Complexity:** **O(1)**
    
    No extra space required.
    

### Dry Run

Suppose the list is: `1 -> 2 -> 3 -> 4 -> 5`, and we want to delete node with value `3`.

| Step | Current Node | Action | List State |
| --- | --- | --- | --- |
| Initial | Node with value 3 | Given node to delete | `1 -> 2 -> 3 -> 4 -> 5` |
| Step 1 | Node with value 3 | Copy `4` into current node (`val=4`) | `1 -> 2 -> 4 -> 4 -> 5` |
| Step 2 | Node with value 4 (originally 3) | Link `next` to `node.next.next` | `1 -> 2 -> 4 -> 5` |

The node with original value `3` is effectively removed by overwriting and bypassing.

---

## **Summary**

| Aspect | Details |
| --- | --- |
| **Approach** | Copy data from the next node, delete the next node |
| **Best suited when** | Only access to the node to be deleted, not the head |
| **Advantages** | O(1) time, no need for traversal |
| **Limitations** | Cannot delete the tail node (since `next` is null) |

---

## Final Notes

- This approach **only works** if the node to be deleted is **not the tail**.
- In cases where the node is the tail, additional information (like access to head) is required to delete it properly.

---

Feel free to revise or ask for further explanations!
