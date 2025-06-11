# Path Sum II - DSA Revision Guide

---

## Problem Overview
Given a binary tree and a target sum, find all root-to-leaf paths where the sum of node values equals the target sum.

---

## Approach 1: Brute Force (Backtracking with Path Tracking)

### Core Idea
Traverse all root-to-leaf paths, summing node values along each path. When a leaf node is reached, check if the accumulated sum matches the target. If yes, add the path to the result list.

### Algorithm
1. Use **DFS traversal** starting from the root.
2. Maintain a **current path list** and **current sum** during traversal.
3. When visiting a node:
    - Add the node value to the current path and sum.
    - If the node is a leaf:
        - Check if the sum equals the target.
        - If yes, add the current path to the answer list.
    - Else, recurse to left and right children.
4. Backtrack by removing the current node from the path when returning from recursion.

### Java Code
```java
public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
    List<List<Integer>> result = new ArrayList<>();
    backtrack(root, targetSum, new ArrayList<>(), result);
    return result;
}

private void backtrack(TreeNode node, int remainingSum, List<Integer> currentPath, List<List<Integer>> result) {
    if (node == null) return;
    
    currentPath.add(node.val);
    remainingSum -= node.val;
    
    if (node.left == null && node.right == null && remainingSum == 0) {
        result.add(new ArrayList<>(currentPath));
    } else {
        backtrack(node.left, remainingSum, currentPath, result);
        backtrack(node.right, remainingSum, currentPath, result);
    }
    
    currentPath.remove(currentPath.size() - 1); // Backtrack
}
```

### Complexity Analysis
- **Time Complexity:** O(N²) in worst case (if many paths are stored), as each node is visited once, and copying paths for the result may take O(N) per path.
- **Space Complexity:** O(N) for the recursion stack and path storage.

### Dry Run
Suppose the tree:

```
      5
     / \
    4   8
   /   / \
  11  13  4
 /  \    / \
7    2  5   1
```
Target sum = 22

- Start at root (5), path=[5], sum=5
- Recurse left (4), path=[5,4], sum=9
- Recurse left (11), path=[5,4,11], sum=20
- Recurse left (7), path=[5,4,11,7], sum=27 → Not leaf, backtrack
- Recurse right (2), path=[5,4,11,2], sum=22 → Leaf, add [5,4,11,2] to result
- Backtrack to [5,4,11]
- Backtrack to [5,4]
- Recurse right (null), backtrack
- Recurse right (8), path=[5,8], sum=13
- Recurse left (13), path=[5,8,13], sum=26 → Leaf, sum != target
- Recurse right (4), path=[5,8,4], sum=17
- Recurse left (5), path=[5,8,4,5], sum=22 → Leaf, add [5,8,4,5]
- Recurse right (1), path=[5,8,4,1], sum=18 → Not add

---

## Approach 2: Improved Solution (Using Path Sum and Backtracking)

*(This is essentially the same as Approach 1; however, if we had a different approach, we could mention memoization or pruning, but for this problem, backtracking is optimal.)*

---

## Approach 3: Most Optimal Solution (Backtracking with Early Pruning)

### Core Idea
Same as above, but with slight optimizations:
- Check for leaf and sum equality as early as possible.
- Avoid copying paths unless necessary.
- Use efficient data structures.

### Algorithm
Same as Approach 1, but with minor optimizations:
- Pass the current path as a linked list or reuse a single list with add/remove for efficiency.
- Check for leaf and sum match immediately upon reaching leaf.

### Java Code
```java
public List<List<Integer>> pathSum(TreeNode root, int targetSum) {
    List<List<Integer>> result = new ArrayList<>();
    List<Integer> currentPath = new ArrayList<>();
    dfs(root, targetSum, currentPath, result);
    return result;
}

private void dfs(TreeNode node, int remainingSum, List<Integer> currentPath, List<List<Integer>> result) {
    if (node == null) return;

    currentPath.add(node.val);
    if (node.left == null && node.right == null && node.val == remainingSum) {
        result.add(new ArrayList<>(currentPath));
    } else {
        dfs(node.left, remainingSum - node.val, currentPath, result);
        dfs(node.right, remainingSum - node.val, currentPath, result);
    }
    currentPath.remove(currentPath.size() - 1); // Backtracking
}
```

### Complexity Analysis
- **Time Complexity:** O(N) to visit each node once, copying paths only when valid paths are found.
- **Space Complexity:** O(N) due to recursion stack and path storage.

---

## Summary
| Approach | Idea | Time Complexity | Space Complexity | Notes |
|------------|-------|------------------|-------------------|--------|
| Brute Force | DFS with path tracking, check at leaves | O(N²) | O(N) | Path copying for each valid path |
| Optimized Backtracking | DFS with in-place path updates, early check at leaves | O(N) | O(N) | More efficient path handling |

---

## Final Tips for Interview
- Use recursion with backtracking to explore all paths.
- Maintain a current path list and remove nodes when backtracking.
- Check for leaf nodes and sum equality at the same time to avoid unnecessary traversal.
- Be mindful of copying lists; do it only when a valid path is found.
- Understand that the problem naturally fits a DFS + backtracking pattern.

---

**End of Revision Notes**
