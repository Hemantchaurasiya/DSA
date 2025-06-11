# Balanced Binary Tree - DSA Revision Notes

---

## Problem Statement
Given a binary tree, determine if it is height-balanced.

**Definition:** A binary tree is *height-balanced* if:
- The left and right subtrees of every node differ in height by no more than 1.

---

## Approach 1: Brute Force

### 1. **Core Idea**
Check the height of the left and right subtrees for each node, and verify the difference is ≤ 1. Repeat this for every node.

### 2. **Algorithm**
- For each node:
  - Recursively compute the height of its left subtree.
  - Recursively compute the height of its right subtree.
  - Check if the absolute difference of these heights is ≤ 1.
  - Recursively verify if the left and right subtrees are balanced.
- If all nodes satisfy the balance condition, the tree is balanced.

### 3. **Java Code**
```java
public class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) return true;
        int leftHeight = height(root.left);
        int rightHeight = height(root.right);
        if (Math.abs(leftHeight - rightHeight) > 1) return false;
        return isBalanced(root.left) && isBalanced(root.right);
    }
    
    private int height(TreeNode node) {
        if (node == null) return 0;
        return 1 + Math.max(height(node.left), height(node.right));
    }
}
```

### 4. **Complexity Analysis**
- **Time Complexity:**  
  **O(n^2)** in the worst case because for each node, height calculation takes O(n) in the worst case, and this is done for all nodes.
- **Space Complexity:**  
  **O(h)**, where h is the height of the tree, due to recursive stack space.

### 5. **Dry Run Example**
Suppose the tree:
```
        1
       / \
      2   3
     /
    4
```

- Check node 1:
  - Height of left subtree (node 2): 2
  - Height of right subtree (node 3): 1
  - Difference: 1 → OK
- Check node 2:
  - Height of left (node 4): 1
  - Height of right: 0
  - Difference: 1 → OK
- Check node 4: leaf, heights 0, OK
- Check node 3: leaf, heights 0, OK
- All nodes satisfy balance → **Tree is balanced**.

---

## Approach 2: Improved Solution (Optimized Recursion)

### 1. **Core Idea**
Combine height calculation and balance checking in a single traversal to avoid recomputation, reducing the overall time complexity from O(n^2) to O(n).

### 2. **Algorithm**
- Define a recursive function that:
  - Returns the height of the subtree if it is balanced.
  - Returns -1 immediately if it finds an imbalance.
- For each node:
  - Recursively get left subtree height:
    - If -1, propagate imbalance upward.
  - Recursively get right subtree height:
    - If -1, propagate imbalance upward.
  - Check the height difference:
    - If > 1, return -1.
  - Else, return the height (max of left and right + 1).

### 3. **Java Code**
```java
public class Solution {
    public boolean isBalanced(TreeNode root) {
        return checkBalance(root) != -1;
    }

    private int checkBalance(TreeNode node) {
        if (node == null) return 0;
        
        int leftHeight = checkBalance(node.left);
        if (leftHeight == -1) return -1; // Not balanced
        
        int rightHeight = checkBalance(node.right);
        if (rightHeight == -1) return -1; // Not balanced
        
        if (Math.abs(leftHeight - rightHeight) > 1) return -1;
        
        return Math.max(leftHeight, rightHeight) + 1;
    }
}
```

### 4. **Complexity Analysis**
- **Time Complexity:**  
  **O(n)** because each node is visited once.
- **Space Complexity:**  
  **O(h)** due to recursive stack space.

### 5. **Dry Run Example**
Using the same tree:
```
        1
       / \
      2   3
     /
    4
```

- Start at node 1:
  - Check left (node 2):
    - Check left (node 4):
      - Leaf: return height 0
    - Check right (null): return 0
    - Difference: 0 → OK, return height 1
  - Check right (node 3):
    - Leaf: return height 0
  - Difference at node 1: |1 - 0| = 1 → OK, return height 2

- Final result: Not -1 → **Tree is balanced**.

---

## Approach 3: Summary & Best Practice

| Approach | Key Idea | Time Complexity | Space Complexity | When to Use |
|------------|------------|-------------------|---------------------|--------------|
| Brute Force | Calculate height for each node separately | O(n^2) | O(h) | Small trees or initial implementation |
| Optimized Recursion | Combine height calculation and balance check | O(n) | O(h) | Large trees, performance-critical applications |

---

## Final Notes:
- The most efficient approach is the **Optimized Recursive method**.
- Always aim to combine multiple traversals into a single pass for better performance.
- Understand the recursive pattern: return height if balanced, else propagate failure (-1).

---

**Happy Coding!**
