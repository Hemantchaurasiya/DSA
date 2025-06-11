# Delete Node in a BST

---

## Introduction
Given a **Binary Search Tree (BST)** and a **node value**, the goal is to **delete the node with that value** while maintaining the BST properties:
- Left subtree contains nodes with values **less than** the node's value.
- Right subtree contains nodes with values **greater than** the node's value.

---

## Approach 1: Brute Force (Recursive Search & Deletion)

### Core Idea
Search for the node to delete recursively, and once found, handle deletion based on the node's children.

### Algorithm
1. **Search** for the node with the key:
   - If the key is less than the current node's value, recurse into the **left subtree**.
   - If the key is greater, recurse into the **right subtree**.
   - If the key matches the current node's value, proceed to delete.
2. **Deletion Cases**:
   - **Node is a leaf** (no children): simply delete it.
   - **Node has one child**: replace the node with its child.
   - **Node has two children**:
     - Find the **inorder successor** (smallest in right subtree) or **inorder predecessor** (largest in left subtree).
     - Replace the node's value with the successor/predecessor.
     - Delete the successor/predecessor node recursively.

### Java Code

```java
class TreeNode {
    int val;
    TreeNode left, right;

    TreeNode(int val) {
        this.val = val;
        left = right = null;
    }
}

public class BSTDelete {
    public TreeNode deleteNode(TreeNode root, int key) {
        if (root == null) return null;

        if (key < root.val) {
            root.left = deleteNode(root.left, key);
        } else if (key > root.val) {
            root.right = deleteNode(root.right, key);
        } else {
            // Node found, handle deletion
            // Case 1: No child
            if (root.left == null && root.right == null) {
                return null;
            }
            // Case 2: One child
            if (root.left == null) return root.right;
            if (root.right == null) return root.left;
            // Case 3: Two children
            TreeNode successor = findMin(root.right);
            root.val = successor.val;
            root.right = deleteNode(root.right, successor.val);
        }
        return root;
    }

    private TreeNode findMin(TreeNode node) {
        while (node.left != null) node = node.left;
        return node;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(h), where h is the height of the tree (O(log n) for balanced, O(n) for skewed)
- **Space Complexity:** O(h) due to recursion stack

---

## Approach 2: Iterative Solution (Optional / Alternative)

### Core Idea
Perform an iterative search for the node, then handle deletion similar to recursive approach.

*(Note: For brevity, not detailed here, but similar logic applies)*

---

## Approach 3: Most Optimal Solution (Standard Recursive Approach)

### Core Idea
Utilize the properties of BST:
- Search for node to delete.
- When found, handle 3 cases:
  - Leaf node.
  - Node with one child.
  - Node with two children (replace with inorder successor).

This is essentially the same as Approach 1 but emphasizes clean, concise recursive logic.

### Algorithm
Same as Approach 1 with a focus on clarity and efficiency.

---

## Dry Run Example

### Input:
```plaintext
BST:        50
           /    \
         30      70
        /  \    /  \
      20   40  60  80
```

**Delete node with value = 70**

### Step-by-step:

| Step | Current Node | Action | Explanation | Resulting Tree Structure |
|---------|----------------|-----------|----------------|--------------------------|
| 1       | 50             | Search right | 70 > 50, move right | Tree remains unchanged |
| 2       | 70             | Found node | Node with value 70 | Proceed to delete |
| 3       | 70             | Has two children | Find inorder successor (60) | 60 is the minimum in right subtree of 70 |

**Replace 70 with 60:**

| Step | Action | Explanation | Tree after replacement |
|---------|--------------|----------------|-------------------------|
| 4       | Replace value | 70 replaced with 60 | Tree now has 60 at root of subtree |
| 5       | Delete inorder successor (60) | 60 is leaf | Remove 60 from its original position |

**Final Tree:**

```plaintext
        50
       /    \
     30      60
    /  \    /  \
  20   40  N   80
```

*(N indicates null)*

---

## Summary
- **Recursive approach** is clean, easy to implement, and efficient.
- **Handling all cases** (leaf, one child, two children) is crucial.
- **Inorder successor/predecessor** helps maintain BST properties after deletion.
- **Time complexity** is O(h), space complexity is O(h).

---

**Happy Coding!**
