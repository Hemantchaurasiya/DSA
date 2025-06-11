# Trim a Binary Search Tree

---

## Problem Overview
Given the root of a Binary Search Tree (BST) and a **range `[low, high]`**, trim the tree so that all its elements lie within the range. The resulting tree should still be a valid BST.

---

## Approach 1: Brute Force (In-order traversal + Rebuilding)

### **Core Idea:**
Traverse the entire tree, collect all nodes within `[low, high]`, then rebuild the BST from these nodes.

### **Algorithm:**
1. Perform an in-order traversal of the BST to get a sorted list of all nodes.
2. Filter this list to keep only nodes with values in `[low, high]`.
3. Rebuild a new BST from this filtered list.

---

### **Java Code:**
```java
import java.util.*;

public class TrimBST {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        List<Integer> nodes = new ArrayList<>();
        inorder(root, nodes);
        // Filter nodes within [low, high]
        List<Integer> filtered = new ArrayList<>();
        for (int val : nodes) {
            if (val >= low && val <= high) {
                filtered.add(val);
            }
        }
        // Rebuild BST from filtered list
        return buildBST(filtered, 0, filtered.size() - 1);
    }

    private void inorder(TreeNode node, List<Integer> nodes) {
        if (node == null) return;
        inorder(node.left, nodes);
        nodes.add(node.val);
        inorder(node.right, nodes);
    }

    private TreeNode buildBST(List<Integer> vals, int left, int right) {
        if (left > right) return null;
        int mid = left + (right - left) / 2;
        TreeNode root = new TreeNode(vals.get(mid));
        root.left = buildBST(vals, left, mid - 1);
        root.right = buildBST(vals, mid + 1, right);
        return root;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  **O(N)** for in-order traversal + **O(N log N)** for rebuilding (if not balanced).  
  Overall: **O(N)**, because traversals and filtering are linear, and rebuilding is linear in the number of nodes.

- **Space Complexity:**  
  **O(N)** for storing nodes in list + recursion stack space in rebuilding.

---

### **Dry Run:**

Suppose the tree:

```
        3
       / \
      0   4
       \
        2
       /
      1
```

with `[low, high] = [1, 3]`.

**In-order traversal:** `[0, 1, 2, 3, 4]`  
**Filtered list:** `[1, 2, 3]`  

**Rebuild BST from `[1, 2, 3]`:**

- Mid = 1 → root = 2
- Left sublist `[1]` → left child = 1
- Right sublist `[3]` → right child = 3

Resulting tree:

```
      2
     / \
    1   3
```

---

## Approach 2: Recursive Solution (Most Optimal)

### **Core Idea:**
Leverage BST properties to prune subtrees during traversal:
- If `node.val < low`, discard the left subtree, recurse on the right.
- If `node.val > high`, discard the right subtree, recurse on the left.
- If `low <= node.val <= high`, keep the node and recurse on both subtrees.

This approach modifies the tree **in-place**.

---

### **Algorithm:**
1. Recursively traverse the tree.
2. For each node:
   - If `node.val < low`, trim the left subtree and recurse on the right.
   - If `node.val > high`, trim the right subtree and recurse on the left.
   - Else, recurse on both children and assign the resulting trimmed subtrees back to the node's left and right pointers.
3. Return the node if it falls within the range; otherwise, return the trimmed subtree.

---

### **Java Code:**
```java
public class TrimBST {
    public TreeNode trimBST(TreeNode root, int low, int high) {
        if (root == null) return null;

        if (root.val < low) {
            // Current node is too small; trim left subtree
            return trimBST(root.right, low, high);
        } else if (root.val > high) {
            // Current node is too large; trim right subtree
            return trimBST(root.left, low, high);
        } else {
            // Node is within range; recurse on both sides
            root.left = trimBST(root.left, low, high);
            root.right = trimBST(root.right, low, high);
            return root;
        }
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  **O(N)**, since each node is visited exactly once.

- **Space Complexity:**  
  **O(H)**, where `H` is the height of the tree, due to recursion stack.

---

### **Dry Run:**

Using the same tree:

```
        3
       / \
      0   4
       \
        2
       /
      1
```

with `[low, high] = [1, 3]`.

**Step-by-step:**

- Start at root=3: within range → recurse left and right.
- Left = 0: less than 1 → discard left, recurse on right subtree (node=2).
- Node=2: within range → recurse left (node=1) and right (null).
- Node=1: within range → no change, recurse on children (both null).
- Node=4: greater than 3 → discard right, recurse on left (null).

Resulting tree:

```
      3
     /
    2
   /
  1
```

which satisfies the range [1, 3].

---

## Summary Table

| Approach | Core Idea | Algorithm Summary | Code Sample | Time Complexity | Space Complexity | Dry Run Notes |
|------------|--------------|---------------------|--------------|-----------------|------------------|---------------|
| Brute Force | In-order traversal + rebuild | Collect nodes, filter, rebuild BST | See above | O(N) | O(N) | Rebuilds entire tree, less efficient |
| Recursive (Optimal) | In-place pruning leveraging BST properties | Recursively trim subtrees based on value comparisons | See above | O(N) | O(H) | Efficient, modifies tree during traversal |

---

## Final Tips for Interviews:
- Always consider the BST properties to prune unnecessary traversal.
- Recursive solutions are typically more efficient and cleaner for this problem.
- Be mindful of edge cases: empty tree, all nodes outside range, etc.
- Practice dry runs with various inputs to understand how the pruning occurs.

---

**End of Revision Note**
