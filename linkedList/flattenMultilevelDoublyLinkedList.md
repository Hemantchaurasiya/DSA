# 430. Flatten Multilevel List

## Problem Overview

You are given a multilevel doubly linked list where each node has:

- `val`: integer value
- `prev`: pointer to previous node
- `next`: pointer to next node
- `child`: pointer to a child doubly linked list

Your goal is to **flatten** this list into a single-level doubly linked list in a depth-first manner.

---

## Approach 1: Brute Force (Using Recursion with Extra Space)

### Core Idea

Use recursion to process each node and its children, flattening the list in a depth-first manner and reconstructing the list as we go.

### Algorithm

1. Create a recursive function `flattenList(node)`:
    - Initialize `current` as `node`.
    - Maintain a pointer `tail` to the last node in the flattened list.
2. Iterate through the list:
    - If `current.child` exists:
        - Recursively flatten the `child` list.
        - Connect the flattened child list between `current` and `current.next`.
        - Set `current.child` to `null`.
        - Move `current` to the end of the flattened child list.
    - Move `current` to `current.next`.
3. Return the head of the flattened list.

### Java Code

```java
class Node {
    public int val;
    public Node prev;
    public Node next;
    public Node child;
}

public class Solution {
    public Node flatten(Node head) {
        flattenList(head);
        return head;
    }

    private Node flattenList(Node node) {
        Node current = node;
        Node last = null; // Tracks the last node in the flattened list
        while (current != null) {
            if (current.child != null) {
                // Recursively flatten the child list
                Node childHead = current.child;
                Node childTail = flattenList(childHead);
                // Connect current node to child head
                Node nextNode = current.next;
                current.next = childHead;
                childHead.prev = current;
                // Connect child tail to next node
                if (nextNode != null) {
                    childTail.next = nextNode;
                    nextNode.prev = childTail;
                }
                // Remove child pointer
                current.child = null;
                last = childTail;
                current = nextNode;
            } else {
                last = current;
                current = current.next;
            }
        }
        return last; // returning the last node of the flattened list
    }
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, where `N` is the total number of nodes, because each node is visited once.
- **Space Complexity:** **O(N)** in the worst case due to recursion stack in a deeply nested list.

### Dry Run

**Input:**

```
1 - 2 - 3
        |
        7 - 8
             |
             11 - 12

```

**Step-by-step:**

| Step | Current Node | Action | List State | Next Node | Notes |
| --- | --- | --- | --- | --- | --- |
| Start | 1 | No child | 1 - 2 - 3 | 2 | Traverse normally |
| Move to 2 | 2 | No child | 1 - 2 - 3 | 3 | Traverse normally |
| Move to 3 | 3 | Has child (7) | 1 - 2 - 3 | 7 | Flatten child list starting at 7 |
| Flatten child (7) | 7 | No child | 7 - 8 | 8 | Continue traversal |
| Move to 8 | 8 | Has child (11) | 7 - 8 | 11 | Flatten child starting at 11 |
| Flatten child (11) | 11 | No child | 11 - 12 | 12 | Continue traversal |
| Finish | 12 | No child | End | null | End of flattening |

The final list: **1 - 2 - 3 - 7 - 8 - 11 - 12**

---

## Approach 2: Iterative Solution Using a Stack (Better Solution)

### Core Idea

Use an explicit stack to handle nodes, avoiding recursion:

- Push `next` nodes onto the stack when encountering a `child`.
- Process nodes in a depth-first manner by popping from the stack.

### Algorithm

1. Initialize a stack and push the head node.
2. Maintain a `prev` pointer to build the flattened list.
3. While the stack is not empty:
    - Pop the top node as `curr`.
    - If `curr.next` exists, push it onto the stack.
    - If `curr.child` exists:
        - Push `curr.child` onto the stack.
        - Set `curr.child` to `null`.
    - Connect `prev.next` to `curr` and `curr.prev` to `prev`.
    - Update `prev` to `curr`.
4. Return the original head.

### Java Code

```java
import java.util.Stack;

