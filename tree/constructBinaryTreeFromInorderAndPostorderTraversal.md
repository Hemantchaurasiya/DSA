# Construct Binary Tree from Inorder and Postorder Traversal

This problem involves reconstructing a binary tree given its inorder and postorder traversal arrays.

---

## 1. Approach: Brute Force

### **Core Idea:**
The last element in the postorder traversal is the root of the tree. Using the inorder traversal, we can find the position of this root, which splits the inorder array into left and right subtrees. Recursively, we can build the tree by partitioning arrays accordingly.

### **Algorithm:**

1. **Identify the root:** The last element in postorder is the root.
2. **Find root in inorder:** Search for the root in the inorder array to determine the left and right subtree sizes.
3. **Partition arrays:**
   - Left subtree inorder: elements before root in inorder.
   - Right subtree inorder: elements after root in inorder.
   - Corresponding postorder segments:
     - Left subtree postorder: first `left_subtree_size` elements.
     - Right subtree postorder: remaining elements before the last element (root).
4. **Recursive calls:**
   - Recursively build left and right subtrees using the respective segments.
5. **Base case:**
   - If the segment is empty, return null.

---

### **Java Code:**

```java
public class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

public class Solution {
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        return build(inorder, 0, inorder.length - 1,
                     postorder, 0, postorder.length - 1);
    }
    
    private TreeNode build(int[] inorder, int inStart, int inEnd,
                           int[] postorder, int postStart, int postEnd) {
        if (inStart > inEnd || postStart > postEnd) {
            return null;
        }
        
        int rootVal = postorder[postEnd];
        TreeNode root = new TreeNode(rootVal);
        
        // Find root in inorder
        int inIndex = inStart;
        while (inIndex <= inEnd && inorder[inIndex] != rootVal) {
            inIndex++;
        }
        
        int leftTreeSize = inIndex - inStart;
        
        // Build left and right subtrees
        root.left = build(inorder, inStart, inIndex - 1,
                          postorder, postStart, postStart + leftTreeSize - 1);
        root.right = build(inorder, inIndex + 1, inEnd,
                           postorder, postStart + leftTreeSize, postEnd - 1);
        return root;
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n^2)** in the worst case, because for each node, we perform a linear search in the inorder array to find the root index.

- **Space Complexity:**  
  **O(n)** due to recursion stack and for storing the tree nodes.

---

### **Dry Run with Example:**

Suppose:
- `inorder = [9,3,15,20,7]`
- `postorder = [9,15,7,20,3]`

**Step-by-step:**

| Step | Postorder Last Element | Root | Inorder Segment | Find root in inorder | Left Subtree size | Recursive calls |  
|-------|-------------------------|--------|------------------|----------------------|-------------------|------------------|  
| 1     | 3                       | 3      | [9,3,15,20,7]    | index 1             | 1                 | Left: [9], Right: [15,20,7] |  
| 2     | 9                       | 9      | [9]              | index 0             | 0                 | Left: null, Right: null |  
| 3     | 20                      | 20     | [15,20,7]        | index 3             | 2                 | Left: [15], Right: [7] |  
| 4     | 15                      | 15     | [15]             | index 2             | 0                 | null, null |  
| 5     | 7                       | 7      | [7]              | index 4             | 0                 | null, null |  

This builds the entire tree.

---

## 2. Approach: Optimized Solution using Hash Map

### **Core Idea:**
Reduce the search time for root in inorder array from O(n) to O(1) by precomputing a value-to-index map of inorder traversal. This optimization improves overall time complexity.

### **Algorithm:**

1. Precompute a HashMap that maps node values to their indices in inorder array.
2. Follow the same recursive strategy as above.
3. Use the HashMap to find root index in inorder array instantly.
4. Partition arrays based on the root index and subtree sizes.
5. Recursively build left and right subtrees.

---

### **Java Code:**

```java
import java.util.HashMap;

public class Solution {
    private HashMap<Integer, Integer> inorderMap;
    
    public TreeNode buildTree(int[] inorder, int[] postorder) {
        inorderMap = new HashMap<>();
        for (int i = 0; i < inorder.length; i++) {
            inorderMap.put(inorder[i], i);
        }
        return build(inorder, 0, inorder.length - 1,
                     postorder, 0, postorder.length - 1);
    }
    
    private TreeNode build(int[] inorder, int inStart, int inEnd,
                           int[] postorder, int postStart, int postEnd) {
        if (inStart > inEnd || postStart > postEnd) {
            return null;
        }
        int rootVal = postorder[postEnd];
        TreeNode root = new TreeNode(rootVal);
        int inIndex = inorderMap.get(rootVal);
        int leftTreeSize = inIndex - inStart;
        
        root.left = build(inorder, inStart, inIndex - 1,
                          postorder, postStart, postStart + leftTreeSize - 1);
        root.right = build(inorder, inIndex + 1, inEnd,
                           postorder, postStart + leftTreeSize, postEnd - 1);
        return root;
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  **O(n)**, since each node is processed once, and lookups in the HashMap are O(1).

- **Space Complexity:**  
  **O(n)** for HashMap storage and recursion stack.

---

### **Dry Run with Example:**

Using the same example:

- Precompute `inorderMap = {9:0, 3:1, 15:2, 20:3, 7:4}`

Process recursively:

| Step | Postorder Last Element | Root | Inorder Index (via HashMap) | Left subtree size | Recursive calls |  
|-------|-------------------------|--------|------------------------------|-------------------|------------------|  
| 1     | 3                       | 3      | 1                            | 1                 | Left: [9], Right: [15,20,7] |  
| 2     | 9                       | 9      | 0                            | 0                 | null, null |  
| 3     | 20                      | 20     | 3                            | 2                 | Left: [15], Right: [7] |  
| 4     | 15                      | 15     | 2                            | 0                 | null, null |  
| 5     | 7                       | 7      | 4                            | 0                 | null, null |  

This efficient approach reconstructs the same tree with improved performance.

---

## Summary

| Aspect | Brute Force | Optimized (HashMap) |
|---------|--------------|---------------------|
| Search for root index | Linear search in inorder | O(1) using HashMap |
| Time Complexity | O(n²) | O(n) |
| Space Complexity | O(n) for recursion stack | O(n) for HashMap + recursion stack |

---

## Final Tip:
- Always use the HashMap for large inputs to optimize search.
- Carefully manage array indices to avoid errors during partitioning.
- Understand the traversal properties:
  - **Inorder:** Left - Root - Right
  - **Postorder:** Left - Right - Root

---

This concludes the detailed revision note for **Construct Binary Tree from Inorder and Postorder Traversal**.
