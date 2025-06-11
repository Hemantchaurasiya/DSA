# Construct Binary Tree from Preorder and Inorder Traversal

This problem involves reconstructing a binary tree given its preorder and inorder traversal sequences. It's a classic problem that demonstrates understanding of tree traversal properties and efficient recursion.

---

## 1. Approach Name: Brute Force (Naive Recursive Search)

### Core Idea:
For each node, find its position in the inorder array to determine the left and right subtrees, then recursively build subtrees.

---

### Algorithm:
1. Start with the first element in the preorder array; this is the root.
2. Find the index of this root in the inorder array.
3. Elements to the left of this index in inorder form the left subtree.
4. Elements to the right form the right subtree.
5. Recursively repeat the process for left and right subtrees:
   - For left subtree: next element in preorder, and the left segment in inorder.
   - For right subtree: subsequent element, and the right segment in inorder.
   
**Note:** For each recursive call, searching for root in inorder array takes O(n).

---

### Java Code:
```java
public class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

public class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || inorder == null || preorder.length != inorder.length)
            return null;
        return buildTreeHelper(preorder, 0, preorder.length - 1,
                               inorder, 0, inorder.length - 1);
    }

    private TreeNode buildTreeHelper(int[] preorder, int preStart, int preEnd,
                                     int[] inorder, int inStart, int inEnd) {
        if (preStart > preEnd || inStart > inEnd)
            return null;

        int rootVal = preorder[preStart];
        TreeNode root = new TreeNode(rootVal);

        // Find root in inorder array
        int inRootIndex = inStart;
        while (inRootIndex <= inEnd && inorder[inRootIndex] != rootVal)
            inRootIndex++;

        int leftTreeSize = inRootIndex - inStart;

        root.left = buildTreeHelper(preorder, preStart + 1, preStart + leftTreeSize,
                                    inorder, inStart, inRootIndex - 1);
        root.right = buildTreeHelper(preorder, preStart + leftTreeSize + 1, preEnd,
                                     inorder, inRootIndex + 1, inEnd);
        return root;
    }
}
```

---

### Complexity Analysis:
- **Time Complexity:**  
  - **O(n^2)** in the worst case, because for each node, searching for its position in inorder array takes O(n).
- **Space Complexity:**  
  - **O(n)** for recursion stack and the tree structure.

---

### Dry Run:
**Input:**
- `preorder = [3,9,20,15,7]`
- `inorder = [9,3,15,20,7]`

| Step | Preorder Index | Preorder Value | Inorder Segment | Root | Inorder Index of Root | Left Subtree Size | Recursive Calls |
|-------|----------------|----------------|----------------|--------|------------------------|-------------------|-----------------|
| 1     | 0              | 3              | [9,3,15,20,7]  | 3      | 1                      | 1                 | Left: [9], Right: [15,20,7] |
| 2     | 1              | 9              | [9]            | 9      | 0                      | 0                 | Leaf node, no children |
| 3     | 2              | 20             | [15,20,7]      | 20     | 3                      | 2                 | Left: [15], Right: [7] |
| 4     | 3              | 15             | [15]           | 15     | 2                      | 0                 | Leaf node |
| 5     | 4              | 7              | [7]            | 7      | 4                      | 0                 | Leaf node |

---

## 2. Approach Name: Improved Solution with HashMap (Using Hash Map for Index Lookup)

### Core Idea:
Reduce search time for root in inorder array by precomputing a HashMap of value -> index.  
This improves search from O(n) to O(1).

---

### Algorithm:
1. Build a HashMap that maps each value in inorder to its index.
2. Use a recursive helper function with parameters for current segment indices.
3. For each recursive call:
   - Pick the current root from preorder.
   - Find its index in inorder using the HashMap.
   - Calculate the size of the left subtree.
   - Recursively build left and right subtrees.

---

### Java Code:
```java
import java.util.HashMap;

public class Solution {
    private HashMap<Integer, Integer> inMap;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || inorder == null || preorder.length != inorder.length)
            return null;
        inMap = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            inMap.put(inorder[i], i);
        }
        return buildTreeHelper(preorder, 0, preorder.length - 1,
                               0, inorder.length - 1);
    }

    private TreeNode buildTreeHelper(int[] preorder, int preStart, int preEnd,
                                     int inStart, int inEnd) {
        if (preStart > preEnd || inStart > inEnd)
            return null;

        int rootVal = preorder[preStart];
        TreeNode root = new TreeNode(rootVal);

        int inRootIndex = inMap.get(rootVal);
        int leftTreeSize = inRootIndex - inStart;

        root.left = buildTreeHelper(preorder, preStart + 1, preStart + leftTreeSize,
                                    inStart, inRootIndex - 1);
        root.right = buildTreeHelper(preorder, preStart + leftTreeSize + 1, preEnd,
                                     inRootIndex + 1, inEnd);
        return root;
    }
}
```

---

### Complexity Analysis:
- **Time Complexity:**  
  - **O(n)**, since each node is processed once and lookups in HashMap are O(1).
- **Space Complexity:**  
  - **O(n)** for HashMap and recursion stack.

---

### Dry Run:
Same as previous, but with HashMap:
- In the initial step, build `inMap`: `{9:0, 3:1, 15:2, 20:3, 7:4}`.
- Use this map to instantly find the index of root in `O(1)` time during recursive calls.

---

## 3. Approach Name: Iterative Approach (Using Stack)

### Core Idea:
Simulate the recursive process iteratively by using a stack to keep track of nodes and their bounds.

---

### Algorithm:
1. Initialize an empty stack.
2. Use two pointers:
   - `preIndex` for preorder array.
   - `inIndex` for inorder array.
3. Create root node from `preorder[preIndex]` and push onto stack.
4. Iterate:
   - If the top of the stack's node value is not equal to `inorder[inIndex]`, create a new node from `preorder[++preIndex]` and assign it as the left child.
   - Else, pop from the stack until the top's value matches `inorder[inIndex]`, then move `inIndex` forward and assign the next node as the right child.

---

### Java Code:
```java
public class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || inorder == null || preorder.length == 0)
            return null;

        TreeNode root = new TreeNode(preorder[0]);
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        int inIndex = 0, preIndex = 1;

        while (preIndex < preorder.length) {
            TreeNode node = stack.peek();
            if (node.val != inorder[inIndex]) {
                // Left child
                node.left = new TreeNode(preorder[preIndex++]);
                stack.push(node.left);
            } else {
                // Find the parent node to attach the right child
                while (!stack.isEmpty() && stack.peek().val == inorder[inIndex]) {
                    node = stack.pop();
                    inIndex++;
                }
                // Right child
                node.right = new TreeNode(preorder[preIndex++]);
                stack.push(node.right);
            }
        }
        return root;
    }
}
```

---

### Complexity Analysis:
- **Time Complexity:**  
  - **O(n)**, each node is processed once.
- **Space Complexity:**  
  - **O(n)** for the stack and tree nodes.

---

### Final Notes:
- The most optimal solution uses a HashMap for value-to-index lookup, reducing time complexity to O(n).
- Recursive solutions are clean and intuitive, iterative solutions can avoid recursion stack overhead.
- Always consider the input size and constraints to choose the best approach.

---

**End of Revision Note**
