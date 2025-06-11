# Symmetric Tree

This guide provides a comprehensive overview of solving the **"Symmetric Tree"** problem, from brute-force to optimal solutions. It's structured for quick revision and interview preparation.

---

## Problem Statement
Given the **root** of a binary tree, determine if the tree is **mirror symmetric** around its center.

---

## Approach 1: Brute Force (Recursive Comparison of Subtrees)

### Core Idea
To check if a tree is symmetric, compare the left and right subtrees for mirror symmetry. The brute-force approach involves recursively comparing nodes of the left subtree with corresponding nodes of the right subtree.

### Algorithm
1. Define a recursive helper function that takes two nodes.
2. If both nodes are `null`, return `true`.
3. If only one is `null`, return `false`.
4. Check if current nodes' values are equal.
5. Recursively check:
    - The left child of the first node with the right child of the second node.
    - The right child of the first node with the left child of the second node.
6. If all comparisons return `true`, the tree is symmetric.

### Java Code
```java
public class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return isMirror(root.left, root.right);
    }
    
    private boolean isMirror(TreeNode t1, TreeNode t2) {
        if (t1 == null && t2 == null) return true;
        if (t1 == null || t2 == null) return false;
        return (t1.val == t2.val)
            && isMirror(t1.left, t2.right)
            && isMirror(t1.right, t2.left);
    }
}
```

### Complexity Analysis
- **Time Complexity:** `O(n)`  
  Each node is visited once during the recursion.
- **Space Complexity:** `O(h)`  
  Due to recursive call stack, where `h` is the height of the tree (`O(n)` in the worst case for skewed trees).

### Dry Run Example
```
Input Tree:
        1
       / \
      2   2
     /     \
    3       3

Steps:
- Compare root.left (2) with root.right (2): same value -> continue
- Compare (2.left -> 3) with (2.right -> 3): same value -> continue
- Compare (2.right -> null) with (2.left -> null): both null -> true
- Compare (3.left -> null) with (3.right -> null): both null -> true
Result: The tree is symmetric.
```

---

## Approach 2: Iterative BFS (Using Queue)

### Core Idea
Use a queue to perform level-order traversal, comparing nodes in pairs to verify mirror symmetry without recursion.

### Algorithm
1. Initialize a queue and enqueue `root.left` and `root.right`.
2. While the queue is not empty:
   - Dequeue two nodes at a time.
   - If both are `null`, continue.
   - If only one is `null` or values differ, return `false`.
   - Enqueue the children in order:
     - left child of the first node
     - right child of the second node
     - right child of the first node
     - left child of the second node
3. If all pairs are symmetric, return `true`.

### Java Code
```java
import java.util.LinkedList;
import java.util.Queue;

public class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root.left);
        queue.offer(root.right);
        
        while (!queue.isEmpty()) {
            TreeNode t1 = queue.poll();
            TreeNode t2 = queue.poll();
            if (t1 == null && t2 == null) continue;
            if (t1 == null || t2 == null || t1.val != t2.val) return false;
            queue.offer(t1.left);
            queue.offer(t2.right);
            queue.offer(t1.right);
            queue.offer(t2.left);
        }
        return true;
    }
}
```

### Complexity Analysis
- **Time Complexity:** `O(n)`  
  Each node is processed once.
- **Space Complexity:** `O(n)`  
  Due to the queue storing nodes at each level.

### Dry Run Example
```
Input Tree:
        1
       / \
      2   2
     /     \
    3       3

Steps:
- Enqueue: (2, 2)
- Dequeue: (2, 2): same values -> enqueue:
  (3, 3), (null, null)
- Dequeue: (3, 3): same values -> enqueue:
  (null, null), (null, null)
- Next pairs: nulls -> continue
Result: symmetric.
```

---

## Approach 3: Optimized Recursive Solution (Using Symmetry Checks)

**Note:** This approach is similar to Approach 1 but emphasizes clean code and clarity. It is considered optimal for recursive solutions.

### Core Idea
Same as Approach 1, but with increased emphasis on code clarity and minimal auxiliary data structures.

### Algorithm
- Same recursive mirror comparison as Approach 1.

### Java Code
*(Same as Approach 1)*

---

## Summary Table

| Approach | Core Idea | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|--------------|------------------|-------------------|---------------------|--------|
| Brute Force (Recursive) | Compare left and right subtrees recursively | Call Stack (recursion) | `O(n)` | `O(h)` | Simple, intuitive, but recursion depth can be high |
| Iterative BFS | Use a queue to compare node pairs level-by-level | Queue | `O(n)` | `O(n)` | Avoids recursion, suitable for very deep trees |

---

## Final Tips
- Always check for `null` nodes before comparisons.
- Use symmetry properties: left of one subtree with right of the other.
- For large trees, iterative solutions prevent stack overflow.
- Test with edge cases:
  - Empty tree (`null`)
  - Single node tree
  - Unbalanced trees
  - Trees with duplicate values

---

This concludes the **"Symmetric Tree"** problem revision. Master these approaches for a solid understanding and quick recall during interviews!
