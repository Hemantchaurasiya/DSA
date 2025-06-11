# Sum of Left Leaves

This problem asks us to find the **sum of all left leaves** in a binary tree.

---

## 1. **Approach Name:** Brute Force (Recursive Traversal)

### **Core Idea:**
Traverse the entire binary tree, check if the current node's left child is a leaf, and if yes, add its value to the sum. Continue traversing both left and right subtrees.

---

### **Algorithm:**
1. Start from the root node.
2. For each node:
   - If the node has a left child, check if it is a leaf:
     - If it is a leaf, add its value to the sum.
   - Recursively call the function on the left subtree.
   - Recursively call the function on the right subtree.
3. Accumulate the sum during traversal.

---

### **Java Code:**
```java
public class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null) return 0;
        int sum = 0;
        if (root.left != null && root.left.left == null && root.left.right == null) {
            sum += root.left.val;
        }
        sum += sumOfLeftLeaves(root.left);
        sum += sumOfLeftLeaves(root.right);
        return sum;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** **O(N)**, where N is the number of nodes in the tree, since each node is visited once.
- **Space Complexity:** **O(H)**, where H is the height of the tree, due to the recursion stack.

---

### **Dry Run:**

**Sample Input Tree:**

```
        3
       / \
      9   20
         /  \
        15   7
```

**Step-by-step traversal:**

| Current Node | Action | Left Child Check | Sum Accumulated | Recursive Calls |
|----------------|---------|------------------|-----------------|-----------------|
| 3              | Check left: 9 (leaf?) No | No | 0 | recurse left (9), recurse right (20) |
| 9              | Leaf? Yes (left= null, right= null) | Yes, add 9 | 9 | return |
| 20             | Check left: 15 (leaf?) Yes, add 15 | Yes, add 15 | 9 + 15 = 24 | recurse left (15), recurse right (7) |
| 15             | Leaf? Yes | Yes, add 15 | 15 | return |
| 7              | Leaf? Yes | Yes, add 7 | 7 | return |

**Final Sum: 9 + 15 + 7 = 31**

---

## 2. **Approach Name:** Iterative DFS using Stack

### **Core Idea:**
Use an explicit stack to perform DFS traversal, checking for left leaves at each step.

---

### **Algorithm:**
1. Initialize a stack and push the root node.
2. Initialize `sum` to 0.
3. While the stack is not empty:
   - Pop the top node.
   - If the node has a left child:
     - Check if it is a leaf:
       - If yes, add its value to `sum`.
     - Push the left child onto the stack.
   - If the node has a right child, push it onto the stack.
4. Return the accumulated `sum`.

---

### **Java Code:**
```java
import java.util.Stack;

public class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        if (root == null) return 0;
        int sum = 0;
        Stack<TreeNode> stack = new Stack<>();
        stack.push(root);
        while (!stack.isEmpty()) {
            TreeNode node = stack.pop();
            if (node.left != null) {
                if (node.left.left == null && node.left.right == null) {
                    sum += node.left.val;
                } else {
                    stack.push(node.left);
                }
            }
            if (node.right != null) {
                stack.push(node.right);
            }
        }
        return sum;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** **O(N)**, same as traversal of all nodes.
- **Space Complexity:** **O(H)**, for the stack, where H is the height of the tree.

---

### **Dry Run:**

Using the same sample tree:

Stack operations:

| Stack Content (Top to Bottom) | Current Node | Action | Sum | Next Pushes |
|--------------------------------|--------------|---------|-----|--------------|
| [3]                            | 3            | Pop 3 | 0   | Push 9, Push 20 |
| [20, 9]                        | 9            | Pop 9 | 0   | No left leaf | (no push) |
| [20]                           | 20           | Pop 20| 0   | Push 15, Push 7 |
| [7, 15]                        | 15           | Pop 15| 15  | No pushes (leaf) |
| [7]                            | 7            | Pop 7 | 15  | No pushes (leaf) |

**Final sum: 15 (from 15) + 7 (from 7) + 9 (from 9) = 31**

---

## 3. **Approach Name:** Morris Traversal (Threaded Tree) - **Most Optimal**

### **Core Idea:**
Use Morris traversal to perform an inorder traversal without extra space, and check for left leaves during traversal. This approach avoids recursion and stack, achieving O(1) space complexity.

---

### **Algorithm:**
1. Initialize `sum` to 0.
2. Set `current` to root.
3. While `current` is not null:
   - If `current.left` is null:
     - Move to `current.right`.
   - Else:
     - Find the inorder predecessor of `current` in the left subtree.
     - If the predecessor's right is null:
       - Make `current` the right child of the predecessor (threading).
       - Move `current` to `current.left`.
     - Else:
       - If the predecessor's right points to `current`:
         - Remove the thread.
         - If the predecessor's left is a leaf, add its value to `sum`.
         - Move `current` to `current.right`.
4. Return `sum`.

**Note:** To specifically sum left leaves, during traversal, check if the node's left child is a leaf.

---

### **Java Code:**
```java
public class Solution {
    public int sumOfLeftLeaves(TreeNode root) {
        int sum = 0;
        TreeNode current = root;
        while (current != null) {
            if (current.left == null) {
                current = current.right;
            } else {
                TreeNode predecessor = current.left;
                // Find rightmost node in left subtree or thread
                while (predecessor.right != null && predecessor.right != current) {
                    predecessor = predecessor.right;
                }
                if (predecessor.right == null) {
                    // Establish thread
                    // Check if left child is a leaf
                    if (predecessor.left == null && predecessor.right == null) {
                        sum += predecessor.val;
                    }
                    predecessor.right = current;
                    current = current.left;
                } else {
                    // Thread exists, remove it
                    predecessor.right = null;
                    current = current.right;
                }
            }
        }
        return sum;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:** **O(N)**, each node is visited at most twice.
- **Space Complexity:** **O(1)**, no extra space apart from variables.

---

### **Dry Run:**

Using the same sample tree:

- Start at root (3).
- Process left subtree (9):
  - 9 is a leaf, check if it is a left child of its parent (3). Yes, add 9.
  - Move to right child (20).
- For node 20:
  - Process left child (15):
    - 15 is a leaf, check if it is a left child of 20. Yes, add 15.
  - Process right child (7):
    - 7 is a leaf, but it's a right child, so not sum.
- Final sum: 9 + 15 = 24.  
*Note:* In this implementation, we sum only left leaves during traversal.

---

# Summary

| Approach | Core Idea | Data Structure | Time Complexity | Space Complexity | Notes |
|------------|--------------|-------------------|-------------------|----------------------|--------|
| Brute Force (Recursive) | Traverse entire tree, sum left leaves | Recursion | O(N) | O(H) | Easy to implement, intuitive |
| Iterative DFS | Use stack to simulate recursion | Stack | O(N) | O(H) | Avoids recursion stack |
| Morris Traversal | Threaded traversal for O(1) space | Threaded Tree | O(N) | O(1) | Most space-efficient, complex to implement |

---

## Final Tips:
- Always verify if the left child is a leaf before adding its value.
- Use recursion or iterative traversal based on problem constraints.
- For space optimization, consider Morris traversal, but weigh complexity.

---

This comprehensive guide should help you understand different ways to solve the "Sum of Left Leaves" problem with clarity and efficiency. Happy coding!
