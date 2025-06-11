# Insert into a Binary Search Tree (BST)

This guide covers various approaches to insert a node into a BST, progressing from brute-force to the most optimized solution. It includes core ideas, step-by-step algorithms, Java implementations, complexity analyses, and dry runs with diagrams for clear understanding.

---

## 1. Brute Force Approach

### **Core Idea:**
Traverse the entire tree to find the correct position where the new node should be inserted, then insert it as a leaf node.

### **Algorithm:**
1. **Start** from the root node.
2. **Compare** the value to be inserted (`val`) with the current node's value.
3. **Traverse** left if `val` < current node's value.
4. **Traverse** right if `val` > current node's value.
5. **Repeat** until reaching a null position where the new node can be inserted.
6. **Create** the new node and attach it as the left or right child of the last visited node.

### **Java Code:**

```java
public class TreeNode {
    int val;
    TreeNode left, right;

    TreeNode(int val) {
        this.val = val;
        left = right = null;
    }
}

public class BSTInsert {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val);
        }

        TreeNode current = root;
        TreeNode parent = null;

        while (current != null) {
            parent = current;
            if (val < current.val) {
                current = current.left;
            } else {
                current = current.right;
            }
        }

        // Insert the new node at the correct position
        if (val < parent.val) {
            parent.left = new TreeNode(val);
        } else {
            parent.right = new TreeNode(val);
        }
        return root;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  **O(h)**, where *h* is the height of the tree, since in the worst case, we traverse from root to leaf.  
  For a skewed tree, *h* can be *n*, so worst case **O(n)**.
- **Space Complexity:**  
  **O(1)**, iterative approach uses constant extra space.

### **Dry Run (Example):**

- **Input:** Insert `5` into the BST:

```
        4
       / \
      2   7
```

- **Step 1:** Start at root (`4`), `5 > 4`, move to right.
- **Step 2:** Current is `7`, `5 < 7`, move to left (null).
- **Step 3:** Insert new node `5` as left child of `7`.

---

## 2. Recursive Approach

### **Core Idea:**
Use recursion to navigate to the correct position and insert the new node, as recursion naturally handles tree traversal.

### **Algorithm:**
1. **Base case:** If the current node is null, create and return a new node.
2. **Compare** `val` with current node's value.
3. **Recurse** into left subtree if `val` < current node's value.
4. **Recurse** into right subtree if `val` > current node's value.
5. **Update** the left or right child pointers during recursion.

### **Java Code:**

```java
public class BSTInsert {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val);
        }
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
  **O(h)**, where *h* is the tree height, similar to iterative.
- **Space Complexity:**  
  **O(h)** due to recursive stack space.

### **Dry Run:**

- **Input:** Insert `3` into:

```
        4
       / \
      2   7
```

- **Step 1:** `3 < 4`, recurse into left (`2`).
- **Step 2:** `3 > 2`, recurse into right (null).
- **Step 3:** Insert `3` as right child of `2`.

---

## 3. Most Optimal Solution: **Recursive Approach (Clean & Elegant)**

### **Core Idea:**
Recursion with concise logic ensures minimal code and clear understanding; it naturally exploits BST properties.

### **Algorithm:**
Same as above, but emphasizing clarity and brevity.

### **Java Code:**

```java
public class BSTInsert {
    public TreeNode insertIntoBST(TreeNode root, int val) {
        if (root == null) {
            return new TreeNode(val);
        }
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
  **O(h)**, best for balanced trees, worst for skewed trees.
- **Space Complexity:**  
  **O(h)** due to recursion stack.

---

## Summary Table

| Approach                  | Time Complexity | Space Complexity | Pros                               | Cons                        |
|----------------------------|-------------------|------------------|-------------------------------------|------------------------------|
| Brute Force (Iterative)    | O(h)              | O(1)             | Simple, iterative, efficient in space | Slightly verbose             |
| Recursive                  | O(h)              | O(h)             | Elegant, concise                   | Stack space usage           |

---

## Final Tips for Interview:
- Always leverage BST properties to navigate the tree efficiently.
- Prefer recursive solution for clean code; iterative for constant space.
- Handle edge cases such as empty tree (null root).
- Remember, insertion modifies the tree; ensure to return the root in recursive solutions.

---

This comprehensive revision should prepare you well for understanding and implementing insertion in a BST for interviews!
