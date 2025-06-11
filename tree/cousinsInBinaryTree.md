# Cousins in Binary Tree

This note provides a comprehensive overview of solving the **"Cousins in Binary Tree"** problem, covering multiple approaches from brute-force to optimized solutions, suitable for interview prep and quick revision.

---

## Problem Statement
Given a binary tree and two nodes `x` and `y`, determine whether these nodes are **cousins**. Two nodes are cousins if:
- They are at the **same depth**.
- They have **different parents**.

---

## Approach 1: Brute Force (Recursive Search with Depth and Parent Tracking)

### Core Idea
- Traverse the entire tree to find the **depth** and **parent** of each node `x` and `y`.
- After traversal, compare the depths and parents:
  - Same depth, different parents ⇒ **cousins**.
  - Otherwise, not cousins.

### Algorithm
1. Traverse the tree (using DFS).
2. During traversal, record:
   - `depth_x`, `parent_x` for node `x`.
   - `depth_y`, `parent_y` for node `y`.
3. After traversal, check if:
   - `depth_x == depth_y`
   - `parent_x != parent_y`
4. Return `true` if both conditions hold, else `false`.

### Java Code
```java
public class Solution {
    TreeNode parentX, parentY;
    int depthX, depthY;

    public boolean isCousins(TreeNode root, int x, int y) {
        dfs(root, null, 0, x, y);
        return (depthX == depthY) && (parentX != parentY);
    }

    private void dfs(TreeNode node, TreeNode parent, int depth, int x, int y) {
        if (node == null) return;

        if (node.val == x) {
            depthX = depth;
            parentX = parent;
        } else if (node.val == y) {
            depthY = depth;
            parentY = parent;
        }

        dfs(node.left, node, depth + 1, x, y);
        dfs(node.right, node, depth + 1, x, y);
    }
}
```

### Complexity Analysis
- **Time Complexity:** `O(N)` — visiting all nodes to find the depth and parent.
- **Space Complexity:** `O(H)` — recursion stack, where `H` is the height of the tree (`O(N)` in worst case).

### Dry Run Example
Suppose the tree:
```
       1
      / \
     2   3
    /     \
   4       5
```
- Check `x=4`, `y=5`.
- Traversal:
  - At node `1`: root, depth=0.
  - Left child `2`: depth=1, parent=1.
  - Right child `3`: depth=1, parent=1.
  - Left child of `2` is `4`: depth=2, parent=`2`.
  - Right child of `3` is `5`: depth=2, parent=`3`.
- Result:
  - `depth_x=2`, `parent_x=2`.
  - `depth_y=2`, `parent_y=3`.
- Since depths are equal and parents are different, **return true**.

---

## Approach 2: BFS Level-Order Traversal (Iterative)

### Core Idea
Use a level-order traversal (BFS) to process nodes level by level:
- For each level, check if both `x` and `y` exist.
- If both are found at the same level, ensure they have different parents.

### Algorithm
1. Use a queue to perform BFS.
2. For each level:
   - Track whether `x` and `y` are found.
   - Record the parents of `x` and `y` if encountered.
3. After processing each level:
   - If both found, check if parents differ ⇒ **cousins**.
   - If only one found, they are not at the same level ⇒ **not cousins**.

### Java Code
```java
import java.util.*;

public class Solution {
    public boolean isCousins(TreeNode root, int x, int y) {
        if (root == null) return false;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int size = queue.size();
            TreeNode parentX = null, parentY = null;
            boolean foundX = false, foundY = false;

            for (int i = 0; i < size; i++) {
                TreeNode current = queue.poll();

                if (current.left != null) {
                    if (current.left.val == x) {
                        parentX = current;
                        foundX = true;
                    }
                    if (current.left.val == y) {
                        parentY = current;
                        foundY = true;
                    }
                    queue.offer(current.left);
                }

                if (current.right != null) {
                    if (current.right.val == x) {
                        parentX = current;
                        foundX = true;
                    }
                    if (current.right.val == y) {
                        parentY = current;
                        foundY = true;
                    }
                    queue.offer(current.right);
                }
            }

            if (foundX && foundY) {
                return parentX != parentY;
            }
            if (foundX || foundY) {
                return false; // Found only one at this level
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** `O(N)` — each node visited once.
- **Space Complexity:** `O(N)` — for the queue in the worst case.

### Dry Run Example
Using the same tree as above:
```
       1
      / \
     2   3
    /     \
   4       5
```
- Level 0:
  - Queue: `[1]`.
- Level 1:
  - Queue: `[2, 3]`.
  - Check children:
    - `2`: children `4`, parent = `1`.
    - `3`: children `5`, parent = `1`.
  - `x=4`, `y=5` found at same level, parents are `2` and `3` respectively.
- Since both found and parents differ, return `true`.

---

## Approach 3: Most Optimal Solution (Single DFS or BFS with Early Termination)

### Core Idea
- Combine depth and parent tracking in a single traversal.
- Early terminate when both nodes are found to optimize.

### Algorithm
- Use DFS or BFS.
- Keep track of:
  - Depths and parents.
- Stop traversal when both nodes are located.
- Check if they are at same depth and have different parents.

### Java Code (Optimized DFS)
```java
public class Solution {
    private TreeNode parentX = null, parentY = null;
    private int depthX = -1, depthY = -1;

    public boolean isCousins(TreeNode root, int x, int y) {
        dfs(root, null, 0, x, y);
        return (depthX == depthY) && (parentX != parentY);
    }

    private void dfs(TreeNode node, TreeNode parent, int depth, int x, int y) {
        if (node == null || (parentX != null && parentY != null)) return;

        if (node.val == x) {
            parentX = parent;
            depthX = depth;
        } else if (node.val == y) {
            parentY = parent;
            depthY = depth;
        }

        dfs(node.left, node, depth + 1, x, y);
        dfs(node.right, node, depth + 1, x, y);
    }
}
```

### Complexity Analysis
- **Time Complexity:** `O(N)` in the worst case, but potentially less due to early termination once both nodes are found.
- **Space Complexity:** `O(H)` due to recursion stack.

---

# Summary Table

| Approach                         | Time Complexity | Space Complexity | Key Idea                                      |
|----------------------------------|-----------------|------------------|----------------------------------------------|
| **Brute Force (DFS)**            | `O(N)`          | `O(H)`           | Find depths and parents via full traversal. |
| **BFS Level Order**              | `O(N)`          | `O(N)`           | Use BFS to check nodes at same level.       |
| **Optimized DFS/BFS with Early Exit** | `O(N)` in worst case, less on average | `O(H)` | Track and terminate early when both found.  |

---

## Final Tips
- Always check if nodes are at the same depth and have different parents.
- BFS is often more intuitive for level-based problems.
- Use early termination to optimize DFS solutions.
- Visualize with diagrams for better understanding during interviews.

---

This concludes the **"Cousins in Binary Tree"** revision note. Use this as a quick reference to grasp key approaches and techniques for solving and optimizing the problem.
