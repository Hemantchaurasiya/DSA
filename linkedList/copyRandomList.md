# 138. Copy List with Random Pointer

---

## Problem Overview

You are given a **linked list** where each node has two pointers:

- **next**: points to the next node in the list.
- **random**: points to any node in the list or `null`.

Your task is to **create a deep copy** of this list, meaning:

- A new list with entirely new nodes.
- The `next` and `random` pointers of the new list must replicate the original list's structure.

---

## Approach 1: Brute Force (Using Hash Map)

### Core Idea

Utilize extra space (hash map) to establish a mapping between original nodes and their copies. This simplifies setting the `random` pointers after creating all nodes.

### Algorithm

1. **First pass:** Traverse the original list, create a copy of each node with the same value, and store the mapping from original node to new node in a hash map.
2. **Second pass:** Traverse the list again:
    - Set the `next` pointer of each copied node to the copy of the original node's `next`.
    - Set the `random` pointer of each copied node to the copy of the original node's `random` (using the hash map).
3. Return the head of the copied list (mapped from the original head).

### Java Code

```java
class Node {
    int val;
    Node next;
    Node random;
    Node(int val) { this.val = val; }
}

public Node copyRandomList(Node head) {
    if (head == null) return null;

    Map<Node, Node> map = new HashMap<>();
    Node current = head;

    // First pass: create copies of nodes and store in map
    while (current != null) {
        map.put(current, new Node(current.val));
        current = current.next;
    }

    // Second pass: assign next and random pointers
    current = head;
    while (current != null) {
        Node copyNode = map.get(current);
        copyNode.next = (current.next != null) ? map.get(current.next) : null;
        copyNode.random = (current.random != null) ? map.get(current.random) : null;
        current = current.next;
    }

    return map.get(head);
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, where N is the number of nodes, since we traverse the list twice.
- **Space Complexity:** **O(N)**, due to the hash map storing all node mappings.

### Dry Run

Suppose the list:

```
Node1(val=1) -> Node2(val=2) -> null
Node1.random -> Node2
Node2.random -> Node1

```

- **First Pass:** Create copies:
    - Map: {Node1 -> Copy1, Node2 -> Copy2}
- **Second Pass:** Set pointers:
    - Copy1.next = Copy2
    - Copy1.random = Copy2
    - Copy2.random = Copy1

---

## Approach 2: Interleaving Nodes (Optimized, No Extra Space)

### Core Idea

Interleave copied nodes with original nodes to avoid extra space:

- For each original node, insert its copy right after it.
- Set the `random` pointers for the copied nodes using the interleaved structure.
- Finally, separate the original and copied nodes to form the new list.

### Algorithm

1. **Create copied nodes interleaved with original nodes:**
    - For each node, create its copy and insert it immediately after the original node.
2. **Assign random pointers:**
    - For each original node, if `random` is not null, set the copied node's `random` pointer to `original.random.next`.
3. **Separate the lists:**
    - Restore the original list and extract the copied list by adjusting the `next` pointers.

### Java Code

```java
public Node copyRandomList(Node head) {
    if (head == null) return null;

    // Step 1: Create new nodes interleaved with original nodes
    Node current = head;
    while (current != null) {
        Node copy = new Node(current.val);
        copy.next = current.next;
        current.next = copy;
        current = copy.next;
    }

    // Step 2: Assign random pointers
    current = head;
    while (current != null) {
        if (current.random != null) {
            current.next.random = current.random.next;
        }
        current = current.next.next;
    }

    // Step 3: Separate the copied list from original list
    Node pseudoHead = new Node(0);
    Node copyCurrent = pseudoHead;
    current = head;
    while (current != null) {
        Node copy = current.next;
        // Restore original list
        current.next = copy.next;
        // Append to copied list
        copyCurrent.next = copy;
        copyCurrent = copy;
        current = current.next;
    }

    return pseudoHead.next;
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, traverses the list thrice (interleaving, setting `random`, separating).
- **Space Complexity:** **O(1)**, no extra data structure used besides pointers.

### Dry Run

Input:

```
Node1(val=1) -> Node2(val=2)
Node1.random -> Node2
Node2.random -> Node1

```

- **Interleave:** List becomes:

```
Node1 -> Copy1 -> Node2 -> Copy2

```

- **Set randoms:**
    - Copy1.random = Node2.next (Copy2)
    - Copy2.random = Node1.next (Copy1)
- **Separate:**
    - Original list restored
    - Copied list: Copy1 -> Copy2

---

## Approach 3: Recursive Deep Copy (Optional/Advanced)

While less common for this problem, recursive solutions can be written, especially when combined with memoization to avoid cycles.

### Core Idea

Use recursion to copy nodes, with a hash map to prevent revisiting nodes.

### Algorithm

- Use a hash map to store already copied nodes.
- For each node:
    - If it has been copied, return the copy.
    - Else, create a new node, store it in the map.
    - Recursively set the `next` and `random` pointers.

### Java Code

```java
public Node copyRandomList(Node head) {
    Map<Node, Node> map = new HashMap<>();
    return copyNode(head, map);
}

private Node copyNode(Node node, Map<Node, Node> map) {
    if (node == null) return null;
    if (map.containsKey(node)) return map.get(node);

    Node copy = new Node(node.val);
    map.put(node, copy);
    copy.next = copyNode(node.next, map);
    copy.random = copyNode(node.random, map);
    return copy;
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, each node is visited once.
- **Space Complexity:** **O(N)**, due to recursion stack and hash map.

---

## Summary

| Approach | Time Complexity | Space Complexity | Key Points |
| --- | --- | --- | --- |
| Brute Force (Hash Map) | O(N) | O(N) | Easy to implement, uses extra space. |
| Interleaving Nodes (Optimized) | O(N) | O(1) | No extra space, clever pointer manipulation. |
| Recursive (with Hash Map) | O(N) | O(N) | Elegant, handles cycles, recursive approach. |

---

## Final Tips for Interviews

- Understand the structure of the list and the role of the `random` pointer.
- Be clear about how to handle the `random` pointers efficiently.
- Practice both the hash map approach and the interleaving technique.
- Remember edge cases: empty list, list with only one node, `null` random pointers.

---

**Happy coding!**
