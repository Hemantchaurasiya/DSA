# Vertical Order Traversal of a Binary Tree

---

## Problem Overview
Given a binary tree, **vertical order traversal** involves grouping nodes based on their **column index** (or x-coordinate), where the root is at column 0, left child at column -1, right child at column +1, and so on. The goal is to output nodes column-wise from **leftmost** to **rightmost**, and within each column, nodes should be ordered **from top to bottom**. If multiple nodes are in the same position (same row and column), they should be sorted by their value.

---

## Approach 1: Brute Force (Using Level Order Traversal + Sorting)

### Core Idea
- Perform a **level order traversal** (BFS) of the tree.
- Track **column indices** for each node.
- Collect nodes in a **map of column to list of nodes**.
- After traversal, sort nodes in each column based on their row and value.
- Output columns from leftmost to rightmost.

### Algorithm
1. Use a **queue** to perform BFS. Each element in the queue is a tuple: `(node, row, col)`.
2. Maintain a **dictionary**: `column_map`, mapping `col` to a list of `(row, value)` pairs.
3. Track the **minimum and maximum column indices** encountered.
4. During BFS:
   - For each node, add `(row, value)` to `column_map[col]`.
   - Enqueue left child with `(row+1, col-1)` and right child with `(row+1, col+1)`.
5. After BFS, for each column from `min_col` to `max_col`, sort the list of `(row, value)` pairs:
   - First by `row`, and if tied, by `value`.
6. Collect the sorted node values into the result list.

### Java Code
```java
import java.util.*;

public class VerticalOrderTraversal {
    public List<List<Integer>> verticalTraversal(TreeNode root) {
        // Map: column -> list of (row, val)
        TreeMap<Integer, List<Pair>> columnMap = new TreeMap<>();
        Queue<Tuple> queue = new LinkedList<>();
        queue.offer(new Tuple(root, 0, 0));
        
        while (!queue.isEmpty()) {
            Tuple current = queue.poll();
            TreeNode node = current.node;
            int row = current.row;
            int col = current.col;
            columnMap.putIfAbsent(col, new ArrayList<>());
            columnMap.get(col).add(new Pair(row, node.val));
            
            if (node.left != null) {
                queue.offer(new Tuple(node.left, row + 1, col - 1));
            }
            if (node.right != null) {
                queue.offer(new Tuple(node.right, row + 1, col + 1));
            }
        }
        
        List<List<Integer>> result = new ArrayList<>();
        for (List<Pair> pairs : columnMap.values()) {
            Collections.sort(pairs, (a, b) -> {
                if (a.row == b.row) return Integer.compare(a.val, b.val);
                return Integer.compare(a.row, b.row);
            });
            List<Integer> columnNodes = new ArrayList<>();
            for (Pair p : pairs) {
                columnNodes.add(p.val);
            }
            result.add(columnNodes);
        }
        return result;
    }
    
    class Tuple {
        TreeNode node;
        int row, col;
        Tuple(TreeNode node, int row, int col) {
            this.node = node;
            this.row = row;
            this.col = col;
        }
    }
    
    class Pair {
        int row, val;
        Pair(int row, int val) {
            this.row = row;
            this.val = val;
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  `O(N log N)` due to sorting of nodes in each column (each node is processed once, sorting depends on the number of nodes per column).
  
- **Space Complexity:**  
  `O(N)` for storing nodes in the map and queue.

---

## Approach 2: Better Solution (Using BFS + Priority Queue)

### Core Idea
- Use BFS to process nodes level by level.
- For each node, insert `(row, col, value)` into a **min-heap** (priority queue) that sorts nodes **by column, then row, then value**.
- After BFS, process nodes in the priority queue to group by columns.

### Algorithm
1. Use a **priority queue** that sorts nodes by `(col, row, value)`.
2. Perform BFS:
   - For each node, add `(col, row, val)` to the priority queue.
   - Enqueue children with updated `(row+1, col±1)`.
3. After BFS, process the priority queue:
   - Extract nodes in order.
   - Group nodes by column.
4. Collect nodes per column into the output list.

### Java Code
```java
public List<List<Integer>> verticalTraversal(TreeNode root) {
    PriorityQueue<NodeInfo> pq = new PriorityQueue<>(
        (a, b) -> a.col != b.col ? a.col - b.col :
                  a.row != b.row ? a.row - b.row : a.val - b.val);
    Queue<Tuple> queue = new LinkedList<>();
    queue.offer(new Tuple(root, 0, 0));
    
    while (!queue.isEmpty()) {
        Tuple current = queue.poll();
        TreeNode node = current.node;
        int row = current.row, col = current.col;
        pq.offer(new NodeInfo(row, col, node.val));
        if (node.left != null) {
            queue.offer(new Tuple(node.left, row + 1, col - 1));
        }
        if (node.right != null) {
            queue.offer(new Tuple(node.right, row + 1, col + 1));
        }
    }
    
    Map<Integer, List<Integer>> columnMap = new LinkedHashMap<>();
    int minCol = Integer.MAX_VALUE, maxCol = Integer.MIN_VALUE;
    while (!pq.isEmpty()) {
        NodeInfo nodeInfo = pq.poll();
        minCol = Math.min(minCol, nodeInfo.col);
        maxCol = Math.max(maxCol, nodeInfo.col);
        columnMap.putIfAbsent(nodeInfo.col, new ArrayList<>());
        columnMap.get(nodeInfo.col).add(nodeInfo.val);
    }
    
    List<List<Integer>> result = new ArrayList<>();
    for (int c = minCol; c <= maxCol; c++) {
        result.add(columnMap.get(c));
    }
    return result;
}