public class Solution {
    public Node flatten(Node head) {
        if (head == null) return null;
        Stack<Node> stack = new Stack<>();
        stack.push(head);
        Node prev = null;

        while (!stack.isEmpty()) {
            Node curr = stack.pop();

            if (prev != null) {
                prev.next = curr;
                curr.prev = prev;
            }

            // If next exists, push it onto the stack
            if (curr.next != null) {
                stack.push(curr.next);
            }

            // If child exists, push it onto the stack
            if (curr.child != null) {
                stack.push(curr.child);
                curr.child = null; // Remove child pointer
            }

            prev = curr;
        }

        return head;
    }
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, as each node is processed once.
- **Space Complexity:** **O(N)** due to the stack in the worst case.

### Dry Run

**Input:**

```
1 - 2 - 3
        |
        7 - 8
             |
             11 - 12

```

**Execution:**

| Step | Stack | Current Node | List State | Notes |
| --- | --- | --- | --- | --- |
| 1 | [1] | 1 | Initialize |  |
| 2 | [2, 3, 7] | 1 | 1 connected to previous | Push next(2), child(7) |
| 3 | [3, 7] | 2 | 1 - 2 |  |
| 4 | [3, 8] | 2 | 1 - 2 | Push next(3), child(8) |
| 5 | [8, 3] | 3 | 1 - 2 - 3 |  |
| 6 | [11, 3] | 8 | 1 - 2 - 3 - 8 | Push child(11) |
| 7 | [12, 11, 3] | 11 | 1 - 2 - 3 - 8 - 11 | Push child(12) |
| 8 | [12, 3] | 12 | 1 - 2 - 3 - 8 - 11 - 12 | No children, continue |

Final flattened list: **1 - 2 - 3 - 7 - 8 - 11 - 12**

---

## Approach 3: Most Optimal Solution (In-Place, O(1) Extra Space)

### Core Idea

Use **in-place** traversal with `next` and `child` pointers:

- Iterate through the list.
- When a node with a child is found:
    - Find the tail of the child list.
    - Connect the tail to the `next` node.
    - Connect current node to the child.
    - Remove the `child` pointer.
- Continue traversal until the end.

### Algorithm

1. Initialize `curr` as `head`.
2. While `curr` is not null:
    - If `curr.child` exists:
        - Find the tail of the child list.
        - Connect tail's `next` to `curr.next`.
        - Connect `curr.next` to `curr.child`.
        - Set `curr.child.prev` to `curr`.
        - Remove `curr.child`.
    - Move to `curr.next`.
3. Return `head`.

### Java Code

```java
public class Solution {
    public Node flatten(Node head) {
        Node curr = head;
        while (curr != null) {
            if (curr.child != null) {
                // Find tail of child list
                Node tail = curr.child;
                while (tail.next != null) {
                    tail = tail.next;
                }
                // Connect tail to next node
                if (curr.next != null) {
                    tail.next = curr.next;
                    curr.next.prev = tail;
                }
                // Connect current to child
                curr.next = curr.child;
                curr.child.prev = curr;
                // Remove child pointer
                curr.child = null;
            }
            curr = curr.next;
        }
        return head;
    }
}

```

### Complexity Analysis

- **Time Complexity:** **O(N)**, each node is visited once.
- **Space Complexity:** **O(1)**, no extra space used apart from pointers.

### Dry Run

**Input:**

```
1 - 2 - 3
        |
        7 - 8
             |
             11 - 12

```

**Execution:**

- Start at `1`: no child, move to `2`.
- At `2`: no child, move to `3`.
- At `3`: has child `7`.
    - Find tail of child (`8`), connect tail to `3.next` (`null`).
    - Connect `3.next` to `7`.
    - Remove `child` pointer.
- Continue traversal:
    - `7` has no child, move to `8`.
    - At `8`: has child `11`, find tail (`12`), connect tail to `8.next`.
    - Remove `child` pointer.
- End.

Final list: **1 - 2 - 3 - 7 - 8 - 11 - 12**

---

## Summary Table

| Approach | Core Idea | Data Structure | Extra Space | Use Case | Code Complexity |
| --- | --- | --- | --- | --- | --- |
| Brute Force | Recursion with helper | Recursion | O(N) | Small or moderate nesting | Easy to implement |
| Stack-based | Iterative DFS | Stack | O(N) in worst case | Large nesting, iterative preferred | Slightly complex |
| In-Place | Pointer manipulation | No extra space | O(1) | Space-constrained environments | Most efficient |

---

## Final Notes

- The **most optimal** solution is the **in-place traversal** with pointer relinking, offering **O(1)** space complexity.
- Always consider the list's depth and nesting to choose the suitable approach.
- Properly handle `null` pointers and edge cases such as empty lists or lists without children.

---

**Happy Coding!**
