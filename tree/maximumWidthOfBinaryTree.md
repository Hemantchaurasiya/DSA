# Maximum Width of Binary Tree

---

## Problem Statement
Given a binary tree, find the maximum width of the tree. The width of a level is defined as the length between the **leftmost** and **rightmost** non-null nodes, considering null nodes in between as placeholders. The maximum width among all levels is the answer.

---

## Approach 1: Brute Force Method

### 1. **Approach Name:**  
**Brute Force**

### 2. **Core Idea:**  
Perform a level-order traversal (BFS) of the tree, and for each level, determine the indices of the leftmost and rightmost non-null nodes. The maximum difference between these indices across all levels gives the maximum width.

### 3. **Algorithm:**  
- Traverse the tree level-by-level using a queue.  
- Assign each node an index based on its position if the tree were a complete binary tree (e.g., root = 0, left child = 2 * index, right child = 2 * index + 1).  
- For each level, record the index of the first and last node.  
- Calculate the width of the level as `last_index - first_index + 1`.  
- Keep track of the maximum width found throughout the traversal.

### 4. **Java Code:**
```java
import java.util.*;

public class Solution {
    public int widthOfBinaryTree(TreeNode root) {
        if (root == null) return 0;
        int maxWidth = 0;
        Queue<Pair<TreeNode, Integer>> queue = new LinkedList<>();
        queue.offer(new Pair<>(root, 0));  // Pair of node and its index

        while (!queue.isEmpty()) {
            int size = queue.size();
            int minIndex = queue.peek().getValue();  // The first index in the current level
            int firstIndex = 0, lastIndex = 0;

            for (int i = 0; i < size; i++) {
                Pair<TreeNode, Integer> current = queue.poll();
                int currIndex = current.getValue() - minIndex; // Normalize indices
                TreeNode node = current.getKey();

                if (i == 0) firstIndex = currIndex;
                if (i == size - 1) lastIndex = currIndex;

                if (node.left != null)
                    queue.offer(new Pair<>(node.left, 2 * currIndex));
                if (node.right != null)
                    queue.offer(new Pair<>(node.right, 2 * currIndex + 1));
            }

            maxWidth = Math.max(maxWidth, lastIndex - firstIndex + 1);
        }
        return maxWidth;
    }

    // Helper class for Pair (or use AbstractMap.SimpleEntry)
    public static class Pair<K, V> {
        private K key;
        private V value;

        public Pair(K key, V value) {
            this.key = key;
            this.value = value;
        }

        public K getKey() { return key; }
        public V getValue() { return value; }
    }
}
```

### 5. **Complexity Analysis:**  
- **Time Complexity:** `O(N)`, where N is the number of nodes, since each node is visited exactly once during BFS.  
- **Space Complexity:** `O(N)` in the worst case (for the queue and storing node indices).

### 6. **Dry Run Example:**

Suppose the tree:
```
        1
       / \
      3   2
     /     \
    5       9
```

- Level 0: Queue = [(1, 0)]  
  Width = 0 - 0 + 1 = 1

- Level 1: Queue after processing = [(3, 0), (2, 1)]  
  Min index = 0  
  First = 0, Last = 1  
  Width = 1 - 0 + 1 = 2

- Level 2: Queue after processing = [(5, 0), (9, 2)]  
  Min index = 0  
  First = 0, Last = 2  
  Width = 2 - 0 + 1 = 3

Max width = 3

---

## Approach 2: Optimized (Using Index Normalization)

### 1. **Approach Name:**  
**Optimized BFS with Index Normalization**

### 2. **Core Idea:**  
Similar to the brute-force approach but with the key optimization of normalizing indices at each level to prevent potential integer overflow and reduce large number handling.

### 3. **Algorithm:**  
- Use BFS traversal with a queue.  
- Assign indices to each node based on their position as if the tree is complete.  
- At each level, record the minimum index (of the first node) to normalize subsequent indices.  
- Calculate the width as `last_index - first_index + 1`.  
- Update maximum width during traversal.

*Note:* The code above already implements this approach.

### 4. **Java Code:**  
Same as Approach 1.

### 5. **Complexity Analysis:**  
Same as Approach 1.

---

## Approach 3: Recursive DFS (Optional, but less efficient)

### 1. **Approach Name:**  
**DFS with Index Mapping**

### 2. **Core Idea:**  
Use DFS traversal, passing along the current node's level and index, and store the minimum index at each level in a map.  
The width at each level is the difference between the current node's index and the first index recorded for that level.

### 3. **Algorithm:**  
- Maintain a map (or array) to record the first node's index at each depth.  
- Recursively traverse the tree, passing the current index.  
- At each node, update the maximum width as `current_index - first_index_at_level + 1`.  
- Update the first index for each level if not already set.

### 4. **Java Code:**
```java
import java.util.*;

public class Solution {
    private int maxWidth = 0;
    private Map<Integer, Integer> levelMinIndexMap = new HashMap<>();
    
    public int widthOfBinaryTree(TreeNode root) {
        dfs(root, 0, 0);
        return maxWidth;
    }
    
    private void dfs(TreeNode node, int level, int index) {
        if (node == null) return;
        levelMinIndexMap.putIfAbsent(level, index);
        int currentWidth = index - levelMinIndexMap.get(level) + 1;
        maxWidth = Math.max(maxWidth, currentWidth);
        dfs(node.left, level + 1, 2 * index);
        dfs(node.right, level + 1, 2 * index + 1);
    }

    // TreeNode class definition
    public static class TreeNode {
        int val;
        TreeNode left, right;
        TreeNode(int x) { val = x; }
    }
}
```

### 5. **Complexity Analysis:**  
- **Time Complexity:** `O(N)` as each node is visited once.  
- **Space Complexity:** `O(H)` where H is the height of the tree due to recursion stack and map storage.

---

## Summary:
| Approach | Core Idea | Key Data Structures | Time Complexity | Space Complexity | Notes |
|---|---|---|---|---|---|
| Brute Force | BFS with indices | Queue, Pair | `O(N)` | `O(N)` | Simple, intuitive, handles nulls implicitly |
| Optimized BFS | BFS with index normalization | Queue, Pair | `O(N)` | `O(N)` | Prevents integer overflow, efficient |
| DFS | Recursive traversal, level map | HashMap | `O(N)` | `O(H)` | Less common, elegant, uses recursion |

---

## Final Tips:
- Use BFS for level-wise processing, especially when needing width per level.
- Assign indices based on complete binary tree logic to handle nulls.
- Normalize indices at each level to avoid overflow.
- For deep trees, DFS with level-index map can be more memory-efficient.

---

This comprehensive guide should help you understand and implement the solution efficiently during interviews!
