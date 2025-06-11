# Invert Binary Tree - DSA Revision Note

## Problem Overview
Given the root of a binary tree, invert the tree, i.e., swap the left and right children of all nodes in the tree.

---

## Approach 1: Brute Force (Recursive Traversal with Swapping)

### Core Idea
Use recursion to traverse the entire tree. At each node, swap its left and right children, then recursively invert the subtrees.

### Algorithm
1. If the current node is `null`, return `null`.
2. Recursively invert the left subtree.
3. Recursively invert the right subtree.
4. Swap the left and right child pointers of the current node.
5. Return the current node as the root of the inverted subtree.

### Java Code
```java
public class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}

public class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;
        
        // Invert left and right subtrees
        TreeNode leftInverted = invertTree(root.left);
        TreeNode rightInverted = invertTree(root.right);
        
        // Swap left and right children
        root.left = rightInverted;
        root.right = leftInverted;
        
        return root;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(n)**, where n is the number of nodes in the tree, as each node is visited exactly once.
- **Space Complexity:** **O(h)**, where h is the height of the tree, due to the recursion stack.

---

### Dry Run Example
**Input Tree:**
```
     4
    / \
   2   7
  / \ / \
 1  3 6  9
```

**Step-by-step:**
1. Start at node 4:
   - Invert left subtree rooted at 2.
   - Invert right subtree rooted at 7.
2. At node 2:
   - Invert left (1), right (3).
   - Swap children: 1 and 3.
3. At node 7:
   - Invert left (6), right (9).
   - Swap children: 6 and 9.
4. After recursion, swap children of 4: left becomes subtree rooted at 7, right becomes subtree rooted at 2.
   
**Resulting Tree:**
```
     4
    / \
   7   2
  / \ / \
  9 6 3  1
```

---

## Approach 2: Iterative Solution Using Stack (Explicit Stack DFS)

### Core Idea
Use an explicit stack to perform DFS traversal iteratively, swapping children at each node.

### Algorithm
1. Initialize a stack and push the root node.
2. While the stack is not empty:
   - Pop a node.
   - Swap its left and right children.
   - Push the non-null children onto the stack.
3. Return the root after processing.

### Java Code
```java
import java.util.Stack;

public class Solution {
    public TreeNode invertTree(TreeNode root) {
        if (root == null) return null;

        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);

        while (!stack.isEmpty()) {
            TreeNode current = stack.pop();

            // Swap children
            TreeNode temp = current.left;
            current.left = current.right;
            current.right = temp;

            // Push children if not null
            if (current.left != null) stack.push(current.left);
            if (current.right != null) stack.push(current.right);
        }
        return root;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(n)**, as each node is visited once.
- **Space Complexity:** **O(h)**, due to the stack, where h is the height of the tree.

---

## Approach 3: Morris Traversal (Threaded Tree - O(1) Space)

**Note:** This approach is more complex and less common for this problem. It involves threaded binary trees to achieve O(1) space, but it's rarely necessary for invert tree problem.

---

# Summary

| Approach | Core Idea | Key Steps | Time Complexity | Space Complexity |
|------------|--------------|--------------|------------------|------------------|
| Recursive | Post-order traversal, swap children | Recursively invert left/right, swap | **O(n)** | **O(h)** (recursion stack) |
| Iterative | Use stack for DFS | Push nodes, swap children, process | **O(n)** | **O(h)** (stack) |
| Morris (Advanced) | Threaded traversal, O(1) space | Threaded traversal, swap children during traversal | **O(n)** | **O(1)** |

---

## Final Tips for Interview
- Recursive solution is most straightforward and easiest to implement.
- Be mindful of base cases (`null` nodes).
- Understand the recursive tree traversal pattern (post-order) for similar problems.
- For large trees or space constraints, consider iterative solutions.

---

**Happy Coding!**
