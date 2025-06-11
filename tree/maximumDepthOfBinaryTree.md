# Maximum Depth of Binary Tree - DSA Revision Note

This guide provides a comprehensive overview of approaches to solve the **Maximum Depth of Binary Tree** problem, suitable for interview prep and quick revision.

---

## Problem Statement
Given the root of a binary tree, return its maximum depth. The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

---

## Approach 1: Brute Force (Recursive DFS)

### Core Idea
Traverse all paths from the root to leaf nodes, calculating the depth of each path, and return the maximum depth encountered.

### Algorithm
1. If the current node is `null`, return 0 (base case).
2. Recursively find the maximum depth of the left subtree.
3. Recursively find the maximum depth of the right subtree.
4. The depth at the current node is `1 + max(leftDepth, rightDepth)`.
5. Return this value.

### Java Code
```java
public class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

public int maxDepth(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int leftDepth = maxDepth(root.left);
    int rightDepth = maxDepth(root.right);
    return 1 + Math.max(leftDepth, rightDepth);
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**  
  Each node is visited exactly once during the traversal.
- **Space Complexity:** **O(H)** (call stack space)  
  Where `H` is the height of the tree, due to recursion stack.

### Dry Run
Suppose the tree:

```
       1
      / \
     2   3
    / 
   4
```

- Start at root (1):
  - Compute maxDepth of left subtree (2):
    - Compute maxDepth of 2’s left (4):
      - 4 is leaf: returns 1
    - 2’s right is null: returns 0
    - maxDepth for 2: 1 + max(1, 0) = 2
  - Compute maxDepth of right subtree (3):
    - 3 is leaf: returns 1
  - maxDepth for root (1): 1 + max(2, 1) = 3

---

## Approach 2: Iterative BFS (Level-Order Traversal)

### Core Idea
Use level-order traversal (BFS) to traverse the tree level by level. The number of levels traversed equals the maximum depth.

### Algorithm
1. Initialize a queue and enqueue the root node.
2. Initialize `depth = 0`.
3. While the queue is not empty:
   - Increment `depth`.
   - For all nodes at the current level:
     - Dequeue node.
     - Enqueue its children if they exist.
4. After traversal, `depth` will hold the maximum depth.

### Java Code
```java
import java.util.LinkedList;
import java.util.Queue;

public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    int depth = 0;

    while (!queue.isEmpty()) {
        int levelSize = queue.size();
        for (int i = 0; i < levelSize; i++) {
            TreeNode current = queue.poll();
            if (current.left != null) queue.offer(current.left);
            if (current.right != null) queue.offer(current.right);
        }
        depth++;
    }
    return depth;
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**  
  Each node is enqueued and dequeued exactly once.
- **Space Complexity:** **O(N)** in the worst case (if the tree is skewed, queue can hold all nodes at the last level).

### Dry Run
Using the same example:
- Start with root (1): queue = [1], depth=0
- Level 1:
  - Dequeue 1, enqueue 2 and 3
  - depth=1
- Level 2:
  - Dequeue 2, enqueue 4
  - Dequeue 3
  - depth=2
- Level 3:
  - Dequeue 4
  - queue empty, depth=3

---

## Approach 3: Optimized DFS (Iterative)

### Core Idea
Use an explicit stack to simulate recursion, storing nodes along with their depth.

### Algorithm
1. Use a stack to store pairs of `(node, currentDepth)`.
2. Initialize stack with `(root, 1)` if root is not null.
3. Track maximum depth encountered.
4. While stack is not empty:
   - Pop a node-depth pair.
   - Update maximum depth if current depth is greater.
   - Push children with depth + 1.

### Java Code
```java
import java.util.Stack;

public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
    stack.push(new Pair<>(root, 1));
    int maxDepth = 0;

    while (!stack.isEmpty()) {
        Pair<TreeNode, Integer> current = stack.pop();
        TreeNode node = current.getKey();
        int depth = current.getValue();
        maxDepth = Math.max(maxDepth, depth);
        if (node.left != null) {
            stack.push(new Pair<>(node.left, depth + 1));
        }
        if (node.right != null) {
            stack.push(new Pair<>(node.right, depth + 1));
        }
    }
    return maxDepth;
}
```

Note: Use `AbstractMap.SimpleEntry` or a custom pair class if `Pair` is unavailable.

---

## Summary Table

| Approach                  | Method         | Time Complexity | Space Complexity | Key Idea                                              |
|---------------------------|----------------|-------------------|------------------|--------------------------------------------------------|
| Brute Force               | Recursive DFS  | O(N)              | O(H)             | Explore all paths, return max depth                     |
| Level-Order Traversal     | BFS            | O(N)              | O(N)             | Traverse level by level, depth equals number of levels |
| Iterative DFS with Stack  | Stack + Pair   | O(N)              | O(H)             | Simulate recursion, track depth explicitly             |

---

## Final Tips
- The recursive DFS solution is the most straightforward and commonly used.
- BFS is useful if you want to find the depth level-wise.
- Be mindful of the recursion stack depth in case of very deep trees.
- Optimize space by choosing iterative solutions if recursion stack overflow is a concern.

---

**End of Revision Note**
