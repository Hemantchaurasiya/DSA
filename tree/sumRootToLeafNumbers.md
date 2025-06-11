# Sum Root to Leaf Numbers

This problem involves traversing a binary tree to compute the sum of all numbers formed by root-to-leaf paths. Each node contains a digit (0-9), and each path from root to leaf forms a number by concatenating these digits.

---

## 1. Approach Name: Brute Force (Recursive DFS with Path Storage)

### Core Idea:
Generate all root-to-leaf paths, store the digits along each path, then convert each path into a number and sum all numbers at the end.

### Algorithm:
- Traverse the tree using DFS.
- Maintain a list (or string builder) to store the current path's digits.
- When a leaf node is reached, convert the stored path to an integer and add it to a result list.
- After traversal, iterate over all path numbers to compute the sum.

### Java Code:
```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int val) { this.val = val; }
}

public class SumRootToLeafNumbers {
    List<Integer> paths = new ArrayList<>();
    
    public int sumNumbers(TreeNode root) {
        List<Integer> currentPath = new ArrayList<>();
        dfs(root, currentPath);
        int totalSum = 0;
        for (int num : paths) {
            totalSum += num;
        }
        return totalSum;
    }
    
    private void dfs(TreeNode node, List<Integer> currentPath) {
        if (node == null) return;
        currentPath.add(node.val);
        if (node.left == null && node.right == null) {
            // Leaf node: convert path to number
            int num = 0;
            for (int digit : currentPath) {
                num = num * 10 + digit;
            }
            paths.add(num);
        } else {
            dfs(node.left, currentPath);
            dfs(node.right, currentPath);
        }
        currentPath.remove(currentPath.size() - 1);
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(N * H), where N is the number of nodes, H is the height of the tree. Each leaf's path conversion takes O(H), and there could be O(N) leaves.
- **Space Complexity:** O(H) for the recursion stack and path storage, plus O(L) for storing all paths (L = number of leaves).

---

## 2. Approach Name: Better Solution - Recursive DFS with Path Value Passing

### Core Idea:
Instead of storing entire paths, pass down a running total that represents the number formed so far. When reaching a leaf, add the current number to the total sum.

### Algorithm:
- Use DFS traversal.
- Maintain a variable `currentNumber` which is updated at each node: `currentNumber = previousNumber * 10 + node.val`.
- When a leaf is encountered, add `currentNumber` to a global sum.
- Recursively process left and right children.

### Java Code:
```java
public class SumRootToLeafNumbers {
    int totalSum = 0;
    
    public int sumNumbers(TreeNode root) {
        dfs(root, 0);
        return totalSum;
    }
    
    private void dfs(TreeNode node, int currentNumber) {
        if (node == null) return;
        currentNumber = currentNumber * 10 + node.val;
        if (node.left == null && node.right == null) {
            totalSum += currentNumber;
        } else {
            dfs(node.left, currentNumber);
            dfs(node.right, currentNumber);
        }
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(N), each node is visited once.
- **Space Complexity:** O(H), due to recursion stack, where H is the height of the tree.

---

## 3. Approach Name: Optimized Solution - Iterative DFS with Stack

### Core Idea:
Use an explicit stack to simulate DFS traversal, carrying along the current number in each stack frame.

### Algorithm:
- Initialize a stack with the root node and initial current number 0.
- While stack is not empty:
  - Pop a node and its current number.
  - Update current number: `currentNumber = previousNumber * 10 + node.val`.
  - If leaf node, add `currentNumber` to total sum.
  - Else, push the child nodes with updated current number.

### Java Code:
```java
import java.util.*;

public class SumRootToLeafNumbers {
    public int sumNumbers(TreeNode root) {
        if (root == null) return 0;
        int totalSum = 0;
        Stack<Pair<TreeNode, Integer>> stack = new Stack<>();
        stack.push(new Pair<>(root, 0));
        
        while (!stack.isEmpty()) {
            Pair<TreeNode, Integer> pair = stack.pop();
            TreeNode node = pair.getKey();
            int currentNumber = pair.getValue() * 10 + node.val;
            if (node.left == null && node.right == null) {
                totalSum += currentNumber;
            } else {
                if (node.right != null)
                    stack.push(new Pair<>(node.right, currentNumber));
                if (node.left != null)
                    stack.push(new Pair<>(node.left, currentNumber));
            }
        }
        return totalSum;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(N), each node is processed once.
- **Space Complexity:** O(H), for the stack in worst case (skewed tree).

---

## Dry Run Example

### Input Tree:
```
      1
     / \
    2   3
```

---

### Approach 2 (Recursive DFS with Path Value Passing):

| Step | Node | Current Number | Total Sum | Notes                        |
|-------|-------|------------------|--------------|------------------------------|
| Start | 1     | 0                | 0            | Root node, currentNumber=0+1=1 |
|       | Left  | 1                | 0            | Traverse left child (2)     |
|       | 2     | 1*10+2=12        | 0            | Leaf node; totalSum+=12     |
|       | Right | 1                | 12           | Traverse right child (3)    |
|       | 3     | 1*10+3=13        | 12           | Leaf node; totalSum+=13     |

**Final sum:** 12 + 13 = **25**

---

## Summary:
| Approach | Best Use Case | Key Takeaways |
|------------|----------------|---------------|
| Brute Force | Small trees or initial understanding | Stores all paths, less efficient |
| Recursive DFS with Path Value | Medium trees, optimized | Passes current number, reduces storage |
| Iterative DFS | Large trees, stack-based traversal | Avoid recursion, explicit control |

---

**Remember:** The most optimal approach is the **recursive DFS passing current number**, with **O(N)** time and **O(H)** space complexity.
