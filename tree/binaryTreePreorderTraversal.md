# Binary Tree Preorder Traversal

This guide covers various approaches to solve the **Binary Tree Preorder Traversal** problem, progressing from brute-force to optimal solutions. It includes detailed explanations, algorithms, Java implementations, and dry runs to facilitate quick revision and solid understanding.

---

## Problem Statement
Given the root of a binary tree, return the preorder traversal of its nodes' values.

**Preorder Traversal Order:** Root -> Left -> Right

---

## Approach 1: Brute Force (Recursive Traversal)

### Core Idea
Use recursion to traverse the tree in preorder fashion and store the node values in a list.

### Algorithm
1. Initialize an empty list `result`.
2. Define a recursive function `dfs(node)`:
   - If `node` is `null`, return.
   - Add `node.val` to `result`.
   - Recursively call `dfs(node.left)`.
   - Recursively call `dfs(node.right)`.
3. Call `dfs(root)` from the main function.
4. Return `result`.

### Java Code
```java
import java.util.*;

public class PreorderTraversal {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        dfs(root, result);
        return result;
    }
    
    private void dfs(TreeNode node, List<Integer> result) {
        if (node == null) return;
        result.add(node.val);
        dfs(node.left, result);
        dfs(node.right, result);
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, where N is the number of nodes, since each node is visited exactly once.
- **Space Complexity:** **O(N)**, for the recursion stack in the worst case (skewed tree) and the output list.

### Dry Run
**Input Tree:**
```
      1
       \
        2
       /
      3
```
**Process:**
- Start at root (1): add 1.
- Recurse left: null, do nothing.
- Recurse right (2): add 2.
- Recurse left (3): add 3.
- Recurse left/right of 3: null, do nothing.

**Result:** `[1, 2, 3]`

---

## Approach 2: Iterative Solution Using Stack

### Core Idea
Simulate recursion using a stack to traverse nodes in preorder without function call overhead.

### Algorithm
1. Initialize a stack and push the root node.
2. While stack is not empty:
   - Pop the top node.
   - Add its value to the result list.
   - Push its right child (if exists) onto the stack.
   - Push its left child (if exists) onto the stack.
3. Return the result list.

### Java Code
```java
import java.util.*;

public class PreorderTraversal {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root == null) return result;
        
        Deque<TreeNode> stack = new ArrayDeque<>();
        stack.push(root);
        
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            result.add(node.val);
            if (node.right != null) stack.push(node.right);
            if (node.left != null) stack.push(node.left);
        }
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, each node is processed once.
- **Space Complexity:** **O(N)**, for the stack in the worst case (skewed tree).

### Dry Run
**Input Tree:**
```
      1
       \
        2
       /
      3
```
**Process:**
- Stack: [1]
- Pop 1: add 1 to result. Push 2 (right), null (left).
- Stack: [2]
- Pop 2: add 2. Push 3 (left), null (right).
- Stack: [3]
- Pop 3: add 3. No children to push.
- Stack: []

**Result:** `[1, 2, 3]`

---

## Approach 3: Morris Traversal (Threaded Binary Tree)

### Core Idea
Use a threaded binary tree to perform preorder traversal with **O(1)** space complexity (excluding output), by temporarily modifying the tree during traversal.

### Algorithm
1. Initialize `current` as root.
2. While `current` is not null:
   - If `current.left` is null:
     - Visit `current` (add value to result).
     - Move `current` to `current.right`.
   - Else:
     - Find the inorder predecessor of `current` in the left subtree.
     - If the predecessor's right is null:
       - Make `current` the right child of the predecessor (thread).
       - Visit `current`.
       - Move `current` to `current.left`.
     - Else:
       - Remove the thread (set predecessor's right to null).
       - Move `current` to `current.right`.

### Java Code
```java
import java.util.*;

public class PreorderTraversal {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        TreeNode current = root;
        
        while (current != null) {
            if (current.left == null) {
                result.add(current.val);
                current = current.right;
            } else {
                TreeNode predecessor = current.left;
                while (predecessor.right != null && predecessor.right != current) {
                    predecessor = predecessor.right;
                }
                if (predecessor.right == null) {
                    result.add(current.val); // Visit before creating thread (preorder)
                    predecessor.right = current;
                    current = current.left;
                } else {
                    predecessor.right = null; // Remove thread
                    current = current.right;
                }
            }
        }
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, each edge is visited at most twice.
- **Space Complexity:** **O(1)**, no extra space apart from output.

### Dry Run
**Input Tree:**
```
      1
       \
        2
       /
      3
```
**Process:**
- Start at 1:
  - No left child, visit 1, move to 2.
- At 2:
  - Left child (3):
    - Find predecessor of 2 in left subtree (3). 3's right is null.
    - Create thread from 3 to 2.
    - Visit 2, move to 3.
- At 3:
  - No left child, visit 3, move to threaded node 2.
- Thread from 3 to 2 exists; remove it and move to 2's right (null).

**Result:** `[1, 2, 3]`

---

# Summary Table

| Approach | Core Idea | Data Structure | Space Complexity | Pros | Cons |
|------------|--------------|------------------|---------------------|-------|-------|
| Brute Force (Recursive) | Recursion simulating preorder | Call stack | O(N) | Simple, intuitive | Risk of stack overflow for deep trees |
| Iterative Stack | Explicit stack to simulate recursion | Stack | O(N) | No recursion overhead | Extra space for stack |
| Morris Traversal | Threaded binary tree, O(1) space | Tree modification | O(1) | Space-efficient | Complex implementation, modifies tree temporarily |

---

## Final Tips for Interviews:
- Understand the recursive approach first.
- Practice the iterative stack method for better control over space.
- Be aware of Morris traversal for advanced space optimization.
- Always consider the input size and constraints to choose the optimal approach.

---

**Happy Coding!**
