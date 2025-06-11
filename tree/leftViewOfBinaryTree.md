# Left View of Binary Tree

---

## Problem Overview
Given a binary tree, **print the left view**, i.e., the set of nodes visible when the tree is viewed from the left side.

---

## Approaches to Solve the Problem

---

### 1. Brute Force Approach

#### **Core Idea**
- Traverse the tree level by level (using level order traversal).
- For each level, identify the first node encountered.
- Collect these first nodes from each level to form the left view.

#### **Algorithm**
1. Perform a level order traversal (using a queue).
2. For each level:
   - Record the first node encountered (the leftmost node at that level).
3. Collect these nodes in a result list.
4. Return the list as the left view.

#### **Java Code**
```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}

public class LeftView {
    public List<Integer> leftView(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root == null) return result;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int levelSize = queue.size();

            // Traverse nodes at current level
            for (int i = 0; i < levelSize; i++) {
                TreeNode current = queue.poll();

                // Add the first node of this level to result
                if (i == 0) {
                    result.add(current.val);
                }

                if (current.left != null) queue.offer(current.left);
                if (current.right != null) queue.offer(current.right);
            }
        }
        return result;
    }
}
```

#### **Complexity Analysis**
- **Time Complexity:** `O(n)`  
  Each node is visited exactly once during level order traversal.
- **Space Complexity:** `O(n)`  
  Queue stores at most all nodes at the widest level.

#### **Dry Run Example**

Suppose the tree:
```
        1
       / \
      2   3
     / \   \
    4   5   6
```

**Level order traversal steps:**

| Step | Queue (Nodes)             | Level nodes processed | Leftmost node | Result so far     |
|-------|----------------------------|------------------------|----------------|-------------------|
| 1     | [1]                        | 1                      | 1              | [1]               |
| 2     | [2, 3]                     | 2                      | 2              | [1, 2]           |
| 3     | [4, 5, 6]                  | 4                      | 4              | [1, 2, 4]        |

---

### 2. Recursive Depth-First Search (DFS) Approach

#### **Core Idea**
- Traverse the tree in pre-order (or any DFS manner).
- Keep track of the maximum level visited so far.
- For each new level, the first node encountered at that level is part of the left view.
- Use a helper function with parameters: current node, current level, and a list to store the result, and a variable to track maximum level visited.

#### **Algorithm**
1. Start DFS from root at level `0`.
2. If the current level is greater than the maximum level visited:
   - Add the current node's value to the result.
   - Update the maximum level.
3. Recurse first into the left child, then into the right child (to ensure leftmost nodes are encountered first at each level).

#### **Java Code**
```java
import java.util.*;

public class LeftView {
    int maxLevel = -1;

    public List<Integer> leftView(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        dfs(root, 0, result);
        return result;
    }

    private void dfs(TreeNode node, int level, List<Integer> result) {
        if (node == null) return;

        // If this is the first node of this level
        if (level > maxLevel) {
            result.add(node.val);
            maxLevel = level;
        }

        // Traverse left first to ensure leftmost node is visited first
        dfs(node.left, level + 1, result);
        dfs(node.right, level + 1, result);
    }
}
```

#### **Complexity Analysis**
- **Time Complexity:** `O(n)`  
  Each node is visited once.
- **Space Complexity:** `O(h)`  
  Recursion stack space, where `h` is the height of the tree (~`O(n)` in worst case for skewed tree).

#### **Dry Run Example**

Using the same tree:

```
        1
       / \
      2   3
     / \   \
    4   5   6
```

- Start at root (1, level 0): maxLevel = -1 → 0 → add 1
- Recurse left (node 2, level 1): maxLevel=0 → 1 → add 2
  - Recurse left (node 4, level 2): maxLevel=1 → 2 → add 4
  - Recurse right (node 5, level 2): maxLevel=2 → skip
- Recurse right (node 3, level 1): maxLevel=2 → skip
  - Recurse right (node 6, level 2): maxLevel=2 → skip

Result: `[1, 2, 4]`

---

### 3. Most Optimal Solution:
- The recursive DFS approach is generally preferred for simplicity and efficiency.
- Both solutions are `O(n)` in time, but DFS uses less auxiliary space compared to level order traversal, especially when the tree is balanced.

---

## Summary

| Approach | Method | Key Idea | Data Structure | Time Complexity | Space Complexity |
|------------|---------|--------------|------------------|-------------------|------------------|
| Brute Force | Level order traversal | Pick first node of each level | Queue | `O(n)` | `O(n)` |
| Recursive DFS | Pre-order traversal with level tracking | Record first node at each level | Recursion stack | `O(n)` | `O(h)` (height of tree) |

---

## Final Tip
- Use DFS if you prefer cleaner code with less auxiliary data structures.
- Use level-order traversal if you want a more intuitive approach related to "levels."

---

This concludes the comprehensive revision note for **Left View of Binary Tree**. Happy coding!
