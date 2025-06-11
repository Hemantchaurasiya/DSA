# Binary Tree Inorder Traversal

---

## Problem Overview
Given a binary tree, return the inorder traversal of its nodes' values.

**Inorder Traversal Order:** Left -> Root -> Right

---

## Approach 1: Brute Force (Recursive Traversal)

### 1. Core Idea:
Use recursion to traverse the tree in inorder fashion directly. The recursive approach naturally follows the inorder pattern.

### 2. Algorithm:
1. Initialize an empty list to store the traversal result.
2. Define a recursive function `inorder(node)`:
   - If `node` is null, return.
   - Recursively traverse the left subtree.
   - Add the current node's value to the result list.
   - Recursively traverse the right subtree.
3. Call the recursive function with the root node.
4. Return the result list.

### 3. Java Code:
```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

public class BinaryTreeInorderTraversal {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        inorder(root, result);
        return result;
    }

    private void inorder(TreeNode node, List<Integer> result) {
        if (node == null) return;
        inorder(node.left, result);
        result.add(node.val);
        inorder(node.right, result);
    }
}
```

### 4. Complexity Analysis:
- **Time Complexity:** **O(n)**, where n is the number of nodes, since each node is visited exactly once.
- **Space Complexity:** **O(h)**, where h is the height of the tree (due to recursion stack). In worst case (skewed tree), O(n); in balanced tree, O(log n).

### 5. Dry Run Example:
Suppose the tree:

```
        1
         \
          2
         /
        3
```

- Start at root (1):
  - Recurse left: null → return.
  - Add 1.
  - Recurse right (2):
    - Recurse left (3):
      - Recurse left: null → return.
      - Add 3.
      - Recurse right: null → return.
    - Add 2.
    - Recurse right: null → return.

**Result:** `[1, 3, 2]`

---

## Approach 2: Iterative Solution Using Stack

### 1. Core Idea:
Simulate the recursive inorder traversal using an explicit stack to avoid recursion.

### 2. Algorithm:
1. Initialize an empty stack.
2. Set `current` to root.
3. While `current` is not null or stack is not empty:
   - Push all the way down the left subtree:
     - While `current` is not null:
       - Push `current` onto the stack.
       - Move `current` to `current.left`.
   - Pop the top node from the stack.
   - Add the node's value to the result list.
   - Set `current` to the right child of the popped node.
4. Return the result list.

### 3. Java Code:
```java
public class BinaryTreeInorderTraversal {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode current = root;

        while (current != null || !stack.isEmpty()) {
            // Reach the left most Node of the current Node
            while (current != null) {
                stack.push(current);
                current = current.left;
            }
            // Current must be null at this point
            current = stack.pop();
            result.add(current.val);
            // Visit the right subtree
            current = current.right;
        }

        return result;
    }
}
```

### 4. Complexity Analysis:
- **Time Complexity:** **O(n)**, each node is visited once.
- **Space Complexity:** **O(h)** due to the stack, where h is the height of the tree.

### 5. Dry Run Example:
Using the same tree:

```
        1
         \
          2
         /
        3
```

- Initialize `current` as root (1), stack empty.
- Loop:
  - Push 1, move to left: null → stop.
  - Pop 1 → add 1 to result.
  - Move to right: 2.
- Now `current` is 2:
  - Push 2, move to left: 3.
  - Push 3, move to left: null → stop.
- Pop 3 → add 3.
- Move to right of 3: null.
- Pop 2 → add 2.
- Move to right of 2: null.

**Result:** `[1, 3, 2]`

---

## Approach 3: Morris Inorder Traversal (Optimized, O(1) Space)

### 1. Core Idea:
Use threaded binary tree concept to perform inorder traversal without recursion or stack, modifying the tree temporarily.

### 2. Algorithm:
1. Initialize `current` as root.
2. While `current` is not null:
   - If `current.left` is null:
     - Add `current.val` to result.
     - Move to `current.right`.
   - Else:
     - Find the predecessor of `current` in its left subtree (rightmost node in `current.left`):
       - If predecessor's right is null:
         - Set predecessor's right to `current` (create a thread).
         - Move `current` to `current.left`.
       - Else (thread exists):
         - Remove the thread (set predecessor's right to null).
         - Add `current.val` to result.
         - Move `current` to `current.right`.

### 3. Java Code:
```java
public class BinaryTreeInorderTraversal {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        TreeNode current = root;

        while (current != null) {
            if (current.left == null) {
                result.add(current.val);
                current = current.right;
            } else {
                // Find predecessor
                TreeNode predecessor = current.left;
                while (predecessor.right != null && predecessor.right != current) {
                    predecessor = predecessor.right;
                }
                if (predecessor.right == null) {
                    // Create thread
                    predecessor.right = current;
                    current = current.left;
                } else {
                    // Thread exists, remove it
                    predecessor.right = null;
                    result.add(current.val);
                    current = current.right;
                }
            }
        }
        return result;
    }
}
```

### 4. Complexity Analysis:
- **Time Complexity:** **O(n)**, each node is visited at most twice.
- **Space Complexity:** **O(1)**, no extra space apart from variables.

### 5. Dry Run Example:
Same tree:

```
        1
         \
          2
         /
        3
```

- Start at 1:
  - Left null → add 1, move right to 2.
- At 2:
  - Left exists (3), find predecessor (3):
    - 3's right null → create thread to 2, move to 3.
- At 3:
  - Left null → add 3, move right (thread to 2):
    - Thread exists → remove thread, add 2, move right (null).

**Result:** `[1, 3, 2]`

---

# Summary Table

| Approach | Core Idea | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|--------------|---------------------|-------------------|-------------------|--------|
| Brute Force (Recursive) | Direct recursion following inorder pattern | Call stack | O(n) | O(h) | Easy to implement, clear |
| Iterative (Stack) | Simulate recursion with explicit stack | Stack | O(n) | O(h) | Avoids recursion, iterative approach |
| Morris Traversal | Threaded binary tree, no extra space | No extra data structure | O(n) | O(1) | Slightly complex, modifies tree temporarily |

---

## Final Tips:
- For **interview**: Prefer the iterative approach with a stack for clarity and control.
- For **space optimization**: Morris traversal is best but more complex.
- Always verify edge cases: empty tree, skewed trees, balanced trees.

---

**Happy Coding & Interview Prep!**
