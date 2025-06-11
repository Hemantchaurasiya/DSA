# Binary Tree Right Side View

This guide provides a comprehensive overview of solving the **Binary Tree Right Side View** problem, including multiple approaches from brute-force to optimal solutions. It covers intuition, algorithms, code, complexity analysis, and dry runs to aid in interview preparation and quick revision.

---

## Problem Statement
Given the **root** of a binary tree, return **the list of node values visible from the right side** when the tree is viewed from the right.

---

## Approach 1: Brute Force - Level Order Traversal (BFS) with Extra Storage

### Core Idea
Perform a level order traversal (BFS) of the tree. For each level, the rightmost node is visible from the right side.

### Algorithm
1. Use a queue to perform BFS traversal.
2. For each level:
   - Traverse all nodes at that level.
   - Keep track of the last node in the current level.
   - Add the value of this last node to the result list.
3. Continue until all levels are processed.

### Java Code
```java
import java.util.*;

public List<Integer> rightSideView(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        int size = queue.size();
        for (int i = 0; i < size; i++) {
            TreeNode current = queue.poll();
            // If it's the last node in this level, add to result
            if (i == size - 1) {
                result.add(current.val);
            }
            if (current.left != null) queue.offer(current.left);
            if (current.right != null) queue.offer(current.right);
        }
    }
    return result;
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, where N is the number of nodes, since each node is visited exactly once.
- **Space Complexity:** **O(N)**, due to the queue storing nodes at each level in the worst case.

### Dry Run Example
Suppose the binary tree:

```
       1
     /   \
    2     3
     \     \
      5     4
```

- Level 0: queue = [1], last node = 1 → result = [1]
- Level 1: queue = [2, 3], last node = 3 → result = [1, 3]
- Level 2: queue = [5, 4], last node = 4 → result = [1, 3, 4]

---

## Approach 2: Depth-First Search (DFS) - Right-First Traversal

### Core Idea
Traverse the tree using DFS, prioritizing the **right child** before the left child. Keep track of the **maximum depth** visited so far. For each depth, the first node encountered from the right is the visible node.

### Algorithm
1. Initialize a list to store the right view.
2. Create a recursive DFS function with parameters: current node, current depth.
3. At each node:
   - If the current depth equals the size of the result list, add this node's value (first node encountered at this depth).
   - Recursively traverse the **right child** first, then the **left child**.
4. Continue until all nodes are visited.

### Java Code
```java
public List<Integer> rightSideView(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    dfs(root, 0, result);
    return result;
}

private void dfs(TreeNode node, int depth, List<Integer> result) {
    if (node == null) return;
    if (depth == result.size()) {
        result.add(node.val); // First node at this depth (from right side)
    }
    dfs(node.right, depth + 1, result);
    dfs(node.left, depth + 1, result);
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, as each node is visited once.
- **Space Complexity:** **O(N)**, for the recursion stack and result storage.

### Dry Run Example
Using the same tree:

```
       1
     /   \
    2     3
     \     \
      5     4
```

- Start at root (1), depth=0, result=[]
  - Add 1 (depth=0, result size=0)
  - Recurse right: node=3, depth=1
    - Add 3 (depth=1, result=[1])
    - Recurse right: node=4, depth=2
      - Add 4 (depth=2, result=[1,3])
    - Recurse left: null
  - Recurse left: node=2, depth=1
    - result already has size=1, do not add 2
    - Recurse right: node=5, depth=2
      - result size=3, do nothing
    - Recurse left: null

Result: `[1, 3, 4]`

---

## Approach 3: Optimized BFS with Queue (Most Efficient)

### Core Idea
Same as Approach 1, but emphasizing that BFS from right to left can be optimized by processing nodes in a specific order (right child first).

### Algorithm
1. Use a queue for BFS.
2. For each level:
   - Process nodes in **right-to-left order**:
     - Enqueue right child first, then left child.
   - The first node processed at each level is the rightmost node.
3. Append that node's value to the result list.

### Java Code
```java
public List<Integer> rightSideView(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);

    while (!queue.isEmpty()) {
        int size = queue.size();
        // Process from right to left
        for (int i = 0; i < size; i++) {
            TreeNode current = queue.poll();
            // The first node at this level from right side
            if (i == 0) {
                result.add(current.val);
            }
            // Enqueue right first, then left
            if (current.right != null) queue.offer(current.right);
            if (current.left != null) queue.offer(current.left);
        }
    }
    return result;
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, each node visited once.
- **Space Complexity:** **O(N)**, due to queue storage.

### Dry Run
Same tree:

```
       1
     /   \
    2     3
     \     \
      5     4
```

- Level 0: queue=[1], process 1 → add 1, enqueue right (3), then left (2)
- Level 1: queue=[3,2]
  - Process 3: add 3, enqueue right (4)
  - Process 2: do not add, enqueue right (5)
- Level 2: queue=[4,5]
  - Process 4: add 4
  - Process 5: do not add

Result: `[1, 3, 4]`

---

## Summary Table

| Approach | Intuition | Best For | Code Style | Time | Space |
|---|---|---|---|---|---|
| BFS Level Order | Traverse level by level, pick last node | When simplicity is preferred | Clear and straightforward | **O(N)** | **O(N)** |
| DFS Right-First | Traverse right subtree first, track depth | When recursion is comfortable | Elegant, concise | **O(N)** | **O(N)** (recursion stack) |
| BFS with Right-to-Left | BFS, enqueue right before left | Slight optimization over first BFS | Slightly more complex | **O(N)** | **O(N)** |

---

## Final Tips
- **Choose BFS or DFS** based on preference; both achieve optimal time complexity.
- **DFS** is more elegant and less space-consuming if recursion stack is small.
- **BFS** is intuitive and easy to understand, especially for level-based problems.
- Always consider edge cases: **null root**, skewed trees, etc.

---

**Happy Coding!**
