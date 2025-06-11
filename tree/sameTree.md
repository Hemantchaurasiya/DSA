# Same Tree

---

## Problem Overview
Given two binary trees, **p** and **q**, determine if they are **the same**.  
Two binary trees are considered the same if they are structurally identical and the nodes have the same value.

---

## Approaches to Solve "Same Tree"

### 1. Brute Force Approach

#### Core Idea:
Compare the trees by traversing them simultaneously and checking if corresponding nodes are equal. If any mismatch occurs, return false.

#### Algorithm:
- Use recursion or DFS traversal.
- At each step:
  - If both nodes are null, they are the same at this point; return true.
  - If one node is null and the other isn't, return false.
  - If node values differ, return false.
  - Recursively check left subtrees and right subtrees.
- Final result is true only if all corresponding nodes match.

#### Java Code:
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (p == null || q == null) return false;
    if (p.val != q.val) return false;
    return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
}
```

#### Complexity Analysis:
- **Time Complexity:**  
  **O(N)**, where N is the number of nodes in the trees, because each node is visited exactly once during traversal.
- **Space Complexity:**  
  **O(H)**, where H is the height of the tree, due to recursion stack space in the worst case (balanced tree: **O(log N)**, skewed tree: **O(N)**).

---

### 2. Iterative Approach (Using Stack or Queue)

#### Core Idea:
Use an iterative traversal (e.g., BFS or DFS) with a data structure (stack or queue) to compare nodes level-by-level or node-by-node.

#### Algorithm:
- Initialize two stacks or queues with the root nodes of both trees.
- While both data structures are not empty:
  - Pop nodes from both.
  - If both are null, continue.
  - If one is null or values differ, return false.
  - Push their children in corresponding order.
- If traversal completes without mismatches, return true.

#### Java Code:
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    Queue<TreeNode> queue1 = new LinkedList<>();
    Queue<TreeNode> queue2 = new LinkedList<>();
    queue1.offer(p);
    queue2.offer(q);
    
    while (!queue1.isEmpty() && !queue2.isEmpty()) {
        TreeNode node1 = queue1.poll();
        TreeNode node2 = queue2.poll();
        
        if (node1 == null && node2 == null) continue;
        if (node1 == null || node2 == null || node1.val != node2.val) return false;
        
        queue1.offer(node1.left);
        queue1.offer(node1.right);
        queue2.offer(node2.left);
        queue2.offer(node2.right);
    }
    return queue1.isEmpty() && queue2.isEmpty();
}
```

#### Complexity Analysis:
- **Time Complexity:**  
  **O(N)**, visiting each node once.
- **Space Complexity:**  
  **O(N)**, for the queues storing nodes during traversal.

---

### 3. Most Optimal Solution: Recursive Deep Comparison

#### Core Idea:
Same as the brute-force recursive approach, but emphasizing the simplicity and clarity of the recursion.

#### Algorithm:
- Recursively compare nodes:
  - If both are null, return true.
  - If one is null or values differ, return false.
  - Recursively compare left children and right children.
- The entire trees are the same if the root nodes and their subtrees are the same.

#### Java Code:
```java
public boolean isSameTree(TreeNode p, TreeNode q) {
    if (p == null && q == null) return true;
    if (p == null || q == null || p.val != q.val) return false;
    return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
}
```

#### Complexity Analysis:
- **Time Complexity:**  
  **O(N)**, where N is the number of nodes; each node is visited once.
- **Space Complexity:**  
  **O(H)** due to recursion stack, where H is tree height.

---

## Dry Run Example

### Input:
```plaintext
Tree p:       Tree q:
    1             1
   / \           / \
  2   3         2   3
```

### Step-by-step for Recursive Approach:

| Step | p Node | q Node | Action | Result | Explanation                                             |
|--------|---------|---------|----------|---------|---------------------------------------------------------|
| 1      | 1       | 1       | Compare values | Same | Both roots are 1; proceed to children             |
| 2      | 2       | 2       | Compare values | Same | Left children match                                  |
| 3      | null    | null    | Both null | True   | Left children of 2 are null, match                   |
| 4      | null    | null    | Both null | True   | Right children of 2 are null, match                  |
| 5      | 3       | 3       | Compare values | Same | Right children of root are 3, match                |
| 6      | null    | null    | Both null | True   | Children of 3 are null, match                        |
| 7      | null    | null    | Both null | True   | Right subtree traversal complete                     |

**Result:** All comparisons return true, so the trees are identical.

---

## Summary
- **Recursive approach** is clean, simple, and efficient.
- **Iterative approach** is useful if recursion stack might be a concern.
- **Time complexity** is **O(N)** for all methods.
- **Space complexity** varies from **O(H)** for recursion (stack space) to **O(N)** for iterative traversal (queue/stack).

---

Feel free to review these approaches and practice implementing them during your interview prep!
