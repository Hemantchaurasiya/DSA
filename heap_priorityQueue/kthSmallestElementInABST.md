# Kth Smallest Element in a BST

This note provides a comprehensive guide to solving the problem **"Kth Smallest Element in a BST"**, covering multiple approaches from brute-force to optimal solutions, suitable for interview preparation and quick revision.

---

## Problem Statement
Given a **Binary Search Tree (BST)**, find the **k-th smallest element** in the BST.

---

## Approach 1: Brute Force (In-order Traversal + Sorting)

### Core Idea:
Perform an in-order traversal of the BST (which yields elements in sorted order), store the elements in a list, and then select the k-th smallest element from that list.

### Algorithm:
1. Traverse the BST in **in-order** (left -> node -> right).
2. Store each visited node's value in a list.
3. After traversal, the list will be sorted.
4. Return the element at index `k-1` in the list.

### Java Code:
```java
import java.util.ArrayList;
import java.util.List;

public class KthSmallest {
    public int kthSmallest(TreeNode root, int k) {
        List<Integer> values = new ArrayList<>();
        inOrder(root, values);
        return values.get(k - 1);
    }

    private void inOrder(TreeNode root, List<Integer> values) {
        if (root == null) return;
        inOrder(root.left, values);
        values.add(root.val);
        inOrder(root.right, values);
    }
}
```

### Complexity Analysis:
- **Time Complexity:**  
  **O(N)**, as it traverses all nodes.
- **Space Complexity:**  
  **O(N)**, for storing node values in the list.

### Dry Run:
- Input BST:
```
       3
      / \
     1   4
      \
       2
```
- k = 2

**In-order traversal sequence:** [1, 2, 3, 4]

**Output:** 2 (the 2nd smallest element)

---

## Approach 2: In-order Traversal with Early Stop (Recursive)

### Core Idea:
Perform an in-order traversal but **stop early once the k-th smallest element is found** to optimize performance.

### Algorithm:
1. Use a recursive in-order traversal.
2. Keep track of the count of nodes visited.
3. When the count reaches `k`, record the current node's value.
4. Terminate traversal early once the k-th element is found.

### Java Code:
```java
public class KthSmallest {
    private int count = 0;
    private int result = -1;

    public int kthSmallest(TreeNode root, int k) {
        inOrder(root, k);
        return result;
    }

    private void inOrder(TreeNode root, int k) {
        if (root == null || count >= k) return;
        inOrder(root.left, k);
        count++;
        if (count == k) {
            result = root.val;
            return;
        }
        inOrder(root.right, k);
    }
}
```

### Complexity Analysis:
- **Time Complexity:**  
  **O(H + k)**, where H is the height of the BST, because in the best case, traversal stops after visiting k nodes.
- **Space Complexity:**  
  **O(H)**, for the recursion stack (H = height of the BST).

### Dry Run:
- Input BST:
```
       3
      / \
     1   4
      \
       2
```
- k = 2

Traversal steps:
- Visit 1 (count=1), not yet k.
- Visit 2 (count=2), k found → return.

**Output:** 2

---

## Approach 3: Iterative In-order Traversal (Using Stack)

### Core Idea:
Use an explicit stack to perform in-order traversal iteratively, which helps avoid recursion stack overhead.

### Algorithm:
1. Initialize an empty stack.
2. Push all the leftmost nodes starting from root onto the stack.
3. While the stack is not empty:
   - Pop the top node.
   - Decrement k; if k==0, return this node's value.
   - Push all leftmost nodes of the right subtree of this node.

### Java Code:
```java
import java.util.Stack;

public class KthSmallest {
    public int kthSmallest(TreeNode root, int k) {
        Stack<TreeNode> stack = new Stack<>();
        TreeNode current = root;

        while (current != null || !stack.isEmpty()) {
            // Push all left nodes
            while (current != null) {
                stack.push(current);
                current = current.left;
            }
            // Process node
            current = stack.pop();
            k--;
            if (k == 0) return current.val;
            // Move to right subtree
            current = current.right;
        }
        return -1; // k is out of bounds
    }
}
```

### Complexity Analysis:
- **Time Complexity:**  
  **O(H + k)**, similar to recursive early stop, traversing up to k nodes.
- **Space Complexity:**  
  **O(H)**, for the stack.

### Dry Run:
- Input:
```
       3
      / \
     1   4
      \
       2
```
- k = 2

Traversal:
- Push 3, then 1, then null (left of 1), stop.
- Pop 1 (k=2 → 1), decrement k → k=1.
- Push right child 2, then push leftmost nodes of 2 (none).
- Pop 2 (k=1 → 1), decrement k → k=0 → return 2.

**Output:** 2

---

## Approach 4: Augmented BST (Using Node Count)

### Core Idea:
Modify the BST node structure to store the **size of the subtree** rooted at each node. Use these counts to jump directly to the k-th smallest element in **O(log N)** time.

### Preprocessing:
- During insertion or via a separate function, compute and store the size of each subtree.

### Algorithm:
1. At each node, compare `k` with the size of the left subtree + 1:
   - If `k` == size of left subtree + 1, current node is the answer.
   - If `k` < size of left subtree + 1, recurse into the left subtree.
   - Else, recurse into the right subtree with `k` adjusted accordingly.

### Java Code:
```java
class TreeNode {
    int val;
    int size; // size of subtree rooted at this node
    TreeNode left, right;

    TreeNode(int val) {
        this.val = val;
        this.size = 1;
    }
}

public class KthSmallest {
    public int kthSmallest(TreeNode root, int k) {
        if (root == null) return -1;
        int leftSize = (root.left != null) ? root.left.size : 0;
        if (k == leftSize + 1) {
            return root.val;
        } else if (k <= leftSize) {
            return kthSmallest(root.left, k);
        } else {
            return kthSmallest(root.right, k - leftSize - 1);
        }
    }

    // Helper method to update sizes after insertion
    private TreeNode insert(TreeNode root, int val) {
        if (root == null) return new TreeNode(val);
        if (val < root.val) {
            root.left = insert(root.left, val);
        } else {
            root.right = insert(root.right, val);
        }
        root.size = 1 + getSize(root.left) + getSize(root.right);
        return root;
    }

    private int getSize(TreeNode node) {
        return node == null ? 0 : node.size;
    }
}
```

### Complexity Analysis:
- **Time Complexity:**  
  **O(log N)** for balanced BST, **O(N)** in worst case (skewed tree).
- **Space Complexity:**  
  **O(H)** for recursion stack, with additional space for node sizes.

---

# Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
|---|---|---|---|---|
| Brute Force | In-order traversal + sort | **O(N)** | **O(N)** | Simple, but not optimal for large trees |
| Recursive Early Stop | In-order traversal with early termination | **O(H + k)** | **O(H)** | Efficient, stops early |
| Iterative In-order | Using stack for traversal | **O(H + k)** | **O(H)** | Avoids recursion, iterative approach |
| Augmented Tree | Using subtree sizes | **O(log N)** (balanced) | **O(H)** | Fast query, requires extra data structure |

---

## Final Tips for Interviews:
- For small trees, brute-force or simple in-order traversal is sufficient.
- For large or balanced trees, consider the iterative or augmented approach.
- Remember the properties of BST and in-order traversal to leverage sorted order.
- If the BST is static, augmenting nodes with subtree sizes provides the most efficient solution.

---

**Happy coding and good luck with your interviews!**
