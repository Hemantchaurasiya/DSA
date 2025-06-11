# Deepest Leaves Sum

The **Deepest Leaves Sum** problem involves finding the sum of node values at the deepest level of a binary tree. This problem is common in interviews and tests understanding of tree traversal techniques.

---

## Problem Statement
Given the root of a binary tree, return the sum of the values of the deepest leaves.

---

## Approaches to Solve the Problem

### 1. Brute Force Approach

#### Core Idea:
Perform multiple traversals of the tree to determine the maximum depth, then sum up all nodes at that maximum depth.

---

### 2. Better Solution (Single Pass - BFS Level Order Traversal)

#### Core Idea:
Perform a level-order traversal (BFS). At each level, update the sum of node values. When the traversal reaches the last level, the sum recorded will be the sum of the deepest leaves.

---

### 3. Most Optimal Solution (Single Pass - DFS with Depth Tracking)

#### Core Idea:
Use DFS to traverse the tree while keeping track of the current depth. Maintain variables to store the maximum depth encountered and sum of nodes at that depth. Update these variables as you traverse.

---

## Approach 1: Brute Force

### Algorithm:
1. Perform a traversal to find the **maximum depth** of the tree.
2. Perform a second traversal to **sum all nodes at the maximum depth**.

### Java Code:
```java
public class Solution {
    int maxDepth = 0;
    int sum = 0;
    
    // Helper to find maximum depth
    private int findMaxDepth(TreeNode root) {
        if (root == null) return 0;
        int left = findMaxDepth(root.left);
        int right = findMaxDepth(root.right);
        return Math.max(left, right) + 1;
    }
    
    // Helper to sum nodes at max depth
    private void sumDeepest(TreeNode root, int depth) {
        if (root == null) return;
        if (depth == maxDepth) {
            sum += root.val;
        }
        sumDeepest(root.left, depth + 1);
        sumDeepest(root.right, depth + 1);
    }
    
    public int deepestLeavesSum(TreeNode root) {
        maxDepth = findMaxDepth(root);
        sum = 0;
        sumDeepest(root, 1);
        return sum;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** `O(N) + O(N) = O(N)`  
  Two traversals of the tree (finding max depth and summing nodes at that depth).
- **Space Complexity:** `O(H)` where `H` is the height of the tree (due to recursion stack).

---

### Dry Run:
Suppose the tree is:
```
        1
       / \
      2   3
     /   / \
    4   5   6
```

- **First traversal (find max depth):**
  - Max depth = 3
- **Second traversal (sum at depth 3):**
  - Nodes at depth 3: 4, 5, 6
  - Sum = 4 + 5 + 6 = 15

---

### Approach 2: Level-Order Traversal (BFS)

### Algorithm:
1. Initialize a queue and add the root.
2. While the queue is not empty:
   - Determine the number of nodes at the current level (`levelSize`).
   - Initialize `currentLevelSum` to 0.
   - For each node at this level:
     - Dequeue the node.
     - Add its value to `currentLevelSum`.
     - Enqueue its children if they exist.
3. When the loop finishes, `currentLevelSum` holds the sum of the deepest level nodes.

### Java Code:
```java
import java.util.*;

public class Solution {
    public int deepestLeavesSum(TreeNode root) {
        if (root == null) return 0;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        int sum = 0;
        
        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            sum = 0; // reset sum for current level
            for (int i = 0; i < levelSize; i++) {
                TreeNode node = queue.poll();
                sum += node.val;
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
        }
        return sum; // sum at the last level
    }
}
```

### Complexity Analysis:
- **Time Complexity:** `O(N)`  
  Each node is visited exactly once.
- **Space Complexity:** `O(N)` in the worst case (if the bottom level is very wide).

---

### Dry Run:
Using the same tree:

```
        1
       / \
      2   3
     /   / \
    4   5   6
```

- Queue after each iteration:
  
| Step | Queue Contents             | Sum of current level | Deepest leaves sum after last iteration |
|-------|----------------------------|----------------------|------------------------------------------|
| 1     | [1]                        | 1                    | 1                                        |
| 2     | [2, 3]                     | 2 + 3 = 5            | 5                                        |
| 3     | [4, 5, 6]                  | 4 + 5 + 6 = 15       | **Final answer: 15**                     |

---

### Approach 3: DFS with Depth Tracking (Most Optimal)

### Algorithm:
1. Use a recursive DFS traversal.
2. Track the current depth during recursion.
3. Maintain two variables:
   - `maxDepth` to record the maximum depth encountered.
   - `deepestSum` to record the sum of values at the maximum depth.
4. During traversal:
   - If current depth > `maxDepth`, update `maxDepth` and reset `deepestSum`.
   - If current depth == `maxDepth`, add current node's value to `deepestSum`.

### Java Code:
```java
public class Solution {
    private int maxDepth = 0;
    private int deepestSum = 0;
    
    private void dfs(TreeNode root, int depth) {
        if (root == null) return;
        // Update maxDepth and sum accordingly
        if (depth > maxDepth) {
            maxDepth = depth;
            deepestSum = root.val;
        } else if (depth == maxDepth) {
            deepestSum += root.val;
        }
        dfs(root.left, depth + 1);
        dfs(root.right, depth + 1);
    }
    
    public int deepestLeavesSum(TreeNode root) {
        maxDepth = 0;
        deepestSum = 0;
        dfs(root, 1);
        return deepestSum;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** `O(N)`  
  Each node visited once.
- **Space Complexity:** `O(H)`  
  Due to recursion stack, where `H` is the height of the tree.

---

### Dry Run:
Using the same example:

- Start at root (value=1, depth=1): maxDepth=1, deepestSum=1
- Traverse left (value=2, depth=2): maxDepth=2, deepestSum=2
- Traverse left (value=4, depth=3): maxDepth=3, deepestSum=4
- Traverse right (null): no change
- Back to root, traverse right (value=3, depth=2): depth=2 < maxDepth, no change
- Traverse left (value=5, depth=3): same as maxDepth, sum=4+5=9
- Traverse right (value=6, depth=3): sum=9+6=15

Final answer: 15

---

## Summary

| Approach | Key Idea | Time Complexity | Space Complexity | Notes |
|------------|------------|------------------|--------------------|--------|
| Brute Force | Find max depth, then sum nodes at that depth | `O(N) + O(N)` = `O(N)` | `O(H)` | Two traversals |
| Level-Order BFS | Traverse level-by-level, sum last level nodes | `O(N)` | `O(N)` | Efficient, single traversal |
| DFS with Depth | Track max depth and sum via recursion | `O(N)` | `O(H)` | Most space efficient in balanced trees |

---

## Final Tips
- **Use BFS** for straightforward implementation and clarity.
- **Use DFS** if you prefer recursion and want a single traversal.
- Remember that the problem is about traversing **all nodes** to find the deepest level and sum accordingly.

---

## Quick Revision
- **Tree Traversals:** BFS (level order), DFS (preorder/inorder/postorder)
- **Level order traversal** naturally helps identify the deepest level.
- **DFS with depth tracking** optimizes to a single pass with minimal extra space.
- Always consider edge cases like empty trees or trees with a single node.

---

**Happy Revising!**
