# House Robber III

---

## Problem Overview
**House Robber III**: Given a binary tree where each node contains some amount of money, determine the maximum amount you can rob without alerting the police. The constraint is that you cannot rob two directly connected nodes (i.e., parent and child).

---

## Approach 1: Brute Force (Recursive without Memoization)

### Core Idea
For each node, decide whether to rob it or not:
- If you rob the current node, you cannot rob its children.
- If you skip the current node, you are free to rob its children.

### Algorithm
1. For each node:
   - Calculate the maximum amount if **rob**ing current node:
     - Sum of current node's value + **not** robbing the left and right children.
   - Calculate the maximum amount if **not** robbing current node:
     - Sum of the maximum amounts from robbing or not robbing its children.
2. Recursively do this for all nodes.
3. Return the maximum between robbing and not robbing the root.

### Java Code
```java
public class Solution {
    public int rob(TreeNode root) {
        if (root == null) return 0;
        int moneyWithRoot = root.val;
        if (root.left != null) {
            moneyWithRoot += rob(root.left.left) + rob(root.left.right);
        }
        if (root.right != null) {
            moneyWithRoot += rob(root.right.left) + rob(root.right.right);
        }
        int moneyWithoutRoot = rob(root.left) + rob(root.right);
        return Math.max(moneyWithRoot, moneyWithoutRoot);
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(2^n)** in the worst case, since each node makes recursive calls for its children, and overlapping subproblems are not cached.
- **Space Complexity:**  
  **O(h)**, where `h` is the height of the tree, due to recursion stack.

### Dry Run (Sample Tree)
```
       3
      / \
     2   3
      \    \
       3    1
```
- Starting at root (3):
  - Rob root (3): sum = 3 + rob(NULL) + rob(NULL) for children, plus grandchildren:
    - For left child (2):
      - Rob (2): 2 + rob(NULL) + rob(3)
      - Not rob (2): rob(NULL) + rob(3)
    - For right child (3):
      - Rob (3): 3 + rob(NULL) + rob(1)
      - Not rob (3): rob(NULL) + rob(1)
- This approach recalculates subproblems multiple times, leading to exponential time.

---

## Approach 2: Top-Down with Memoization

### Core Idea
Use memoization to cache results for each node to avoid recomputation, thus reducing time complexity.

### Algorithm
1. Use a hash map (or dictionary) to store the maximum amount that can be robbed starting from each node.
2. For each node:
   - If the result is cached, return it.
   - Compute the sum if robbing the current node + not robbing children.
   - Compute the sum if skipping the current node and robbing children.
   - Cache and return the maximum.

### Java Code
```java
import java.util.HashMap;

public class Solution {
    private HashMap<TreeNode, Integer> memo = new HashMap<>();
    
    public int rob(TreeNode root) {
        if (root == null) return 0;
        if (memo.containsKey(root)) return memo.get(root);
        
        int robRoot = root.val;
        if (root.left != null) {
            robRoot += rob(root.left.left) + rob(root.left.right);
        }
        if (root.right != null) {
            robRoot += rob(root.right.left) + rob(root.right.right);
        }
        
        int skipRoot = rob(root.left) + rob(root.right);
        int result = Math.max(robRoot, skipRoot);
        memo.put(root, result);
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(n)**, since each node's result is computed once and cached.
- **Space Complexity:**  
  **O(n)** for the recursion stack and memoization storage.

### Dry Run
Same as previous, but with caching:
- Once a node's max amount is computed, it is stored in `memo` to avoid recomputation.
- Ensures the overall time complexity is linear.

---

## Approach 3: Bottom-Up Dynamic Programming (Most Optimal)

### Core Idea
Perform a post-order traversal:
- For each node, compute two values:
  - **`rob`**: maximum amount when robbing this node.
  - **`notRob`**: maximum amount when not robbing this node.
- These values are propagated up from children to parent, combining results optimally.

### Algorithm
1. Define a helper function that returns a pair:
   - First element: maximum amount when robbing current node.
   - Second element: maximum amount when not robbing current node.
2. For each node:
   - Recursively compute for left and right children.
   - `rob` for current node = node value + left.notRob + right.notRob.
   - `notRob` for current node = max(left.rob, left.notRob) + max(right.rob, right.notRob).
3. At the root, return the maximum of `rob` and `notRob`.

### Java Code
```java
public class Solution {
    private class Result {
        int rob, notRob;
        Result(int rob, int notRob) {
            this.rob = rob;
            this.notRob = notRob;
        }
    }
    
    public int rob(TreeNode root) {
        Result res = dfs(root);
        return Math.max(res.rob, res.notRob);
    }
    
    private Result dfs(TreeNode node) {
        if (node == null) {
            return new Result(0, 0);
        }
        Result left = dfs(node.left);
        Result right = dfs(node.right);
        
        int rob = node.val + left.notRob + right.notRob;
        int notRob = Math.max(left.rob, left.notRob) + Math.max(right.rob, right.notRob);
        
        return new Result(rob, notRob);
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  **O(n)**, each node is visited once during post-order traversal.
- **Space Complexity:**  
  **O(h)**, height of the tree for recursion stack.

### Dry Run (Sample Tree)
```
       3
      / \
     2   3
      \    \
       3    1
```

| Node | `rob` (if robbed) | `notRob` (if not robbed) | Explanation                                               |
|--------|-------------------|--------------------------|------------------------------------------------------------|
| 3 (leaf) | 3               | 0                        | Leaf node: rob=3, notRob=0                                |
| 2 (parent of 3) | 2 + 0 + 0 = 2 | max(0,0)+max(3,0)=3 | rob: rob 2 + notRob of children; notRob: max of children |
| 3 (right child) | 3 + 0 + 1=4 | max(0,0)+max(1,0)=1 | similar calculation for right side                         |
| Root (3) | 3 + 3 + 1=7 | max(2,3)+max(4,1)=3+4=7 | Final maximum amount                                       |

Result: **7**.

---

## Summary

| Approach | Technique | Time Complexity | Space Complexity | Key Idea | Suitable for |
|------------|--------------|-------------------|-------------------|------------|--------------|
| Brute Force | Recursive | **O(2^n)** | **O(h)** | Recurse over all possibilities, no caching | Small trees, conceptual understanding |
| Memoization | Top-Down DP | **O(n)** | **O(n)** | Cache results to avoid recomputation | Larger trees, optimized solution |
| Bottom-Up DP | Post-order traversal | **O(n)** | **O(h)** | Calculate rob/notRob at each node, propagate results | Most efficient for large trees |

---

## Final Tips for Interview
- Focus on the **bottom-up DP approach** for optimality.
- Understand the **pair of values (rob, notRob)** at each node and how to combine them.
- Practice implementing the solution recursively and with memoization.
- Be able to explain the intuition behind choosing to rob or skip a node.

---

**Happy Revising!**
