# All Nodes Distance K in Binary Tree

This problem asks: Given a binary tree, a target node, and an integer `K`, find all nodes in the tree that are exactly `K` edges away from the target node.

---

## Approach 1: Brute Force

### Core Idea
- **Find all nodes at distance `K` from the target node** by traversing the entire tree.
- For each node, **check if it is at distance `K` from the target** by computing the distance from the target node to every other node.

### Algorithm
1. **Find the target node** in the tree.
2. For every node in the tree:
   - Compute the distance to the target node if the target is in its subtree.
   - If the current node is at distance `K` from the target, add it to the result.
3. Use **recursive functions**:
   - One to **find the target node**.
   - One to **calculate distance from the current node to the target**.
   
### Java Code

```java
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}

public class Solution {
    List<Integer> result = new ArrayList<>();

    // Main function
    public List<Integer> distanceK(TreeNode root, TreeNode target, int K) {
        dfs(root, target, K);
        return result;
    }

    // Helper to find nodes at distance K in subtree rooted at node
    private int subtreeAdd(TreeNode node, int distance) {
        if (node == null) return -1;
        if (distance == 0) {
            result.add(node.val);
            return 0;
        }
        int left = subtreeAdd(node.left, distance - 1);
        int right = subtreeAdd(node.right, distance - 1);
        return -1;
    }

    // Helper to find target and process nodes at distance K
    private int dfs(TreeNode node, TreeNode target, int K) {
        if (node == null) return -1;
        if (node == target) {
            subtreeAdd(node, K);
            return 0;
        }

        int L = dfs(node.left, target, K);
        if (L != -1) {
            if (L + 1 == K) result.add(node.val);
            else subtreeAdd(node.right, K - L - 2);
            return L + 1;
        }

        int R = dfs(node.right, target, K);
        if (R != -1) {
            if (R + 1 == K) result.add(node.val);
            else subtreeAdd(node.left, K - R - 2);
            return R + 1;
        }
        return -1;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  - **O(N^2):** For each node, we may traverse its subtree, leading to quadratic behavior in the worst case.
- **Space Complexity:**  
  - **O(N):** For recursion stack and result storage.

---

## Approach 2: Improved Approach using Parent Pointers + BFS (Optimal Solution)

### Core Idea
- Convert the binary tree into an **undirected graph** by assigning **parent pointers**.
- Use **Breadth-First Search (BFS)** starting from the target node:
  - Traverse **upwards** (via parent links) and **downwards** (via left/right child links).
  - Collect nodes at distance `K`.

### Algorithm
1. **Create a parent map**:
   - Traverse the tree and record each node's parent.
2. **Perform BFS**:
   - Initialize a queue with the target node.
   - Keep a visited set to avoid revisiting nodes.
   - For each level, process all nodes at the current distance.
   - When the current distance reaches `K`, collect all nodes at this level.
3. **Return the collected nodes**.

### Java Code

```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}

public class Solution {
    public List<Integer> distanceK(TreeNode root, TreeNode target, int K) {
        Map<TreeNode, TreeNode> parentMap = new HashMap<>();
        buildParentMap(root, null, parentMap);
        return bfs(target, parentMap, K);
    }

    private void buildParentMap(TreeNode node, TreeNode parent, Map<TreeNode, TreeNode> parentMap) {
        if (node == null) return;
        parentMap.put(node, parent);
        buildParentMap(node.left, node, parentMap);
        buildParentMap(node.right, node, parentMap);
    }

    private List<Integer> bfs(TreeNode target, Map<TreeNode, TreeNode> parentMap, int K) {
        Queue<TreeNode> queue = new LinkedList<>();
        Set<TreeNode> visited = new HashSet<>();
        List<Integer> result = new ArrayList<>();

        queue.offer(target);
        visited.add(target);
        int distance = 0;

        while (!queue.isEmpty()) {
            int size = queue.size();
            if (distance == K) {
                for (TreeNode node : queue) {
                    result.add(node.val);
                }
                break;
            }
            for (int i = 0; i < size; i++) {
                TreeNode current = queue.poll();
                // Check neighbors: left, right, parent
                if (current.left != null && !visited.contains(current.left)) {
                    visited.add(current.left);
                    queue.offer(current.left);
                }
                if (current.right != null && !visited.contains(current.right)) {
                    visited.add(current.right);
                    queue.offer(current.right);
                }
                TreeNode parent = parentMap.get(current);
                if (parent != null && !visited.contains(parent)) {
                    visited.add(parent);
                    queue.offer(parent);
                }
            }
            distance++;
        }
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  - **O(N):**  
    - Building parent map: O(N)  
    - BFS traversal: O(N)
- **Space Complexity:**  
  - **O(N):**  
    - Parent map storage, queue, and visited set.

---

## Dry Run Example (Using Approach 2)

Suppose the Tree:

```
        3
       / \
      5   1
     / \   \
    6   2   8
       / \
      7   4
```

Target: Node with value `5`, `K = 2`

**Step 1:** Build parent map:

| Node | Parent |
|---------|---------|
| 3       | null    |
| 5       | 3       |
| 1       | 3       |
| 6       | 5       |
| 2       | 5       |
| 8       | 1       |
| 7       | 2       |
| 4       | 2       |

**Step 2:** BFS from target (node 5):

- Distance 0: Queue = [5], Result = []
- Distance 1: From 5, neighbors: 3, 6, 2
  - Queue = [3, 6, 2]
- Distance 2: From 3, neighbors: 1, 5 (already visited)
  - From 6: no new nodes
  - From 2: neighbors 7, 4, 5 (visited)
  - Queue = [1, 7, 4]

**Result:** Nodes at distance 2 are **1, 7, 4**

---

## Summary

| Approach | Core Idea | Time Complexity | Space Complexity | When to Use |
|------------|--------------|-------------------|---------------------|--------------|
| **Brute Force** | Find all nodes at distance K by traversing all nodes repeatedly | O(N^2) | O(N) | Small trees or quick implementation |
| **Optimized BFS + Parent Map** | Convert tree to graph, BFS from target | O(N) | O(N) | Large trees, performance critical |

---

## Final Tips
- Use the parent pointer + BFS approach for optimal performance.
- Remember to handle edge cases: target node not in tree, K = 0, etc.
- Practice tree traversal, BFS, and parent pointer techniques to master this problem.

---

**Happy Coding!**
