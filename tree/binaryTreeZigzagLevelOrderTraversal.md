# Binary Tree Zigzag Level Order Traversal - DSA Revision Note

This problem involves traversing a binary tree level by level, but with alternating directions ("zigzag"). The goal is to return a list of lists, where each inner list contains node values at each level, traversed left-to-right, then right-to-left, alternately.

---

## Approach 1: Brute Force (Level Order Traversal + Reversal)

### **Core Idea:**
Perform a standard level order traversal (BFS) to get nodes level-wise, then reverse the order of nodes at every alternate level to achieve the zigzag pattern.

### **Algorithm:**
1. Initialize a queue and add the root node.
2. While the queue is not empty:
   - Determine the number of nodes at the current level (`size`).
   - For each node at this level:
     - Pop from the queue, add its value to a temporary list.
     - Add its children to the queue.
   - After processing the level:
     - If the level index is odd, reverse the temporary list.
     - Append the list to the result.
3. Return the result after processing all levels.

### **Java Code:**
```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    boolean leftToRight = true;

    while (!queue.isEmpty()) {
        int size = queue.size();
        List<Integer> level = new ArrayList<>();

        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();
            level.add(node.val);

            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }

        if (!leftToRight) {
            Collections.reverse(level);
        }
        result.add(level);
        leftToRight = !leftToRight; // Toggle direction
    }
    return result;
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N)**, where N is the number of nodes.
  - Each node is visited once; reversing at each level is O(k), but total across all levels sums to O(N).
- **Space Complexity:** **O(N)**, for the queue and result storage.

### **Dry Run:**

**Input Tree:**
```
       1
      / \
     2   3
    / \   \
   4   5   6
```

| Step | Queue                    | Level List | Reversed? | Result             | Next Queue                   |
|-------|--------------------------|--------------|-----------|--------------------|------------------------------|
| 1     | [1]                      | [1]          | No        | [[1]]             | [2, 3]                       |
| 2     | [2, 3]                   | [2, 3]       | Yes       | [[1], [3, 2]]     | [4, 5, 6]                    |
| 3     | [4, 5, 6]                | [4, 5, 6]    | No        | [[1], [3, 2], [4, 5, 6]] | []                        |

---

## Approach 2: Optimized Zigzag using Deque for O(1) Reversal

### **Core Idea:**
Instead of reversing the list at each level, use a **Deque** (double-ended queue) to insert values at either end based on the current level's traversal direction. This reduces the overhead of reversing.

### **Algorithm:**
1. Initialize a queue with the root node.
2. Maintain a boolean `leftToRight` to track direction.
3. At each level:
   - Use a `Deque<Integer>` to store node values.
   - For each node:
     - If `leftToRight` is `true`, add the node value at the end.
     - Else, add at the beginning.
   - After processing the level, convert the deque to a list and add to result.
   - Toggle `leftToRight`.
4. Continue until all levels are processed.

### **Java Code:**
```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    if (root == null) return result;

    Queue<TreeNode> queue = new LinkedList<>();
    queue.offer(root);
    boolean leftToRight = true;

    while (!queue.isEmpty()) {
        int size = queue.size();
        Deque<Integer> levelDeque = new LinkedList<>();

        for (int i = 0; i < size; i++) {
            TreeNode node = queue.poll();

            if (leftToRight) {
                levelDeque.offerLast(node.val);
            } else {
                levelDeque.offerFirst(node.val);
            }

            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }

        result.add(new ArrayList<>(levelDeque));
        leftToRight = !leftToRight;
    }

    return result;
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N)** for visiting all nodes.
- **Space Complexity:** **O(N)** for the queue and result storage.

### **Dry Run:**

Using the same tree:

| Step | Queue          | Level Deque (before conversion) | Result             | Next Queue             |
|-------|----------------|--------------------------------|--------------------|------------------------|
| 1     | [1]            | [1]                            | [[1]]             | [2, 3]                 |
| 2     | [2, 3]         | [3, 2]                         | [[1], [3, 2]]     | [4, 5, 6]              |
| 3     | [4, 5, 6]      | [4, 5, 6]                      | [[1], [3, 2], [4, 5, 6]] | []             |

---

## Approach 3: DFS (Recursive) with Level Tracking (Less common for zigzag)

### **Core Idea:**
Use DFS to traverse the tree, track the level, and insert node values into the result list:
- For even levels, append at the end.
- For odd levels, insert at the beginning (or vice versa).

### **Algorithm:**
1. Maintain a list of lists for result.
2. Define a recursive DFS function:
   - If the current level doesn't exist in result, create a new list.
   - Add the current node's value:
     - Append if level is even.
     - Insert at start if level is odd.
   - Recurse into left and right children, increasing level.
3. Return the result after traversal.

### **Java Code:**
```java
public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
    List<List<Integer>> result = new ArrayList<>();
    dfs(root, 0, result);
    return result;
}

private void dfs(TreeNode node, int level, List<List<Integer>> result) {
    if (node == null) return;

    if (result.size() <= level) {
        result.add(new LinkedList<>());
    }

    if (level % 2 == 0) {
        result.get(level).add(node.val);
    } else {
        ((LinkedList<Integer>) result.get(level)).addFirst(node.val);
    }

    dfs(node.left, level + 1, result);
    dfs(node.right, level + 1, result);
}
```

### **Complexity Analysis:**
- **Time Complexity:** **O(N)**, visiting each node once.
- **Space Complexity:** **O(N)**, due to recursion stack and result storage.

### **Dry Run:**

Using the same tree:

| Level | Call Stack | Result after visiting nodes | Final Result                     |
|--------|--------------|------------------------------|----------------------------------|
| 0      | root(1)      | [ [1] ]                     | [[1]]                           |
| 1      | left(2), right(3) | [ [1], [3, 2] ] | [[1], [3, 2]]                   |
| 2      | 4, 5, 6     | Final: [[1], [3, 2], [4, 5, 6]] | |

---

# Summary:

| Approach | Time Complexity | Space Complexity | Key Idea | Best Use Case |
|------------|------------------|-------------------|------------|--------------|
| Brute Force (Level order + reversal) | O(N) | O(N) | BFS + reversal at every odd level | Simple, easy to implement |
| Optimized (Deque approach) | O(N) | O(N) | BFS + insert at ends via deque | Efficient, avoids reversal overhead |
| DFS (Recursive) | O(N) | O(N) | Depth-first traversal with level-based insertion | When recursion preferred |

---

## Final Tips:
- Use **BFS** with a **queue** for clarity and control over levels.
- Use **Deque** to efficiently alternate insertion directions.
- For large trees, prefer the deque approach to reduce reversal overhead.
- Remember to toggle the traversal direction after each level.

---

**Happy coding!**
