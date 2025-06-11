# Binary Tree Level Order Traversal

---

## Problem Overview
Given a binary tree, return the level order traversal of its nodes' values. (i.e., from left to right, level by level).

---

## Approach 1: Brute Force (Using Recursion and Level Tracking)

### 1. Approach Name:
**Brute Force (Recursive Level Tracking)**

### 2. Core Idea:
Traverse the tree recursively, keeping track of the current depth (level). For each node, add its value to a list corresponding to its level.

### 3. Algorithm:
- Use a helper function that takes the current node and the current level.
- If the current node is `null`, return.
- If the list for the current level doesn't exist, create it.
- Add the current node's value to the list for the current level.
- Recursively call the helper for the left and right children, increasing the level by 1.
- After traversal, the list of lists will contain nodes grouped by their levels.

### 4. Java Code:
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        helper(root, 0, result);
        return result;
    }

    private void helper(TreeNode node, int level, List<List<Integer>> result) {
        if (node == null) return;
        
        if (result.size() == level) {
            result.add(new ArrayList<>());
        }
        result.get(level).add(node.val);
        helper(node.left, level + 1, result);
        helper(node.right, level + 1, result);
    }
}
```

### 5. Complexity Analysis:
- **Time Complexity:** **O(N)**, where N is the number of nodes, as each node is visited exactly once.
- **Space Complexity:** **O(N)**, for the output list and recursion stack (in the worst case, skewed tree).

### 6. Dry Run:

**Sample Input:**
```
        1
       / \
      2   3
     /   / \
    4   5   6
```

**Step-by-step:**

| Call Stack / Level | Node Value | Result List                 | Explanation                                    |
|---------------------|--------------|------------------------------|------------------------------------------------|
| helper(1, 0)        | 1            | [[1]]                        | Add level 0 list, add 1                       |
| helper(2, 1)        | 2            | [[1], [2]]                   | Level 1, add 2                                |
| helper(4, 2)        | 4            | [[1], [2], [4]]              | Level 2, add 4                                |
| helper(null, 3)     | null         | -                            | Null, return                                  |
| helper(null, 3)     | null         | -                            | Null, return                                  |
| back to node 2, helper(3, 1) | 3 | [[1], [2,3]] | Level 1, add 3 to existing list             |
| helper(5, 2)        | 5            | [[1], [2, 3], [4, 5]]        | Level 2, add 5                                |
| helper(null, 3)     | null         | -                            | Null, return                                  |
| helper(null, 3)     | null         | -                            | Null, return                                  |
| helper(6, 2)        | 6            | [[1], [2, 3], [4, 5, 6]]     | Level 2, add 6                                |
| helper(null, 3)     | null         | -                            | Null, return                                  |
| helper(null, 3)     | null         | -                            | Null, return                                  |

---

## Approach 2: Iterative Solution Using Queue (BFS)

### 1. Approach Name:
**Iterative BFS with a Queue**

### 2. Core Idea:
Use a **Breadth-First Search (BFS)** traversal with a queue to process nodes level by level. Enqueue the root, then process all nodes at the current level before moving to the next level.

### 3. Algorithm:
- Initialize a `Queue<TreeNode>` and enqueue the root node.
- While the queue is not empty:
  - Determine the number of nodes at the current level (`size = queue.size()`).
  - Create a list to hold current level's node values.
  - Loop `size` times:
    - Dequeue a node.
    - Add its value to the current level list.
    - Enqueue its left child if exists.
    - Enqueue its right child if exists.
  - Add the current level list to the result.
- Return the list of lists after traversal.

### 4. Java Code:
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) return result;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int size = queue.size();
            List<Integer> currentLevel = new ArrayList<>();

            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                currentLevel.add(node.val);

                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            result.add(currentLevel);
        }

        return result;
    }
}
```

### 5. Complexity Analysis:
- **Time Complexity:** **O(N)**, as each node is enqueued and dequeued exactly once.
- **Space Complexity:** **O(N)**, for the queue and output list.

### 6. Dry Run:

**Sample Input:**
```
        1
       / \
      2   3
     /   / \
    4   5   6
```

**Step-by-step:**

| Queue Content          | Current Level List | Result After Level | Explanation                            |
|------------------------|---------------------|---------------------|----------------------------------------|
| [1]                   | []                  | [[1]]              | Dequeue 1, enqueue 2, 3               |
| [2, 3]                | [2, 3]             | [[1], [2, 3]]      | Process 2 and 3, enqueue children 4,5,6 |
| [4, 5, 6]             | [4, 5, 6]          | [[1], [2, 3], [4, 5, 6]] | Process 4, 5, 6; enqueue children if any |
| []                    | -                   | End                | Queue empty, finish traversal       |

---

## Approach 3: Most Optimal Solution - BFS with Queue (Already Covered)

This is typically the standard and most efficient approach for level order traversal, with **O(N)** time and space complexity.

---

## Summary Table

| Approach | Time Complexity | Space Complexity | Main Idea |
| --- | --- | --- | --- |
| Recursive Level Tracking | O(N) | O(N) | Traverse recursively, tracking level |
| Iterative BFS with Queue | O(N) | O(N) | Use a queue for level-by-level traversal |

---

## Final Tips for Interviews:
- Remember to handle null root cases.
- Use appropriate data structures (`Queue` for BFS).
- Keep track of the current level size to process nodes level-wise.
- Understand recursive vs iterative approaches; both are valid.

---

**Happy practicing!**
