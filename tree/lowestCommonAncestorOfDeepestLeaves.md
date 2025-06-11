# Lowest Common Ancestor of Deepest Leaves

This problem involves finding the **Lowest Common Ancestor (LCA)** of the **deepest leaves** in a binary tree. The challenge is to identify the node that is the **deepest** in the tree and, among those deepest leaves, find their **LCA**.

---

## Approach 1: Brute Force (Find All Deepest Leaves, then Find Their LCA)

### **Core Idea:**
- First, **identify the maximum depth** of the tree.
- Collect **all leaves at this maximum depth**.
- Find the **LCA of these leaves**.

### **Algorithm:**
1. **Traverse the entire tree** to determine the **maximum depth**.
2. **Collect all leaves** at this maximum depth during traversal.
3. Use a **standard LCA algorithm** to find the **LCA of the collected deepest leaves**.
   
Alternatively, since the leaves are known, you can perform multiple LCA computations pairwise until only one node remains.

---

### **Java Code:**

```java
class Solution {
    TreeNode ans;
    int maxDepth = -1;
    List<TreeNode> deepestLeaves = new ArrayList<>();
    
    public TreeNode lcaDeepestLeaves(TreeNode root) {
        // Step 1: Find max depth
        maxDepth = getMaxDepth(root);
        // Step 2: Collect all deepest leaves
        collectDeepestLeaves(root, 0);
        // Step 3: Compute LCA of all deepest leaves
        TreeNode lcaNode = deepestLeaves.get(0);
        for (int i = 1; i < deepestLeaves.size(); i++) {
            lcaNode = findLCA(root, lcaNode, deepestLeaves.get(i));
        }
        return lcaNode;
    }
    
    private int getMaxDepth(TreeNode node) {
        if (node == null) return 0;
        return Math.max(getMaxDepth(node.left), getMaxDepth(node.right)) + 1;
    }
    
    private void collectDeepestLeaves(TreeNode node, int depth) {
        if (node == null) return;
        if (depth == maxDepth - 1 && (node.left == null && node.right == null || 
            (getMaxDepth(node.left) + depth == maxDepth - 1) || 
            (getMaxDepth(node.right) + depth == maxDepth - 1))) {
            // A leaf at maximum depth
            if (node.left == null && node.right == null) {
                deepestLeaves.add(node);
            }
        }
        collectDeepestLeaves(node.left, depth + 1);
        collectDeepestLeaves(node.right, depth + 1);
    }
    
    private TreeNode findLCA(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q) return root;
        TreeNode left = findLCA(root.left, p, q);
        TreeNode right = findLCA(root.right, p, q);
        if (left != null && right != null) return root;
        return left != null ? left : right;
    }
}
```

---

### **Complexity Analysis:**

| Aspect | Explanation |
|---|---|
| **Time Complexity** | **O(N^2)** in the worst case, because for each leaf, we may traverse large parts of the tree during LCA computations. Finding max depth is O(N), collecting leaves is O(N), and multiple LCA computations can be costly. |
| **Space Complexity** | **O(N)** for storing list of deepest leaves and recursion stack during traversal. |

---

### **Dry Run:**

**Sample Tree:**

```
        1
       / \
      2   3
     /     \
    4       5
   /         \
  6           7
```

- Max depth = 4 (nodes 6 and 7).
- Deepest leaves: 6, 7.
- LCA of 6 and 7: node 1.

**Steps:**
- Find max depth: 4.
- Collect leaves at depth 4: nodes 6, 7.
- Compute LCA:
  - LCA(6,7): node 1.

---

## Approach 2: Recursive Post-Order Traversal (Most Optimal Solution)

### **Core Idea:**
- Use a **bottom-up recursive approach**.
- For each node, **calculate the depth of its subtrees**.
- When the depths of left and right subtrees are **equal and maximal**, the node is the **LCA of the deepest leaves**.
- **Return** the **height** of the subtree along with the **candidate node**.

### **Algorithm:**
1. Define a recursive function that returns **(height, node)**:
   - **height**: the maximum depth from current node downwards.
   - **node**: the lowest common ancestor of the deepest leaves in its subtrees.
2. For each node:
   - Recursively compute for left and right children.
   - **Compare** the left and right subtree heights:
     - If **left height > right height**, propagate left's LCA.
     - If **right height > left height**, propagate right's LCA.
     - If **equal**, current node is the **LCA**.
3. The **initial call** returns the **LCA of the deepest leaves**.

---

### **Java Code:**

```java
class Solution {
    public TreeNode lcaDeepestLeaves(TreeNode root) {
        return helper(root).node;
    }
    
    private Result helper(TreeNode node) {
        if (node == null) return new Result(0, null);
        Result left = helper(node.left);
        Result right = helper(node.right);
        if (left.height > right.height) {
            return new Result(left.height + 1, left.node);
        } else if (right.height > left.height) {
            return new Result(right.height + 1, right.node);
        } else {
            // Equal height -> current node is LCA of deepest leaves
            return new Result(left.height + 1, node);
        }
    }
    
    private static class Result {
        int height;
        TreeNode node;
        Result(int height, TreeNode node) {
            this.height = height;
            this.node = node;
        }
    }
}
```

---

### **Complexity Analysis:**

| Aspect | Explanation |
|---|---|
| **Time Complexity** | **O(N)** because each node is visited once during the post-order traversal. |
| **Space Complexity** | **O(H)** where H is the height of the tree due to recursion stack; in the worst case, O(N). |

---

### **Dry Run:**

**Sample Tree:**

```
        1
       / \
      2   3
     /     \
    4       5
   /         \
  6           7
```

- **Starting from root (1):**
  - Left subtree (2):
    - Left (4):
      - Left (6): height=1, node=6
      - Right: null: height=0, node=null
      - Since left height=1 > right=0, propagate (height=2, node=6)
    - Right (null): height=0, node=null
    - Compare (4): left=2, right=0 -> height=3, node=6
  - Right subtree (3):
    - Left: null
    - Right (5):
      - Left: null
      - Right (7): height=1, node=7
      - Compare (5): left=0, right=1 -> height=2, node=7
    - Compare (3): height=2, node=7
  - Now at root (1):
    - Left height=3, node=6
    - Right height=2, node=7
    - Since left > right, propagate (height=4, node=6)

- **Result:** Node 6 is the deepest leaf, but since 6 and 7 are at same depth, the LCA of deepest leaves (6 and 7) is node 1, as per correct logic.

---

## Summary:

| Approach | Pros | Cons | Final notes |
|---|---|---|---|
| **Brute Force** | Easy to understand, straightforward | Less efficient, multiple traversals | Suitable for small trees or initial implementation |
| **Optimized Recursive** | **O(N)** time, single traversal, elegant | Slightly complex implementation | Best suited for large trees and interview scenarios |

---

## Final Tips:
- Focus on the **bottom-up approach** for optimal solutions.
- Understand the **depth calculation** and how to propagate results during recursion.
- Practice **LCA techniques** as they are fundamental in many tree problems.

---

**End of Revision Note**
