# Serialize and Deserialize BST

This problem involves converting a Binary Search Tree (BST) into a string representation (**serialization**) and reconstructing the BST from the string (**deserialization**). It's a common problem to assess understanding of tree traversal, recursion, and data structures.

---

## 1. Brute Force Approach

### **Core Idea:**
Serialize the entire BST by performing a **preorder traversal** (or other traversal), recording node values, and then deserialize by reconstructing the BST using the known traversal order.

### **Algorithm:**

- **Serialization:**
  - Perform a **preorder traversal** (root-left-right).
  - Store node values separated by a delimiter (e.g., comma).
  - For null nodes, you can choose to include placeholders, but in this case, since it's a BST, nulls are not strictly necessary.

- **Deserialization:**
  - Split the serialized string into a list of node values.
  - Reconstruct the BST by inserting nodes in the order they appear.
  - For each value, insert it into the BST following BST insertion rules.

### **Java Code:**

```java
public class Codec {
    
    // Serialize
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        preorder(root, sb);
        return sb.toString();
    }
    
    private void preorder(TreeNode root, StringBuilder sb) {
        if (root == null) {
            return;
        }
        sb.append(root.val).append(",");
        preorder(root.left, sb);
        preorder(root.right, sb);
    }
    
    // Deserialize
    public TreeNode deserialize(String data) {
        if (data.isEmpty()) return null;
        String[] values = data.split(",");
        TreeNode root = null;
        for (String val : values) {
            root = insertIntoBST(root, Integer.parseInt(val));
        }
        return root;
    }
    
    private TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) return new TreeNode(val);
        if (val < root.val) {
            root.left = insertIntoBST(root.left, val);
        } else {
            root.right = insertIntoBST(root.right, val);
        }
        return root;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:**
  - Serialization: O(N), where N is the number of nodes, since each node is visited once.
  - Deserialization: O(N * H), where H is the height of the tree, as inserting each node can take O(H). For a balanced BST, O(log N), so overall O(N log N). Unbalanced trees can degrade to O(N²).

- **Space Complexity:**
  - Serialization string: O(N).
  - Recursion stack: O(H).

### **Dry Run:**

Suppose the BST is:

```
      4
     / \
    2   6
   / \ / \
  1  3 5  7
```

- **Serialization (preorder):** `"4,2,1,3,6,5,7,"`

- **Deserialization:**
  - Insert 4 → root.
  - Insert 2 → goes to left of 4.
  - Insert 1 → goes to left of 2.
  - Insert 3 → goes to right of 2.
  - Insert 6 → goes to right of 4.
  - Insert 5 → goes to left of 6.
  - Insert 7 → goes to right of 6.

---

## 2. Better Solution (Using BST properties)

### **Core Idea:**
Leverage the **BST property** during deserialization to construct the tree in **O(N)** time, avoiding repeated insertions.

### **Algorithm:**

- **Serialization:**
  - Similar to above; use preorder traversal.

- **Deserialization:**
  - Use **bounds** to reconstruct the BST:
    - Maintain a global index to traverse the serialized array.
    - For the current value:
      - If it falls within the bounds (min, max), create a node.
      - Recursively construct left and right subtrees with updated bounds.
    - This approach ensures O(N) time because each element is processed once.

### **Java Code:**

```java
public class Codec {
    private int index = 0;
    private String[] values;
    
    // Serialize
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        preorder(root, sb);
        return sb.toString();
    }
    
    private void preorder(TreeNode root, StringBuilder sb) {
        if (root == null) return;
        sb.append(root.val).append(",");
        preorder(root.left, sb);
        preorder(root.right, sb);
    }
    
    // Deserialize
    public TreeNode deserialize(String data) {
        if (data.isEmpty()) return null;
        values = data.split(",");
        index = 0;
        return buildBST(Integer.MIN_VALUE, Integer.MAX_VALUE);
    }
    
    private TreeNode buildBST(int min, int max) {
        if (index >= values.length) return null;
        int val = Integer.parseInt(values[index]);
        if (val < min || val > max) return null;
        index++;
        TreeNode root = new TreeNode(val);
        root.left = buildBST(min, val);
        root.right = buildBST(val, max);
        return root;
    }
}
```

### **Complexity Analysis:**

- **Time Complexity:** O(N), as each node is processed exactly once.
- **Space Complexity:** O(N) for storing the serialized data and recursion stack in the worst case.

### **Dry Run:**

Using the same input, `"4,2,1,3,6,5,7,"`:

- Start with bounds (-∞, +∞), index=0.
- Value = 4 → within bounds → create node.
  - Build left subtree with bounds (-∞, 4).
    - Next value = 2 → within bounds → create node.
      - Build left with (-∞, 2): next value=1 → within bounds → create node.
        - Left of 1: no value within bounds → null.
        - Right of 1: no value within bounds → null.
      - Build right of 2 with (2, 4): next value=3 → within bounds → create node.
        - Left of 3: null.
        - Right of 3: null.
  - Build right subtree with (4, +∞).
    - Next value=6 → within bounds → create node.
      - Build left with (4, 6): next value=5 → within bounds → create node.
        - Left of 5: null.
        - Right of 5: null.
      - Build right of 6 with (6, +∞): next value=7 → within bounds → create node.
        - Left of 7: null.
        - Right of 7: null.

---

## 3. Optimal Approach Summary

| Aspect | Details |
|---------|---------|
| **Approach** | Use preorder traversal for serialization; recursive bounds for deserialization. |
| **Key Idea** | Exploit BST properties to reconstruct with O(N) time. |
| **Advantages** | Efficient, no repeated insertions, preserves BST structure. |

---

## **Summary**

| Approach | Time Complexity | Space Complexity | Notes |
|------------|-------------------|--------------------|--------|
| Brute Force (Insert nodes repeatedly) | O(N log N) (avg) | O(N) | Simple but less efficient for large trees |
| **BST property-based (bounds method)** | **O(N)** | O(N) | Most optimal, leverages BST properties |

---

## Final Tips:
- Always consider whether the tree is a BST; it allows optimized deserialization.
- Preorder traversal is most suitable because it gives the root-first order.
- Use a global or class variable for index during recursive deserialization.
- Handle edge cases: empty trees, skewed trees.

---

**Good luck with your interview prep!**