class NodeInfo {
    int row, col, val;
    NodeInfo(int row, int col, int val) {
        this.row = row;
        this.col = col;
        this.val = val;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  `O(N log N)` due to insertion into priority queue.
  
- **Space Complexity:**  
  `O(N)` for storing nodes and the priority queue.

---

## Approach 3: Most Optimal Solution (Using DFS + Coordinate Hashing)

### Core Idea
- Use **DFS** to traverse the tree.
- Track `(row, col)` for each node.
- Store nodes in a **hash map** keyed by `col`, with values as a list of `(row, val)`.
- After DFS, process each column:
  - Sort nodes by `row` and `val`.
  - Collect results in order from leftmost to rightmost column.

This approach avoids the overhead of sorting large amounts of data during BFS, and leverages recursion.

### Algorithm
1. Use DFS:
   - Start from root at `(row=0, col=0)`.
   - For each node, insert `(row, val)` into `columnMap[col]`.
   - Recurse left with `(row+1, col-1)` and right with `(row+1, col+1)`.
2. Determine the range of columns.
3. For each column in sorted order:
   - Sort the list of `(row, val)` by `row`, then `val`.
   - Collect node values.

### Java Code
```java
public List<List<Integer>> verticalTraversal(TreeNode root) {
    TreeMap<Integer, List<Pair>> columnMap = new TreeMap<>();
    dfs(root, 0, 0, columnMap);
    List<List<Integer>> result = new ArrayList<>();
    for (List<Pair> pairs : columnMap.values()) {
        Collections.sort(pairs, (a, b) -> {
            if (a.row == b.row) return Integer.compare(a.val, b.val);
            return Integer.compare(a.row, b.row);
        });
        List<Integer> columnNodes = new ArrayList<>();
        for (Pair p : pairs) {
            columnNodes.add(p.val);
        }
        result.add(columnNodes);
    }
    return result;
}

private void dfs(TreeNode node, int row, int col, TreeMap<Integer, List<Pair>> columnMap) {
    if (node == null) return;
    columnMap.putIfAbsent(col, new ArrayList<>());
    columnMap.get(col).add(new Pair(row, node.val));
    dfs(node.left, row + 1, col - 1, columnMap);
    dfs(node.right, row + 1, col + 1, columnMap);
}
```

### Complexity Analysis
- **Time Complexity:**  
  `O(N log N)` due to sorting nodes within each column.
  
- **Space Complexity:**  
  `O(N)` for storing node coordinates and the map.

---

# Summary
| Approach | Idea | Data Structures | Sorting | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- | --- |
| **Brute Force** | BFS + sort nodes per column | HashMap + Sorting | Yes | `O(N log N)` | `O(N)` |
| **Better Solution** | BFS + Priority Queue | Priority Queue | Implicit in PQ | `O(N log N)` | `O(N)` |
| **Optimal DFS** | DFS + HashMap + Sorting | TreeMap + List | Yes | `O(N log N)` | `O(N)` |

---

This comprehensive guide should help you understand various approaches to solve **Vertical Order Traversal** efficiently, and prepare for interviews with clarity.
