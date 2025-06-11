# Binary Tree Level Order Traversal II

This problem involves traversing a binary tree level-by-level but returning the result from bottom to top. The goal is to generate a list of lists, where each sublist contains node values at a given level, starting from the bottommost level up to the root.

---

## 1. **Approach: Brute Force**

### **Core Idea:**
Perform a standard level order traversal (top-down) and then reverse the result at the end to get bottom-up order.

### **Algorithm:**
1. Use a queue to perform a **Breadth-First Search (BFS)** starting from the root.
2. For each level:
    - Dequeue nodes of the current level.
    - Collect their values in a temporary list.
    - Enqueue their children for the next level.
3. Append each level's list to a result list.
4. After traversal, **reverse** the result list to get bottom-up order.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> result = new ArrayList<>();
        if (root == null) return result;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> currentLevel = new ArrayList<>();

            for (int i = 0; i < levelSize; i++) {
                TreeNode node = queue.poll();
                currentLevel.add(node.val);
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }

            result.add(currentLevel);
        }

        // Reverse the list to get bottom-up order
        Collections.reverse(result);
        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  **O(N)**, where N is the number of nodes, since each node is visited exactly once.
- **Space Complexity:**  
  **O(N)**, for storing the result and the queue in the worst case (full binary tree).

---

## 2. **Approach: Using DFS with Depth Tracking**

### **Core Idea:**
Perform a **Depth-First Search (DFS)** traversal, tracking the depth of each node, and build the list of levels from top to bottom. Then, reverse the list at the end.

### **Algorithm:**
1. Initialize an empty list to hold levels.
2. Define a recursive DFS function with parameters: current node and current depth.
3. In DFS:
    - If the current node is null, return.
    - If the current depth equals the size of the result list, add a new list for this depth.
    - Append the current node's value to the list corresponding to its depth.
    - Recurse for left and right children with depth + 1.
4. After DFS traversal, reverse the list to get bottom-up order.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        List<List<Integer>> levels = new ArrayList<>();
        dfs(root, 0, levels);

        Collections.reverse(levels);
        return levels;
    }

    private void dfs(TreeNode node, int depth, List<List<Integer>> levels) {
        if (node == null) return;

        if (depth == levels.size()) {
            levels.add(new ArrayList<>());
        }

        levels.get(depth).add(node.val);
        dfs(node.left, depth + 1, levels);
        dfs(node.right, depth + 1, levels);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  **O(N)**, as each node is visited once.
- **Space Complexity:**  
  **O(N)**, for storing levels and recursion stack in worst case.

---

## 3. **Approach: Optimized BFS with Reversal During Traversal**

### **Core Idea:**
Instead of reversing at the end, insert each level at the beginning of the result list during BFS, thus building the bottom-up order on-the-fly.

### **Algorithm:**
1. Use BFS as before with a queue.
2. For each level:
    - Collect node values.
    - Instead of appending to the end, **insert at the beginning** of the result list.
3. Return the result list directly, which now contains levels from bottom to top.

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        LinkedList<List<Integer>> result = new LinkedList<>();
        if (root == null) return result;

        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int levelSize = queue.size();
            List<Integer> currentLevel = new ArrayList<>();

            for (int i = 0; i < levelSize; i++) {
                TreeNode node = queue.poll();
                currentLevel.add(node.val);
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }

            // Insert at the beginning for bottom-up order
            result.addFirst(currentLevel);
        }

        return result;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  **O(N)**, each node visited once.
- **Space Complexity:**  
  **O(N)**, for the result list and queue.

---

## **Summary of Approaches**

| Approach | Idea | Data Structure | Reverse at End | Reverse During Build | Code Efficiency |
|------------|-------|----------------|------------------|----------------------|-----------------|
| Brute Force | BFS + reverse | Queue + List | Yes | No | Simple, intuitive |
| DFS + Depth | DFS + level list + reverse | Call stack + List | Yes | No | Recursive, elegant |
| BFS with Prepend | BFS + insert at beginning | Queue + LinkedList | No | Yes | Efficient, less reversal |

---

## **Dry Run Example**

### Sample Tree:
```
        3
       / \
      9   20
         /  \
        15   7
```

### Expected Output:
```
[
  [15, 7],
  [9, 20],
  [3]
]
```

### **Approach 1: Brute Force**

**Step-by-step:**
- Traverse level by level:
  - Level 1: [3]
  - Level 2: [9, 20]
  - Level 3: [15, 7]
- Result after BFS: [[3], [9, 20], [15, 7]]
- Reverse: [[15, 7], [9, 20], [3]]

---

This comprehensive guide should serve as an effective quick revision and interview prep resource for the **Binary Tree Level Order Traversal II** problem.
