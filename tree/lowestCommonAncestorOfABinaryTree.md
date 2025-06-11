# Lowest Common Ancestor (LCA) of a Binary Tree

This guide covers multiple approaches to solve the **Lowest Common Ancestor** problem in a binary tree, progressing from brute-force to optimal solutions. It is structured to help with interview prep and quick revision.

---

## Problem Statement
Given a binary tree and two nodes `p` and `q`, find their **Lowest Common Ancestor (LCA)**, i.e., the lowest node in the tree that has both `p` and `q` as descendants (where a node can be a descendant of itself).

---

## Approach 1: Brute Force (Recursive Search for Paths)

### **Core Idea**
Find the root-to-node paths for both `p` and `q`. The last common node in these paths is the LCA.

### **Algorithm**
1. **Find Path for `p`:** Recursively traverse the tree to find the path from root to `p`.
2. **Find Path for `q`:** Recursively traverse the tree to find the path from root to `q`.
3. **Compare Paths:** Traverse both paths from the start, compare nodes until they differ. The last matched node is the LCA.

---

### **Java Code**

```java
import java.util.*;

public class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        List<TreeNode> pathP = new ArrayList<>();
        List<TreeNode> pathQ = new ArrayList<>();
        
        // Find paths from root to p and q
        findPath(root, p, pathP);
        findPath(root, q, pathQ);
        
        int i = 0, j = 0;
        TreeNode lca = null;
        // Compare paths
        while (i < pathP.size() && j < pathQ.size()) {
            if (pathP.get(i) == pathQ.get(j)) {
                lca = pathP.get(i);
            } else {
                break;
            }
            i++;
            j++;
        }
        return lca;
    }
    
    private boolean findPath(TreeNode root, TreeNode target, List<TreeNode> path) {
        if (root == null) return false;
        path.add(root);
        if (root == target) return true;
        if (findPath(root.left, target, path) || findPath(root.right, target, path))
            return true;
        path.remove(path.size() - 1);
        return false;
    }
}
```

---

### **Complexity Analysis**
- **Time Complexity:**  
  **O(N)** for path finding (each node visited once for each path).  
  Total: **O(N)** (since paths are found separately).

- **Space Complexity:**  
  **O(N)** for storing paths in the worst case (height of tree).

---

### **Dry Run Example**
```
Tree:        3
             /   \
            5     1
           / \   / \
          6   2 0   8
             / \
            7   4

p = 7, q = 4
Paths:
p: 3 -> 5 -> 2 -> 7
q: 3 -> 5 -> 2 -> 4

Compare:
3 == 3 -> lca = 3
5 == 5 -> lca = 5
2 == 2 -> lca = 2
7 != 4 -> stop
LCA = 2
```

---

## Approach 2: Recursive Approach (Single Traversal, Bottom-Up)

### **Core Idea**
Use a bottom-up recursion:
- If current node is `p` or `q`, return it.
- Recursively check left and right subtrees.
- If both subtrees return non-null nodes, current node is the LCA.
- Else, propagate non-null child upwards.

### **Algorithm**
1. **Recursively traverse** from root.
2. **Check base cases:** if node is null, or matches `p` or `q`.
3. **Recurse** on left and right children.
4. **Determine LCA:**
   - If both left and right are non-null, current node is LCA.
   - Else, return non-null child if exists.

---

### **Java Code**

```java
public class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q)
            return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left != null && right != null)
            return root;
        return (left != null) ? left : right;
    }
}
```

---

### **Complexity Analysis**
- **Time Complexity:**  
  **O(N)**, each node visited once.

- **Space Complexity:**  
  **O(H)**, where *H* is the height of the tree (recursion stack).

---

### **Dry Run Example**
```
Tree:        3
             /   \
            5     1
           / \   / \
          6   2 0   8
             / \
            7   4

p = 7, q = 4
Start at root (3):
- Recurse left (5):
  - Recurse left (6): no match, returns null
  - Recurse right (2):
    - Recurse left (7): matches p, returns 7
    - Recurse right (4): matches q, returns 4
    - Both children non-null => node 2 is LCA
  - Node 5: left null, right 2 => return 2
- Recurse right (1): no match, returns null
- Both left (5) and right (1): non-null? No, only left has answer.
- Final answer: 2
```

---

## Approach 3: Parent Pointer + Ancestor Set (Using Parent Pointers)

### **Core Idea**
- Traverse the tree to record parent pointers.
- Trace ancestors of `p` in a set.
- Traverse ancestors of `q` until a common ancestor is found.

### **Algorithm**
1. **Preprocessing:**
   - Traverse the tree, store parent pointers in a hash map.
2. **Find Ancestors of `p`:**
   - Add all ancestors to a set.
3. **Find LCA:**
   - Move up from `q` using parent pointers.
   - The first ancestor of `q` found in `p`'s ancestor set is the LCA.

---

### **Java Code**

```java
import java.util.*;

public class Solution {
    private Map<TreeNode, TreeNode> parentMap = new HashMap<>();
    
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // Build parent map
        buildParentMap(root, null);
        Set<TreeNode> ancestors = new HashSet<>();
        // Record ancestors of p
        while (p != null) {
            ancestors.add(p);
            p = parentMap.get(p);
        }
        // Traverse ancestors of q
        while (q != null) {
            if (ancestors.contains(q))
                return q;
            q = parentMap.get(q);
        }
        return null;
    }
    
    private void buildParentMap(TreeNode node, TreeNode parent) {
        if (node == null) return;
        parentMap.put(node, parent);
        buildParentMap(node.left, node);
        buildParentMap(node.right, node);
    }
}
```

---

### **Complexity Analysis**
- **Time Complexity:**  
  **O(N)** for building parent map + ancestor traversal.

- **Space Complexity:**  
  **O(N)** for storing parent pointers and ancestor set.

---

## Summary of Approaches

| Approach | Time Complexity | Space Complexity | Notes |
|------------|-------------------|-------------------|--------|
| Brute Force (Paths) | O(N) | O(N) | Finds paths, compares last common node |
| Recursive (Bottom-Up) | O(N) | O(H) | Single traversal, efficient |
| Parent Pointers | O(N) | O(N) | Uses parent references, suitable if parent info available |

---

## Final Tips for Interview
- **Most common approach:** Recursive bottom-up (Approach 2) is concise and efficient.
- **Edge cases:** When `p` or `q` is the ancestor of the other, ensure code handles that.
- **Tree structure:** Confirm the tree is a valid binary tree, no duplicates.

---

This concludes your comprehensive revision note on **Lowest Common Ancestor of a Binary Tree**. Practice implementing these approaches to solidify understanding!
