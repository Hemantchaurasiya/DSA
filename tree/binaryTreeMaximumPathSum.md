# Binary Tree Maximum Path Sum - DSA Revision Note

This problem involves finding the maximum path sum in a binary tree, where a path can start and end at any node, traversing parent-child connections.

---

## 1. **Approach Name:** Brute Force (Recursive Path Enumeration)

### **Core Idea:**
- Consider every node as a potential "highest" point of the path.
- For each node, compute the maximum path sum passing through it, considering left and right subtrees.
- The global maximum is updated during traversal.

### **Algorithm:**
1. For each node:
   - Compute the maximum path sum **including** the node and **possibly** extending to its left and right children.
   - The path sum **passing through node** = node's value + max(0, left max sum) + max(0, right max sum).
2. Use a recursive function to:
   - Compute the maximum sum **including** the current node (which can be used by parent nodes).
   - During traversal, update a global variable that tracks the maximum path sum seen so far.
3. The recursion explores all nodes, ensuring all possible paths are considered.

### **Java Code:**
```java
public class Solution {
    private int maxSum = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return maxSum;
    }

    private int maxGain(TreeNode node) {
        if (node == null) return 0;

        int leftGain = Math.max(maxGain(node.left), 0);
        int rightGain = Math.max(maxGain(node.right), 0);

        int currentPathSum = node.val + leftGain + rightGain;

        maxSum = Math.max(maxSum, currentPathSum);

        return node.val + Math.max(leftGain, rightGain);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N)**, where N is the number of nodes, since each node is visited once.
- **Space Complexity:** **O(H)**, where H is the height of the tree, due to recursion stack.

---

## 2. **Approach Name:** Top-Down Dynamic Programming (with Memoization)

### **Core Idea:**
- Use memoization to avoid recalculating maximum gains for subtrees.
- Similar to brute force but stores intermediate results to optimize.

### **Algorithm:**
- For each node, store the maximum gain in a cache.
- When computing max gain for a node, check cache first.
- The rest of the logic remains similar to brute force.

### **Note:**
- In this problem, since each node is processed once in a DFS, memoization isn't significantly different from the previous approach.
- The primary optimization is to avoid recomputation in more complex trees or overlapping subproblems.

### **Java Code:**
*(Similar to approach 1, with added memoization if needed)*

```java
public class Solution {
    private int maxSum = Integer.MIN_VALUE;
    private Map<TreeNode, Integer> memo = new HashMap<>();

    public int maxPathSum(TreeNode root) {
        maxGain(root);
        return maxSum;
    }

    private int maxGain(TreeNode node) {
        if (node == null) return 0;
        if (memo.containsKey(node)) return memo.get(node);

        int leftGain = Math.max(maxGain(node.left), 0);
        int rightGain = Math.max(maxGain(node.right), 0);

        int currentPathSum = node.val + leftGain + rightGain;
        maxSum = Math.max(maxSum, currentPathSum);

        int gain = node.val + Math.max(leftGain, rightGain);
        memo.put(node, gain);
        return gain;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N)**, each node computed once.
- **Space Complexity:** **O(N)**, for memoization and recursion stack.

---

## 3. **Approach Name:** Optimized Solution (Single Pass with Global Variable)

### **Core Idea:**
- Use a single DFS traversal.
- At each node, compute the maximum gain from its left and right subtrees.
- Update a global maximum considering the node as the highest point in a path.
- Return the maximum gain **including** current node to its parent.

### **Algorithm:**
1. Initialize a global variable `maxSum` to `Integer.MIN_VALUE`.
2. Define a recursive function `dfs(node)`:
   - If `node` is null, return 0.
   - Recursively compute `leftGain` and `rightGain`.
   - Compute `priceNewpath` as `node.val + max(leftGain, 0) + max(rightGain, 0)`.
   - Update `maxSum` with `max(maxSum, priceNewpath)`.
   - Return `node.val + max(max(leftGain, rightGain), 0)` to the parent.
3. Call `dfs(root)` and return `maxSum`.

### **Java Code:**
```java
public class Solution {
    private int maxSum = Integer.MIN_VALUE;

    public int maxPathSum(TreeNode root) {
        dfs(root);
        return maxSum;
    }

    private int dfs(TreeNode node) {
        if (node == null) return 0;

        int leftGain = Math.max(dfs(node.left), 0);
        int rightGain = Math.max(dfs(node.right), 0);

        int priceNewpath = node.val + leftGain + rightGain;

        maxSum = Math.max(maxSum, priceNewpath);

        return node.val + Math.max(leftGain, rightGain);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N)**, each node processed once.
- **Space Complexity:** **O(H)**, recursion stack space, where H is height of the tree.

---

## 4. **Dry Run with Diagram**

### **Sample Input:**

```
        -10
        /  \
       9   20
           /  \
          15   7
```

### **Step-by-step Execution:**

| Step | Node | Left Gain | Right Gain | Price New Path | Max Sum Updated | Return Value to Parent |
|-------|--------|--------------|--------------|----------------|----------------|------------------------|
| 1     | 15     | 0            | 0            | 15 + 0 + 0 = 15 | maxSum = 15     | 15                     |
| 2     | 7      | 0            | 0            | 7 + 0 + 0 = 7  | maxSum = 15     | 7                      |
| 3     | 20     | max(15,0)=15 | max(7,0)=7   | 20 + 15 + 7 = 42 | maxSum = 42    | 20 + max(15,7)=20     |
| 4     | 9      | 0            | 0            | 9 + 0 + 0 = 9  | maxSum = 42     | 9                      |
| 5     | -10    | max(9,0)=9   | max(20,0)=20 | -10 + 9 + 20=19 | maxSum = 42    | -10 + max(9,20)=10    |

### **Final Result:**

- The maximum path sum is **42**, corresponding to the path **15 -> 20 -> 7** or **15 -> 20 -> 7** (both paths have the same sum).

---

## **Summary:**

| Approach | Key Idea | Implementation | Time | Space |
|------------|------------|----------------|---------|--------|
| Brute Force | Enumerate all paths | Recursive with global update | O(N) | O(H) |
| Memoized DFS | Cache results to avoid recomputation | Recursive + HashMap | O(N) | O(N) |
| Optimized DFS | Single pass, update global max | Recursive with return value | O(N) | O(H) |

---

## **Key Takeaway:**
- The most optimal solution involves a **single DFS traversal** with **post-order processing**.
- Maintain a **global maximum** and return the **maximum gain** from each node.
- Handles both negative and positive node values gracefully.

---

Feel free to review this guide for quick revision before interviews!
