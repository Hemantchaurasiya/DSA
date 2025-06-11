# 114. Flatten Binary Tree to Linked List

---

## Problem Summary

Given a binary tree, flatten it to a linked list **in-place** following the preorder traversal. The flattened tree should resemble a singly linked list where the right child pointer points to the next node in preorder, and the left child is always `null`.

---

## Approach 1: Brute Force (Using Extra Space)

### Core Idea

Perform a preorder traversal, store the node values in an array/list, then reconstruct the flattened tree using this sequence.

### Algorithm

1. **Traverse** the tree in preorder (root-left-right), collecting nodes or values into a list.
2. **Reconstruct** the tree:
    - Iterate through the list.
    - For each node:
        - Set its left child to `null`.
        - Set its right child to the next node in the list.
3. The original tree structure is overwritten in-place.

### Java Code

```java
class Solution {
    List<TreeNode> nodes = new ArrayList<>();

    public void flatten(TreeNode root) {
        if (root == null) return;
        // Step 1: Preorder traversal and store nodes
        dfs(root);
        // Step 2: Reconstruct the list
        for (int i = 0; i < nodes.size() - 1; i++) {
            TreeNode current = nodes.get(i);
            TreeNode next = nodes.get(i + 1);
            current.left = null;
            current.right = next;
        }
        // Last node's left and right are already null
    }

    private void dfs(TreeNode node) {
        if (node == null) return;
        nodes.add(node);
        dfs(node.left);
        dfs(node.right);
    }
}

```

### Complexity Analysis

- **Time Complexity:**
    
    O(N), where N is the number of nodes, since we traverse all nodes once during traversal and reconstruction.
    
- **Space Complexity:**
    
    O(N), due to the auxiliary list storing all nodes.
    

### Dry Run

Suppose the tree:

```
    1
   / \\
  2   5
 / \\   \\
3   4   6

```

- Preorder traversal sequence: [1, 2, 3, 4, 5, 6]
- After reconstruction:

```
1 - 2 - 3 - 4 - 5 - 6

```

with all left pointers `null`.

---

## Approach 2: In-place Using Reverse Postorder Traversal (Optimal)

### Core Idea

Use a **reverse preorder traversal** (right -> left -> root) to modify pointers in-place, avoiding extra space.

### Intuition

- During traversal, keep track of the **previous node** processed.
- For each node:
    - Make the current node's right pointer point to the previously processed node.
    - Set its left pointer to `null`.
- This way, we link nodes in the correct order in a single pass.

### Algorithm

1. Initialize a `TreeNode` variable `prev` as `null`.
2. Perform a **reverse preorder traversal**:
    - Traverse right subtree.
    - Traverse left subtree.
    - Process current node:
        - Set `node.right = prev`.
        - Set `node.left = null`.
        - Update `prev = node`.

### Java Code

```java
class Solution {
    private TreeNode prev = null;

    public void flatten(TreeNode root) {
        if (root == null) return;
        // Traverse right subtree
        flatten(root.right);
        // Traverse left subtree
        flatten(root.left);
        // Process current node
        root.right = prev;
        root.left = null;
        prev = root;
    }
}

```

### Complexity Analysis

- **Time Complexity:**
    
    O(N), as each node is visited exactly once.
    
- **Space Complexity:**
    
    O(1), ignoring recursion stack space, since in-place modification is used.
    

### Dry Run

Using the same tree:

```
    1
   / \\
  2   5
 / \\   \\
3   4   6

```

- Start at root (1):
    - Recurse right (5):
        - Recurse right (6):
            - Recurse right (`null`): return
            - Recurse left (`null`): return
            - Process 6: `6.right = prev (null)`, `6.left = null`, `prev = 6`
        - Recurse left (4):
            - Recurse right (`null`): return
            - Recurse left (`null`): return
            - Process 4: `4.right = prev (6)`, `4.left = null`, `prev = 4`
        - Process 5: `5.right = prev (4)`, `5.left = null`, `prev = 5`
    - Recurse left (2):
        - Recurse right (4): already processed, prev = 5, but since we've traversed fully, no change.
        - Recurse left (3):
            - Recurse right (`null`)
            - Recurse left (`null`)
            - Process 3: `3.right = prev (5)`, `3.left = null`, `prev = 3`
        - Process 2: `2.right = prev (3)`, `2.left = null`, `prev = 2`
    - Process root (1): `1.right = prev (2)`, `1.left = null`, `prev = 1`

Resulting list:

```
1 - 2 - 3 - 4 - 5 - 6

```

---

## Summary Table

| Approach | Core Idea | Advantages | Disadvantages | Time | Space |
| --- | --- | --- | --- | --- | --- |
| **Brute Force** | Store nodes in a list, then reconstruct | Simple to understand | Uses extra space | O(N) | O(N) |
| **In-place Reverse Traversal** | Modify pointers during reverse preorder | O(1) space, efficient | Slightly tricky to implement correctly | O(N) | O(1) |

---

## Final Tips for Interview

- Aim for the in-place approach as it is optimal.
- Remember to traverse in reverse preorder (right-left-root) for in-place flattening.
- Use a class member variable `prev` to keep track of the last processed node.
- Always set `left` pointers to `null` to satisfy the problem constraints.

---

Feel free to review this guide for quick revision before your interview!
