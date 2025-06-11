# Binary Tree Postorder Traversal (Iterative)

---

## Problem Overview
Given a binary tree, perform a **postorder traversal** (left, right, root) iteratively and return the list of node values.

---

## Approach 1: Brute Force (Recursive)
### Core Idea:
Use recursion to traverse the tree in postorder directly.

### Algorithm:
1. Create a recursive helper function.
2. Traverse the left subtree.
3. Traverse the right subtree.
4. Visit the current node (add its value to the result list).

### Java Code:
```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    helper(root, result);
    return result;
}

private void helper(TreeNode node, List<Integer> result) {
    if (node == null) return;
    helper(node.left, result);
    helper(node.right, result);
    result.add(node.val);
}
```

### Complexity Analysis:
- **Time Complexity:** O(n), where n is the number of nodes, as each node is visited once.
- **Space Complexity:** O(h), where h is the height of the tree, due to the recursion stack.

### Dry Run:
Input:  
```
    1
     \
      2
     /
    3
```
Steps:
- helper(1): recurse left (null), recurse right (2)
- helper(2): recurse left (3), recurse right (null)
- helper(3): add 3, return
- add 2, return
- add 1, return

Output: `[3, 2, 1]`

---

## Approach 2: Using Two Stacks
### Core Idea:
Simulate postorder traversal by leveraging two stacks:
- The first stack is used for visiting nodes.
- The second stack stores nodes in reverse postorder.
- Finally, pop from the second stack to get the postorder sequence.

### Algorithm:
1. Initialize two stacks: `stack1` and `stack2`.
2. Push the root onto `stack1`.
3. While `stack1` is not empty:
   - Pop a node `curr` from `stack1`.
   - Push `curr` onto `stack2`.
   - Push `curr.left` and `curr.right` onto `stack1` (if they exist).
4. After the loop, `stack2` contains nodes in reverse postorder.
5. Pop all nodes from `stack2` and add their values to the result list.

### Java Code:
```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Deque<TreeNode> stack1 = new ArrayDeque<>();
    Deque<TreeNode> stack2 = new ArrayDeque<>();
    stack1.push(root);

    while (!stack1.isEmpty()) {
        TreeNode curr = stack1.pop();
        stack2.push(curr);
        if (curr.left != null) stack1.push(curr.left);
        if (curr.right != null) stack1.push(curr.right);
    }

    while (!stack2.isEmpty()) {
        result.add(stack2.pop().val);
    }
    return result;
}
```

### Complexity Analysis:
- **Time Complexity:** O(n), each node is pushed and popped once.
- **Space Complexity:** O(n), for the two stacks in the worst case.

### Dry Run:
Input:
```
    1
     \
      2
     /
    3
```
Steps:
- Push root (1) to `stack1`.
- Pop 1, push to `stack2`.
- Push 2 onto `stack1`.
- Pop 2, push to `stack2`.
- Push 3 onto `stack1`.
- Pop 3, push to `stack2`.
- Now, `stack1` empty.
- Pop from `stack2`: 3, 2, 1 → postorder: `[3, 2, 1]`.

---

## Approach 3: Optimized Iterative Solution (Single Stack + Previous Node Tracking)
### Core Idea:
Use a single stack to simulate the traversal, and keep track of the last visited node to determine when to process the current node.

### Algorithm:
1. Initialize an empty stack and set `current` to root.
2. Use `lastVisited` pointer to track the last node processed.
3. While `current` is not null or stack not empty:
   - Traverse down the tree, pushing nodes onto the stack until reaching the leftmost node.
   - Peek the top node of the stack:
     - If the node's right child is null or already processed, process the node (add to result), pop it, update `lastVisited`.
     - Else, set `current` to right child and continue.

### Java Code:
```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Deque<TreeNode> stack = new ArrayDeque<>();
    TreeNode current = root;
    TreeNode lastVisited = null;

    while (current != null || !stack.isEmpty()) {
        // Go to the leftmost node
        while (current != null) {
            stack.push(current);
            current = current.left;
        }

        TreeNode peekNode = stack.peek();
        // If right child exists and not processed yet
        if (peekNode.right != null && lastVisited != peekNode.right) {
            current = peekNode.right;
        } else {
            result.add(peekNode.val);
            lastVisited = stack.pop();
        }
    }
    return result;
}
```

### Complexity Analysis:
- **Time Complexity:** O(n), each node is processed once.
- **Space Complexity:** O(h), due to the stack, where h is the height of the tree.

### Dry Run:
Input:
```
    1
     \
      2
     /
    3
```
Steps:
- Push 1, go left: null, so peek 1:
  - Right child 2 exists and not processed.
  - Set current to 2.
- Push 2, go left: push 3.
- Push 3, go left: null.
- Peek 3:
  - No right child, process 3 → add to result, pop 3.
- Peek 2:
  - Right child is null or processed, process 2 → add to result, pop 2.
- Peek 1:
  - Right child 2 processed, process 1 → add to result, pop 1.

Result: `[3, 2, 1]`

---

# Summary Table

| Approach | Core Idea | Data Structures | Time Complexity | Space Complexity | Notes |
| --- | --- | --- | --- | --- | --- |
| Recursive | Direct recursion for postorder | Call stack | O(n) | O(h) | Simple but not iterative |
| Two Stacks | Reverse postorder with two stacks | Two stacks | O(n) | O(n) | Slightly more space, cleaner iterative approach |
| Single Stack + Last Visited | Simulate traversal with one stack | One stack + pointer | O(n) | O(h) | Most space-efficient iterative solution |

---

## Final Tips:
- Understand the traversal order and how to simulate it iteratively.
- Use auxiliary data structures like stacks to manage traversal state.
- Keep track of last visited nodes to avoid revisiting processed nodes.
- Practice dry runs with various tree shapes (balanced, skewed) to master iterative traversal.

---

**Happy Revision & Good luck with your interview!**
