# Recover Binary Search Tree

This problem involves fixing a Binary Search Tree (BST) where **two nodes have been swapped by mistake**. The goal is to identify and correct these nodes to restore the BST's properties.

---

## 1. Approach: Brute Force

### **Core Idea:**
Find the two nodes that violate the BST property by using an inorder traversal, then swap their values.

### **Algorithm:**
1. Perform an inorder traversal of the BST and store the node values in an array.
2. Sort the array (or identify the two swapped nodes directly).
3. Perform another inorder traversal, replacing the node values with the corrected sorted values, or directly swap the two nodes identified.

### **Java Code:**
```java
class Solution {
    public void recoverTree(TreeNode root) {
        List<TreeNode> nodes = new ArrayList<>();
        inorder(root, nodes);
        int[] vals = nodes.stream().mapToInt(n -> n.val).toArray();

        // Identify the two swapped nodes
        int[] swapped = findSwappedNodes(vals);
        int first = swapped[0], second = swapped[1];

        // Swap the values in the nodes
        for (TreeNode node : nodes) {
            if (node.val == vals[first]) {
                node.val = vals[second];
            } else if (node.val == vals[second]) {
                node.val = vals[first];
            }
        }
    }

    private void inorder(TreeNode root, List<TreeNode> nodes) {
        if (root == null) return;
        inorder(root.left, nodes);
        nodes.add(root);
        inorder(root.right, nodes);
    }

    private int[] findSwappedNodes(int[] vals) {
        int n = vals.length;
        int first = -1, second = -1;
        for (int i = 0; i < n - 1; i++) {
            if (vals[i] > vals[i + 1]) {
                if (first == -1) {
                    first = i;
                } else {
                    second = i + 1;
                    break;
                }
            }
        }
        if (second == -1) second = first + 1;
        return new int[]{first, second};
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  O(N) for inorder traversal + O(N log N) for sorting if sorting is used.  
  **In this implementation**, since we do a single inorder traversal and then find the swapped nodes, overall is **O(N)**.
- **Space Complexity:**  
  O(N) for storing the inorder list.

---

## 2. Approach: Better Solution (Using Inorder Traversal to Detect Swapped Nodes)

### **Core Idea:**
Perform an inorder traversal, which should be sorted for a BST. The swapped nodes cause violations in this order:
- The first violation occurs when `prev.val > current.val`.
- The second violation (if any) indicates the second swapped node.

### **Algorithm:**
1. Use an inorder traversal with **three pointers**:
   - `prev` (previous visited node)
   - `first` (first node that violates the BST property)
   - `second` (second node that violates the property)
2. During traversal, when `prev.val > current.val`, update `first` and `second`.
3. After traversal, swap the values of `first` and `second`.

### **Java Code:**
```java
class Solution {
    private TreeNode first = null, second = null, prev = null;

    public void recoverTree(TreeNode root) {
        inorder(root);
        // Swap the values of the two nodes
        int temp = first.val;
        first.val = second.val;
        second.val = temp;
    }

    private void inorder(TreeNode root) {
        if (root == null) return;

        inorder(root.left);

        if (prev != null && prev.val > root.val) {
            if (first == null) {
                first = prev;
            }
            second = root;
        }
        prev = root;

        inorder(root.right);
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  O(N), as we traverse the tree once.
- **Space Complexity:**  
  O(H), where H is the height of the tree (due to recursion stack).

---

## 3. Approach: Most Optimal Solution (Using Morris Traversal)

### **Core Idea:**
Perform an inorder traversal with **O(1) space** using Morris Traversal, which temporarily modifies the tree links.

### **Algorithm:**
1. Initialize pointers `prev`, `first`, `second`.
2. Use Morris Traversal:
   - For each node:
     - If the node has no left child, visit it and move right.
     - Else, find the inorder predecessor:
       - If the predecessor's right is null, set it to current (threading), and move left.
       - Else, revert the thread, process current, and move right.
3. During traversal, detect violations similar to the previous approach.
4. Swap the values of `first` and `second` at the end.

### **Java Code:**
```java
class Solution {
    public void recoverTree(TreeNode root) {
        TreeNode first = null, second = null, prev = null;
        TreeNode curr = root;

        while (curr != null) {
            if (curr.left == null) {
                // Detect violation
                if (prev != null && prev.val > curr.val) {
                    if (first == null) first = prev;
                    second = curr;
                }
                prev = curr;
                curr = curr.right;
            } else {
                // Find the inorder predecessor
                TreeNode pred = curr.left;
                while (pred.right != null && pred.right != curr) {
                    pred = pred.right;
                }
                if (pred.right == null) {
                    pred.right = curr; // create thread
                    curr = curr.left;
                } else {
                    pred.right = null; // remove thread
                    // Detect violation
                    if (prev != null && prev.val > curr.val) {
                        if (first == null) first = prev;
                        second = curr;
                    }
                    prev = curr;
                    curr = curr.right;
                }
            }
        }
        // Swap the values
        int temp = first.val;
        first.val = second.val;
        second.val = temp;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**  
  O(N), as each node is visited at most twice.
- **Space Complexity:**  
  O(1), as Morris traversal modifies the tree temporarily without extra space.

---

# Dry Run Example

Suppose the BST is:
```
      3
     / \
    1   4
       /
      2
```
where nodes 2 and 3 are swapped (correct BST should be 1, 2, 3, 4).

---

### **Inorder Traversal (Correct BST):**
`1 -> 2 -> 3 -> 4`

### **In the faulty BST:**
`1 -> 2 -> 3 -> 4` with 2 and 3 swapped, so the sequence becomes:
`1 -> 3 -> 2 -> 4`.

---

### **Detection of swapped nodes:**

- **First violation:** when `prev = 3` and `current = 2` (since 3 > 2).  
  - `first` = node with value 3  
  - `second` = node with value 2

No other violations.

- **Swap values** of `first` and `second`:
  - Swap 3 and 2 to restore BST:
  
Result:
```
      2
     / \
    1   4
       /
      3
```

---

# Summary Table

| Approach | Core Idea | Key Steps | Time | Space | Pros & Cons |
|------------|--------------|--------------|---------|----------|--------------|
| Brute Force | Use inorder traversal + sorting | Store nodes, sort, assign sorted values | O(N log N) | O(N) | Simple but less efficient |
| Better Solution | Detect violations in inorder traversal | Identify two nodes during traversal | O(N) | O(H) | Optimal, easy to implement |
| Morris Traversal | O(1) space, threaded traversal | Use Morris traversal for detection | O(N) | O(1) | Most space-efficient, complex to implement |

---

This comprehensive guide covers all major approaches to **Recover Binary Search Tree**, enabling quick revision and deep understanding for interviews.
