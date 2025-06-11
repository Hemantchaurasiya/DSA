# Binary Tree Paths

This guide provides a comprehensive overview of solving the **Binary Tree Paths** problem, focusing on multiple approaches from brute-force to optimized solutions. It includes core ideas, detailed algorithms, Java implementations, complexity analyses, and dry runs for better understanding.

---

## Problem Statement
Given a binary tree, return all root-to-leaf paths as a list of strings.

**Example:**

```
     1
    / \
   2   3
    \
     5
```

Output: `["1->2->5", "1->3"]`

---

## Approach 1: Brute Force (Recursive DFS with String Concatenation)

### Core Idea
Explore all paths from root to leaf using DFS. When a leaf node is reached, record the current path.

### Algorithm
1. Start from the root node.
2. Maintain a list or string builder to keep track of the current path.
3. Recursively traverse left and right children.
4. When reaching a leaf node, add the current path to the result list.
5. Backtrack to explore other paths.

### Java Code
```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> paths = new ArrayList<>();
    if (root == null) return paths;
    dfs(root, "", paths);
    return paths;
}

private void dfs(TreeNode node, String path, List<String> paths) {
    if (node == null) return;
    String currentPath = path.isEmpty() ? "" + node.val : path + "->" + node.val;
    if (node.left == null && node.right == null) {
        paths.add(currentPath);
        return;
    }
    dfs(node.left, currentPath, paths);
    dfs(node.right, currentPath, paths);
}
```

### Complexity Analysis
- **Time Complexity:** *O(N * H)*, where N is the number of nodes and H is the maximum height of the tree, due to string concatenation at each node.
- **Space Complexity:** *O(N)* for the recursion stack and path storage.

### Dry Run
- Input: Tree as above.
- Path traversal:
  - Start at node 1:
    - Path: "1"
  - Traverse left:
    - Path: "1->2"
    - Traverse right child of 2:
      - Path: "1->2->5"
      - Leaf reached, record "1->2->5"
  - Traverse right of 1:
    - Path: "1->3"
    - Leaf reached, record "1->3"
- Result: `["1->2->5", "1->3"]`

---

## Approach 2: Better Solution - Using StringBuilder for Efficiency

### Core Idea
Reduce overhead caused by string concatenation by using `StringBuilder`. Pass a mutable object along recursive calls to build paths efficiently.

### Algorithm
1. Use a `StringBuilder` to build the path.
2. Append current node value.
3. If leaf:
   - Save a copy of the current path.
4. Else:
   - Append "->" and recurse on children.
5. After recursion, backtrack by removing last appended characters.

### Java Code
```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> paths = new ArrayList<>();
    if (root == null) return paths;
    StringBuilder pathBuilder = new StringBuilder();
    dfs(root, pathBuilder, paths);
    return paths;
}

private void dfs(TreeNode node, StringBuilder path, List<String> paths) {
    int len = path.length();
    path.append(node.val);
    if (node.left == null && node.right == null) {
        paths.add(path.toString());
    } else {
        path.append("->");
        if (node.left != null) dfs(node.left, path, paths);
        if (node.right != null) dfs(node.right, path, paths);
    }
    path.setLength(len); // backtrack
}
```

### Complexity Analysis
- **Time Complexity:** *O(N)*, each node is visited once.
- **Space Complexity:** *O(H)* for the recursion stack plus *O(N)* for storing paths, where H is tree height.

### Dry Run
- Similar to Approach 1 but with more efficient string handling.
- Path building and backtracking are explicitly managed via `setLength()`.

---

## Approach 3: Most Optimal - Using DFS with Path List (Backtracking)

### Core Idea
Avoid string building overhead altogether by passing a list of node values representing the current path. When a leaf is reached, join the list with "->" to form a string.

### Algorithm
1. Maintain a list of integers representing the current path.
2. Traverse the tree recursively:
   - Add current node to the path list.
   - If leaf:
     - Convert path list to string with "->" separator and add to result.
   - Else:
     - Recurse on children.
   - Remove the current node from the path list (backtracking).

### Java Code
```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> paths = new ArrayList<>();
    if (root == null) return paths;
    List<Integer> currentPath = new ArrayList<>();
    dfs(root, currentPath, paths);
    return paths;
}

private void dfs(TreeNode node, List<Integer> currentPath, List<String> paths) {
    currentPath.add(node.val);
    if (node.left == null && node.right == null) {
        // Convert path list to string
        StringBuilder pathStr = new StringBuilder();
        for (int i = 0; i < currentPath.size(); i++) {
            if (i > 0) pathStr.append("->");
            pathStr.append(currentPath.get(i));
        }
        paths.add(pathStr.toString());
    } else {
        if (node.left != null) dfs(node.left, currentPath, paths);
        if (node.right != null) dfs(node.right, currentPath, paths);
    }
    currentPath.remove(currentPath.size() - 1); // backtrack
}
```

### Complexity Analysis
- **Time Complexity:** *O(N)*, each node visited once, converting list to string for each leaf.
- **Space Complexity:** *O(H)* for recursion stack plus *O(N)* for path list.

### Dry Run
- Path list is maintained dynamically:
  - At each node, add node value.
  - When leaf, join list into string and add to results.
  - Remove the last element when backtracking.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|-------------------|---------------------|--------|
| Brute Force | DFS with string concatenation | O(N * H) | O(N) | Simple but inefficient due to string concatenation |
| StringBuilder | Use StringBuilder for path | O(N) | O(H) | More efficient string handling |
| Path List + Join | Maintain list of node values, join at leaf | O(N) | O(H) + O(N) | Most optimal in terms of string operations |

---

## Final Tips for Interview
- Use backtracking to explore all paths.
- Prefer passing a mutable data structure (like list) over string concatenation.
- Convert the path list to string only at leaf nodes to optimize performance.
- Remember to backtrack correctly to avoid incorrect paths.

---

This concludes the comprehensive revision note for **Binary Tree Paths**. Use this as a quick reference and practice implementing these approaches for solid understanding.
