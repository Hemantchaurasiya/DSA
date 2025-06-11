# Populating Next Right Pointers in Each Node

---

## Problem Overview
Given a perfect binary tree (all leaves are at the same level, and every parent has two children), connect each node's `next` pointer to its **next right node**. If there is no next right node, the `next` pointer should be set to `null`.

---

## Approach 1: Brute Force (Level Order Traversal Using Extra Space)

### Core Idea
Use level-order traversal (BFS) with a queue to connect nodes at each level. For each level, connect all nodes to their next right node in sequence.

### Algorithm
1. Initialize a queue and enqueue the root node.
2. While the queue is not empty:
   - Determine the size of the current level (`levelSize`).
   - For each node in this level:
     - Dequeue the node.
     - If it is not the last node in this level, set its `next` pointer to the next node in the queue.
     - Enqueue its children (if any).
3. Repeat until all levels are processed.

### Java Code
```java
class Node {
    public int val;
    public Node left, right, next;

    public Node() {}
    public Node(int val) { this.val = val; }
    public Node(int val, Node left, Node right, Node next) {
        this.val = val;
        this.left = left;
        this.right = right;
        this.next = next;
    }
}

public class Solution {
    public Node connect(Node root) {
        if (root == null) return null;

        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int levelSize = queue.size();

            for (int i = 0; i < levelSize; i++) {
                Node current = queue.poll();

                // Connect to next node in the same level if it exists
                if (i < levelSize - 1) {
                    current.next = queue.peek();
                }

                // Enqueue children
                if (current.left != null) queue.offer(current.left);
                if (current.right != null) queue.offer(current.right);
            }
        }
        return root;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)** — Each node is processed exactly once.
- **Space Complexity:** **O(N)** — Queue can hold up to all nodes at the last level in the worst case.

### Dry Run
**Input:**

```
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

**Process:**

| Step | Queue (before processing level) | Nodes processed | Next pointers set | Queue (after enqueuing children) |
|-------|----------------------------------|-------------------|-------------------|-----------------------------------|
| 1     | [1]                              | 1                 | 1.next = null    | [2, 3]                          |
| 2     | [2, 3]                           | 2, 3              | 2.next = 3       | [4, 5, 6, 7]                     |
| 3     | [4, 5, 6, 7]                     | 4, 5, 6, 7        | 4.next = 5, 5.next=6, 6.next=7 | [] (leaf nodes)                     |

---

## Approach 2: Using Established `next` Pointers (Constant Space)

### Core Idea
Since the tree is perfect, once you connect the nodes at one level, you can use those connections to connect the next level without using extra space.  
Start from the root, connect the child nodes, then move down level by level.

### Algorithm
1. Initialize a pointer `leftmost` at the root.
2. While `leftmost.left != null`:
   - For each node (`head`) at the current level:
     - Connect `head.left.next = head.right`.
     - If `head.next != null`, connect `head.right.next = head.next.left`.
     - Move `head` to `head.next`.
   - Move `leftmost` down to the next level (`leftmost = leftmost.left`).

### Java Code
```java
public class Solution {
    public Node connect(Node root) {
        if (root == null) return null;

        Node leftmost = root;

        while (leftmost.left != null) {
            Node head = leftmost;

            while (head != null) {
                // Connect left to right
                head.left.next = head.right;

                // Connect right to next node's left child if exists
                if (head.next != null) {
                    head.right.next = head.next.left;
                }

                head = head.next;
            }
            leftmost = leftmost.left; // move down to next level
        }
        return root;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)** — Each node is visited once.
- **Space Complexity:** **O(1)** — No extra data structure apart from pointers.

### Dry Run
**Input:**

```
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

**Process:**

| Level | `leftmost` | Connection steps | Next pointers established | Move to next level |
|---------|--------------|---------------------|----------------------------|---------------------|
| Level 1 | Node 1       | 1.left.next=2, 1.right.next=3 | 2.next=3 | `leftmost=2` |
| Level 2 | Node 2       | 2.left.next=4, 2.right.next=5, 3.left.next=6, 3.right.next=7 | 4.next=5, 6.next=7 | `leftmost=4` |
| Level 3 | Node 4       | No further connections (leaf level) | - | End |

---

## Approach 3: Recursive Solution (Elegant but similar to iterative constant space)

### Core Idea
Recursively connect children, leveraging the properties of a perfect binary tree.

### Algorithm
- Recursively connect the left and right children.
- Connect `node.left.next = node.right`.
- If `node.next != null`, connect `node.right.next = node.next.left`.
- Recursion proceeds until leaf nodes are reached.

### Java Code
```java
public class Solution {
    public Node connect(Node root) {
        if (root == null || root.left == null) return root;

        // Connect left and right child
        root.left.next = root.right;

        // Connect right child to next subtree's left child if exists
        if (root.next != null) {
            root.right.next = root.next.left;
        }

        // Recurse for subtrees
        connect(root.left);
        connect(root.right);

        return root;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)** — Each node is visited once.
- **Space Complexity:** **O(h)**, where `h` is the height of the tree, due to recursion stack (`O(log N)` for perfect binary tree).

### Dry Run
For the same input:
- Connect node 2 and 3 under node 1.
- Connect 4 to 5, 6 to 7.
- Recursively process subtrees, establishing all `next` pointers.

---

## Summary
| Approach | Time Complexity | Space Complexity | Key Idea |
|------------|-------------------|---------------------|--------------|
| **Brute Force (Level Order BFS)** | O(N) | O(N) | Use a queue to process level-by-level. |
| **Constant Space Iterative** | O(N) | O(1) | Use existing `next` pointers to traverse levels. |
| **Recursive** | O(N) | O(h) | Recursively connect nodes leveraging perfect binary tree properties. |

---

## Final Tips for Interviews
- Recognize the tree is *perfect*, which enables optimized solutions.
- Use level pointers to traverse without extra space.
- Be mindful of connecting nodes across subtrees.
- Practice dry runs with small trees to understand pointer manipulations.

---

**Happy Coding!**
