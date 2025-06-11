# Lowest Common Ancestor (LCA) of a Binary Search Tree (BST)

This note provides a comprehensive overview of different approaches to find the **Lowest Common Ancestor (LCA)** in a BST, suitable for interview preparation and quick revision.

---

## Problem Statement
Given a BST and two nodes, find their **Lowest Common Ancestor** — the lowest node in the tree that has both nodes as descendants (where a node can be a descendant of itself).

---

## Approach 1: Brute Force (General Tree LCA)

### 1. Core Idea
- Find the path from the root to each node.
- Compare the paths to find the last common node.

### 2. Algorithm
- **Step 1:** Find the path from root to `n1`, store it in `path1`.
- **Step 2:** Find the path from root to `n2`, store it in `path2`.
- **Step 3:** Traverse both paths until the values differ; the last common node before divergence is the LCA.

### 3. Java Code
```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int v) { val = v; }
}

public class LCA_BruteForce {
    // Find path from root to target node
    private boolean findPath(TreeNode root, int n, List<TreeNode> path) {
        if (root == null) return false;
        path.add(root);
        if (root.val == n) return true;
        if (findPath(root.left, n, path) || findPath(root.right, n, path))
            return true;
        path.remove(path.size() - 1);
        return false;
    }

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        List<TreeNode> path1 = new ArrayList<>();
        List<TreeNode> path2 = new ArrayList<>();
        findPath(root, p.val, path1);
        findPath(root, q.val, path2);

        int i = 0;
        while (i < path1.size() && i < path2.size() && path1.get(i) == path2.get(i))
            i++;
        return path1.get(i - 1);
    }
}
```

### 4. Complexity Analysis
- **Time Complexity:**  
  - Path finding each takes **O(h)** where `h` is the height of the tree.  
  - Total: **O(h) + O(h) = O(h)**.
- **Space Complexity:**  
  - Storing paths: **O(h)**.

---

## Approach 2: Recursive BST Property Utilization (More Efficient)

### 1. Core Idea
- Use the **BST property**: For any node `n`, all nodes in `n.left` are less, and in `n.right` are greater.
- Traverse from the root:
  - If both `p` and `q` are less than current node, go to left.
  - If both are greater, go to right.
  - Otherwise, current node is the LCA.

### 2. Algorithm
- Start from the root.
- While root exists:
  - If `p.val < root.val` and `q.val < root.val`, move to `root.left`.
  - Else if `p.val > root.val` and `q.val > root.val`, move to `root.right`.
  - Else, `root` is the LCA.

### 3. Java Code
```java
public class LCA_BST {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        while (root != null) {
            if (p.val < root.val && q.val < root.val)
                root = root.left;
            else if (p.val > root.val && q.val > root.val)
                root = root.right;
            else
                return root;
        }
        return null;
    }
}
```

### 4. Complexity Analysis
- **Time Complexity:**  
  - **O(h)**, where `h` is the height of the BST, since we traverse down a path.
- **Space Complexity:**  
  - **O(1)**, iterative approach with no extra space apart from variables.

---

## Approach 3: Recursive Solution with Divide & Conquer (Elegant and Clean)

### 1. Core Idea
- Recursively traverse the tree:
  - If both `p` and `q` are less than current node, recurse into left subtree.
  - If both are greater, recurse into right subtree.
  - Else, current node is the LCA.

### 2. Algorithm
- **Base Case:** If root is null, return null.
- If `p.val` and `q.val` are less than root's value, recurse on `root.left`.
- If both are greater, recurse on `root.right`.
- Else, return current root.

### 3. Java Code
```java
public class LCA_Recursive {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) return null;

        if (p.val < root.val && q.val < root.val)
            return lowestCommonAncestor(root.left, p, q);
        else if (p.val > root.val && q.val > root.val)
            return lowestCommonAncestor(root.right, p, q);
        else
            return root;
    }
}
```

### 4. Complexity Analysis
- **Time Complexity:**  
  - **O(h)**, where `h` is tree height, due to recursive traversal.
- **Space Complexity:**  
  - **O(h)** for recursion stack in worst case (skewed tree).

---

## **Dry Run Example**

Suppose the BST is:

```
        6
       / \
      2   8
     / \ / \
    0  4 7  9
      / \
     3   5
```

- Find `p = 2`, `q = 8`.

### Approach 2 (Iterative)

| Step | Current `root` | Comparison | Action | Resulting `root` |
|---------|----------------|--------------|---------|------------------|
| 1 | 6 | `2` and `8` | `2 < 6` and `8 > 6` | Both on different sides | Return 6 |  
**LCA = 6**

### Approach 3 (Recursive)

- Start at 6:
  - `p=2 <6` and `q=8 >6` → current node 6 is LCA.

---

## **Summary**

| Approach | Best Use Case | Key Points |
|------------|----------------|--------------|
| Brute Force | Small trees / No BST property | Finds paths, general approach |  
| Iterative BST Property | Large trees, optimized | Uses BST properties, faster |  
| Recursive Divide & Conquer | Clean code, recursive style | Elegant, easy to understand |

---

## **Key Takeaways**
- Leverage BST properties for optimal solutions.
- Use iterative approach for constant space.
- Recursive solutions are elegant and concise.
- Always analyze time and space complexity for large inputs.

---

**Happy Coding!**
