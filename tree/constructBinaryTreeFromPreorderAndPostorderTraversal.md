# Construct Binary Tree from Preorder and Postorder Traversal

This problem involves constructing a binary tree given its preorder and postorder traversal arrays. It is a classic problem demonstrating tree construction techniques and the importance of traversal properties.

---

## Problem Summary
**Given**:
- `preorder[]`: array representing preorder traversal of the tree
- `postorder[]`: array representing postorder traversal of the tree

**Goal**:
Construct the original binary tree and return its root node.

---

## Approach 1: Brute Force (Recursive Search)

### **Core Idea**
- Use the fact that the first element in preorder is the root.
- The last element in postorder is also the root.
- Find the position of the left subtree's root in postorder to split the arrays.
- Recursively construct left and right subtrees.

### **Algorithm**
1. The first element of preorder is the root.
2. Find the next element in preorder (say `pre[1]`) in the postorder array, which indicates the end of the left subtree.
3. The size of the left subtree can be computed from its position in postorder.
4. Recursively construct:
   - Left subtree using slices of preorder and postorder arrays.
   - Right subtree similarly.
5. Base case: if the array slices are empty, return `null`.

### **Java Code**
```java
public class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

public class Solution {
    public TreeNode constructFromPrePost(int[] pre, int[] post) {
        return build(pre, 0, pre.length - 1, post, 0, post.length - 1);
    }

    private TreeNode build(int[] pre, int preStart, int preEnd,
                           int[] post, int postStart, int postEnd) {
        if (preStart > preEnd || postStart > postEnd)
            return null;
        TreeNode root = new TreeNode(pre[preStart]);
        if (preStart == preEnd)
            return root;

        // Next element in preorder is the left child
        int leftRootVal = pre[preStart + 1];

        // Find leftRootVal in postorder to determine size of left subtree
        int leftRootIndex = postIndex(post, leftRootVal, postStart, postEnd);
        int leftSize = leftRootIndex - postStart + 1;

        // Recursively build left and right subtrees
        root.left = build(pre, preStart + 1, preStart + leftSize,
                          post, postStart, leftRootIndex);
        root.right = build(pre, preStart + leftSize + 1, preEnd,
                           post, leftRootIndex + 1, postEnd - 1);
        return root;
    }

    private int postIndex(int[] post, int val, int start, int end) {
        for (int i = start; i <= end; i++) {
            if (post[i] == val)
                return i;
        }
        return -1; // Should never happen if input is valid
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n^2)** in the worst case due to searching for `leftRootVal` in the postorder array for each recursive call.
  
- **Space Complexity:**  
  **O(n)** for recursion stack in the worst case (skewed tree).

### **Dry Run Example**
Suppose:
```
preorder = [1, 2, 4, 5, 3, 6, 7]
postorder = [4, 5, 2, 6, 7, 3, 1]
```

Step-by-step:
- Root = 1 (`pre[0]`)
- Next in preorder: 2
- Find 2 in postorder at index 2 → left subtree size = 3
- Build left subtree with:
  - `pre[1..3]` = [2, 4, 5]
  - `post[0..2]` = [4, 5, 2]
- Build right subtree with:
  - `pre[4..6]` = [3, 6, 7]
  - `post[3..6]` = [6, 7, 3]
- Repeat recursively...

---

## Approach 2: Optimized Solution Using HashMap

### **Core Idea**
- To avoid repeated searching, store element indices of postorder traversal in a HashMap for O(1) access.
- This reduces the search time from O(n) to O(1), improving overall complexity.

### **Algorithm**
1. Precompute a `Map<Integer, Integer>` for element to index in postorder.
2. Follow the same recursive logic as Approach 1, but with constant-time lookups.
3. Use the hashmap to quickly find the position of the left subtree's root in postorder.

### **Java Code**
```java
import java.util.HashMap;

public class Solution {
    private HashMap<Integer, Integer> postIndexMap;

    public TreeNode constructFromPrePost(int[] pre, int[] post) {
        postIndexMap = new HashMap<>();
        for (int i = 0; i < post.length; i++) {
            postIndexMap.put(post[i], i);
        }
        return build(pre, 0, pre.length - 1, post, 0, post.length - 1);
    }

    private TreeNode build(int[] pre, int preStart, int preEnd,
                           int[] post, int postStart, int postEnd) {
        if (preStart > preEnd || postStart > postEnd)
            return null;
        TreeNode root = new TreeNode(pre[preStart]);
        if (preStart == preEnd)
            return root;

        int leftRootVal = pre[preStart + 1];
        int leftRootIndex = postIndexMap.get(leftRootVal);
        int leftSize = leftRootIndex - postStart + 1;

        root.left = build(pre, preStart + 1, preStart + leftSize,
                          post, postStart, leftRootIndex);
        root.right = build(pre, preStart + leftSize + 1, preEnd,
                           post, leftRootIndex + 1, postEnd - 1);
        return root;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  **O(n)** because each element is processed once, and hashmap lookups are O(1).

- **Space Complexity:**  
  **O(n)** for the hashmap and recursion stack.

### **Dry Run**
Same as previous, but with:
- Preprocessing step: HashMap built in O(n).
- During recursion, element index lookup in O(1).

---

## **Summary of Approaches**

| Approach | Idea | Time Complexity | Space Complexity | Notes |
|------------|-------|------------------|-------------------|--------|
| Brute Force | Search for left root in postorder each time | O(n^2) | O(n) | Simplest, but inefficient for large inputs |
| HashMap Optimization | Use HashMap for quick index lookups | O(n) | O(n) | Most efficient for large inputs |

---

## **Key Takeaways**
- The core of the problem relies on the properties of preorder and postorder traversals.
- Preorder: `[Root, Left, Right]`
- Postorder: `[Left, Right, Root]`
- The challenge is identifying subtree boundaries efficiently.
- HashMap optimization drastically reduces search time, leading to a linear solution.

---

Feel free to review and practice these approaches to strengthen your understanding of tree construction problems!
