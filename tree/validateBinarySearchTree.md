# Validate Binary Search Tree (BST)

This guide covers multiple approaches to determine whether a binary tree is a valid Binary Search Tree (BST). It is structured to help with interview prep and quick revision, progressing from brute-force to the most optimal solution.

---

## 1. Brute Force Approach

### **Core Idea:**
- For each node, verify whether all nodes in its left subtree are less than the node and all nodes in the right subtree are greater.
- This approach checks the entire subtree for each node, leading to redundant traversals.

### **Algorithm:**
1. For each node:
   - Recursively check the maximum value in its left subtree.
   - Recursively check the minimum value in its right subtree.
   - Validate if current node's value is greater than max in left subtree and less than min in right subtree.
2. If all nodes satisfy the condition, return true; otherwise, false.

### **Java Code:**
```java
public class Solution {
    public boolean isValidBST(TreeNode root) {
        if (root == null) return true;
        if (!isValidBST(root.left) || !isValidBST(root.right))
            return false;
        int maxLeft = findMax(root.left);
        int minRight = findMin(root.right);
        if (maxLeft != Integer.MIN_VALUE && root.val <= maxLeft)
            return false;
        if (minRight != Integer.MAX_VALUE && root.val >= minRight)
            return false;
        return true;
    }

    private int findMax(TreeNode node) {
        if (node == null) return Integer.MIN_VALUE;
        int max = node.val;
        max = Math.max(max, findMax(node.left));
        max = Math.max(max, findMax(node.right));
        return max;
    }

    private int findMin(TreeNode node) {
        if (node == null) return Integer.MAX_VALUE;
        int min = node.val;
        min = Math.min(min, findMin(node.left));
        min = Math.min(min, findMin(node.right));
        return min;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** *O(N^2)*, because for each node, we perform a traversal to find min/max in its subtrees.
- **Space Complexity:** *O(H)*, where H is the height of the tree (due to recursion stack).

### **Dry Run:**
- Input:  
  ```
        5
       / \
      1   7
  ```
- Step-by-step:
  - Check root (5):
    - Find max in left subtree (1): 1
    - Find min in right subtree (7): 7
    - Validate: 5 > 1 and 5 < 7 → OK
  - Recursively check left (1): no subtrees → OK
  - Recursively check right (7): no subtrees → OK
- Result: **Valid BST**

---

## 2. Better Solution: In-order Traversal

### **Core Idea:**
- In-order traversal of a BST yields nodes in **strictly increasing order**.
- If during traversal, the current node's value is not greater than the previous node's value, the tree is invalid.

### **Algorithm:**
1. Initialize a variable `prev` to track the last visited node's value (initialize to `null`).
2. Perform an in-order traversal:
   - Recursively visit the left subtree.
   - Check if current node's value > `prev`.
   - Update `prev` to current node's value.
   - Recursively visit the right subtree.
3. If any violation occurs, return false immediately.

### **Java Code:**
```java
public class Solution {
    private TreeNode prev = null;

    public boolean isValidBST(TreeNode root) {
        return inorder(root);
    }

    private boolean inorder(TreeNode node) {
        if (node == null) return true;
        if (!inorder(node.left)) return false;
        if (prev != null && node.val <= prev.val) return false; // Not strictly increasing
        prev = node;
        return inorder(node.right);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** *O(N)*, each node visited exactly once.
- **Space Complexity:** *O(H)*, due to recursion stack (H = height of tree).

### **Dry Run:**
- Input:
  ```
        5
       / \
      1   7
  ```
- Traversal:
  - Visit left (1): prev = null → OK, prev = 1
  - Visit root (5): 5 > 1 → OK, prev = 5
  - Visit right (7): 7 > 5 → OK, prev = 7
- Result: **Valid BST**

---

## 3. Optimal Solution: Min-Max Range Validation

### **Core Idea:**
- Each node must adhere to a valid range:
  - All nodes in the left subtree must be less than the current node's value.
  - All nodes in the right subtree must be greater than the current node's value.
- Use recursion with min and max bounds to verify this.

### **Algorithm:**
1. Start with initial range: `(-∞, +∞)`.
2. For each node:
   - Check if node's value is within the valid range.
   - Recursively validate left subtree with updated max bound (`max = node.val - 1`).
   - Recursively validate right subtree with updated min bound (`min = node.val + 1`).
3. If any node violates the range, return false.

### **Java Code:**
```java
public class Solution {
    public boolean isValidBST(TreeNode root) {
        return validate(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }

    private boolean validate(TreeNode node, long min, long max) {
        if (node == null) return true;
        if (node.val <= min || node.val >= max) return false;
        return validate(node.left, min, node.val) && validate(node.right, node.val, max);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** *O(N)*, each node visited exactly once.
- **Space Complexity:** *O(H)*, recursion stack space.

### **Dry Run:**
- Input:
  ```
        5
       / \
      1   7
  ```
- Step-by-step:
  - Validate root (5): range (-∞, +∞): OK
  - Validate left (1): range (-∞, 5): 1 in range → OK
  - Validate right (7): range (5, +∞): 7 in range → OK
- Result: **Valid BST**

---

# Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Key Points |
|------------|--------------|------------------|-------------------|------------|
| Brute Force | Check subtrees for each node | O(N^2) | O(H) | Redundant traversals, easy to understand but inefficient |
| In-order Traversal | Strictly increasing sequence | O(N) | O(H) | Efficient, simple, relies on inorder property |
| Min-Max Range | Validate node values within bounds | O(N) | O(H) | Most optimal, clean, and standard approach |

---

# Final Notes:
- **Most optimal approach:** Min-Max Range Validation.
- Always verify edge cases like:
  - Empty tree (`null`): should return `true`.
  - Tree with duplicate values: typically invalid in strict BST.
  - Large trees: prefer the in-order or min-max approach for efficiency.

---

Feel free to revisit these approaches and understand their nuances to excel in coding interviews!
