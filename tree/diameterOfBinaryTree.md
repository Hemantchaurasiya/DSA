# Diameter of Binary Tree - DSA Revision Note

---

## Problem Statement
Given a binary tree, find the **diameter** of the tree. The diameter is defined as the number of nodes on the longest path between any two nodes in the tree. The path may or may not pass through the root.

---

## Approach 1: Brute Force

### Core Idea
Calculate the **height** of every node's subtree multiple times, and for each node, determine the longest path passing through that node as the sum of the heights of its left and right subtrees.

### Algorithm
1. For each node:
   - Recursively calculate the height of its left subtree.
   - Recursively calculate the height of its right subtree.
2. The diameter passing through that node is `leftHeight + rightHeight + 1`.
3. Keep track of the maximum diameter found so far.
4. Return the maximum diameter after processing all nodes.

### Java Code
```java
class Solution {
    int diameter = 0;

    public int height(TreeNode root) {
        if (root == null) return 0;
        int leftHeight = height(root.left);
        int rightHeight = height(root.right);
        // Update diameter if longer path found
        diameter = Math.max(diameter, leftHeight + rightHeight + 1);
        return Math.max(leftHeight, rightHeight) + 1;
    }

    public int diameterOfBinaryTree(TreeNode root) {
        height(root);
        return diameter - 1; // Number of edges = nodes - 1
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N^2)**  
  For each node, height is recomputed, leading to repeated calculations.
- **Space Complexity:** **O(H)** (where H is the height of the tree) for recursion stack space.

### Dry Run
**Input Tree:**
```
        1
       / \
      2   3
     / 
    4
```

- Compute height for node 4: 1, diameter = 0.
- Compute height for node 2: max(0, height(4)) + 1 = 2, diameter updated to 2.
- Compute height for node 3: 1, diameter remains 2.
- For root 1: max(height(2), height(3)) + 1 = 3, diameter updated to max(2, 3) = 3.

Final diameter (number of edges): 3.

---

## Approach 2: Better Solution (Optimized with Single Pass)

### Core Idea
Combine height calculation and diameter update in a single traversal to avoid recomputing heights, achieving an **O(N)** time complexity.

### Algorithm
1. Define a helper function that:
   - Recursively computes the height of each node.
   - During the recursion, updates the maximum diameter based on the heights of left and right subtrees.
2. Return the height of the current node.
3. Maintain a global variable to keep track of the maximum diameter found.

### Java Code
```java
class Solution {
    int maxDiameter = 0;

    private int dfs(TreeNode root) {
        if (root == null) return 0;
        int leftHeight = dfs(root.left);
        int rightHeight = dfs(root.right);
        // Update maximum diameter
        maxDiameter = Math.max(maxDiameter, leftHeight + rightHeight);
        // Return height of current node
        return Math.max(leftHeight, rightHeight) + 1;
    }

    public int diameterOfBinaryTree(TreeNode root) {
        dfs(root);
        return maxDiameter; // Number of edges
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**  
  Each node is visited exactly once.
- **Space Complexity:** **O(H)** for recursion stack, where H is the height of the tree.

### Dry Run
Using the previous tree:
```
        1
       / \
      2   3
     / 
    4
```

- Start at root (1):
  - dfs(2):
    - dfs(4):
      - null children return 0.
      - maxDiameter = max(0, 0 + 0) = 0.
      - return height 1.
    - dfs(2) continues:
      - leftHeight = 1 (from node 4),
      - rightHeight = 0,
      - maxDiameter = max(0, 1 + 0) = 1,
      - return height 2.
  - dfs(3):
    - null children return 0.
    - maxDiameter = max(1, 0 + 0) = 1.
    - return height 1.
  - For root (1):
    - leftHeight = 2,
    - rightHeight = 1,
    - maxDiameter = max(1, 2 + 1) = 3,
    - return height 3.

**Result:** Diameter in edges = 3.

---

## Approach 3: Most Optimal Solution (Using Post-order Traversal)

### Core Idea
This approach is similar to the second but emphasizes the **post-order traversal** to compute height and diameter in a single recursive pass, leading to a clean and efficient implementation.

### Algorithm
- Use a recursive function that:
  - Returns the height of the current node.
  - Updates a global maximum diameter with the sum of left and right subtree heights.
- The diameter is stored as the maximum sum of left and right heights found during traversal.

### Java Code
```java
class Solution {
    int maxDiameter = 0;

    public int computeHeight(TreeNode node) {
        if (node == null) return 0;
        int leftHeight = computeHeight(node.left);
        int rightHeight = computeHeight(node.right);
        // Update the diameter if the path through this node is longer
        maxDiameter = Math.max(maxDiameter, leftHeight + rightHeight);
        // Return height of this node
        return Math.max(leftHeight, rightHeight) + 1;
    }

    public int diameterOfBinaryTree(TreeNode root) {
        computeHeight(root);
        return maxDiameter; // Number of edges
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, each node processed once.
- **Space Complexity:** **O(H)** due to recursion stack.

### Dry Run
Using the same input:
```
        1
       / \
      2   3
     / 
    4
```

- Start with node 4:
  - null children: 0, maxDiameter=0, return 1.
- Node 2:
  - leftHeight=1, rightHeight=0,
  - maxDiameter= max(0, 1+0)=1,
  - return height=2.
- Node 3:
  - null children: 0,
  - maxDiameter= max(1, 0+0)=1,
  - return height=1.
- Node 1:
  - leftHeight=2, rightHeight=1,
  - maxDiameter= max(1, 2+1)=3,
  - return height=3.

Final diameter (edges): 3.

---

## Summary Table

| **Aspect** | **Approach 1: Brute Force** | **Approach 2 & 3: Optimized** |
|--------------|------------------------------|------------------------------|
| **Time Complexity** | O(N^2) | O(N) |
| **Space Complexity** | O(H) | O(H) |
| **Key Idea** | Recompute heights for each node | Compute height and diameter simultaneously in one traversal |

---

## Final Tips for Interviews:
- Always aim for the **O(N)** solution by combining height calculation and diameter update.
- Remember that the diameter is the number of edges, so subtract 1 if you count nodes.
- Use post-order traversal to process children before the parent for such tree problems.
- Keep track of global maximum during recursion to avoid extra passes.

---

**Happy Revising!**
