# Path Sum - DSA Revision Note

---

## Problem Overview
Given a binary tree and a target sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the target sum.

---

## Approach 1: Brute Force (Recursive Traversal)

### Core Idea
Explore all root-to-leaf paths, summing the node values along each path, and check if any path equals the target sum.

### Algorithm
1. Start from the root node.
2. Recursively traverse the left and right subtrees.
3. At each node, subtract the node's value from the target sum.
4. If you reach a leaf node (no children), check if the remaining sum equals the node's value.
5. If yes, return `true`.
6. If any recursive call returns `true`, propagate it upwards.
7. If none of the paths match, return `false`.

### Java Code
```java
public class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;
        // Check if leaf node and sum matches
        if (root.left == null && root.right == null) {
            return targetSum == root.val;
        }
        // Recurse on left and right subtrees
        return hasPathSum(root.left, targetSum - root.val) ||
               hasPathSum(root.right, targetSum - root.val);
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, where N is the number of nodes, since every node is visited once.
- **Space Complexity:** **O(H)**, where H is the height of the tree, due to the recursion stack.

### Dry Run
**Input:**
```
       5
      / \
     4   8
    /   / \
   11  13  4
  /  \      \
 7    2      1
Target Sum = 22
```

**Step-by-step:**
- Start at root (5), remaining sum = 22 - 5 = 17.
- Explore left:
  - Node (4), remaining sum = 17 - 4 = 13.
  - Explore left:
    - Node (11), remaining sum = 13 - 11 = 2.
    - Explore left:
      - Node (7), remaining sum = 2 - 7 = -5 → leaf but sum doesn't match.
    - Explore right:
      - Node (2), remaining sum = 2 - 2 = 0 → leaf node, sum matches, return `true`.
- Since left subtree returns `true`, overall result is `true`.

---

## Approach 2: Using a Helper Function with Path Sum Tracking

### Core Idea
Track the remaining sum as you traverse, checking at each leaf if the sum matches.

### Algorithm
Same as Approach 1, but explicitly pass the remaining sum down the recursion, verifying at leaf nodes.

### Java Code
*(Same as above; the principle is similar.)*

---

## Approach 3: Iterative (Using Stack)

### Core Idea
Perform Depth-First Search (DFS) iteratively using a stack, keeping track of the current path sum.

### Algorithm
1. Initialize a stack to hold pairs of `(node, currentSum)`.
2. Push the root node with its value.
3. While the stack isn't empty:
   - Pop a node and its sum.
   - If it's a leaf and the sum equals target sum, return `true`.
   - Push its children with cumulative sums.
4. If no path matches, return `false`.

### Java Code
```java
import java.util.Stack;

public class Solution {
    public boolean hasPathSum(TreeNode root, int targetSum) {
        if (root == null) return false;

        Stack<TreeNode> nodeStack = new Stack<>();
        Stack<Integer> sumStack = new Stack<>();

        nodeStack.push(root);
        sumStack.push(root.val);

        while (!nodeStack.isEmpty()) {
            TreeNode current = nodeStack.pop();
            int currSum = sumStack.pop();

            if (current.left == null && current.right == null && currSum == targetSum) {
                return true;
            }

            if (current.right != null) {
                nodeStack.push(current.right);
                sumStack.push(currSum + current.right.val);
            }

            if (current.left != null) {
                nodeStack.push(current.left);
                sumStack.push(currSum + current.left.val);
            }
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, visiting each node once.
- **Space Complexity:** **O(H)**, where H is the height of the tree, due to the stack.

---

## Summary
| Approach             | Method                        | Time Complexity | Space Complexity | Use Case                                  |
|----------------------|------------------------------|-----------------|------------------|------------------------------------------|
| Brute Force          | Recursive traversal           | O(N)            | O(H)             | Simple, straightforward solution        |
| Using Helper Function| Recursive with path sum passing | O(N)            | O(H)             | Clear separation of logic               |
| Iterative DFS        | Stack-based traversal         | O(N)            | O(H)             | Avoids recursion, iterative approach   |

---

## Final Tips for Interviews
- Understand the recursive nature of tree problems.
- Use DFS (recursion or stack) to explore all paths.
- Keep track of the current sum along the path.
- Check for leaf nodes when the path sum matches the target.
- Optimize for space or iteration as needed.

---

**Happy revising!**
