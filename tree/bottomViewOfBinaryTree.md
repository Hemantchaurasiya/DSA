# Bottom View of Binary Tree

---

## Problem Statement
Given a binary tree, print the **bottom view** of the binary tree. The bottom view contains the nodes that are visible when the tree is viewed from the bottom.

---

## Approach 1: Brute Force  
### 1. Core Idea:
- For each horizontal distance (HD), find the bottom-most node.  
- Since there's no direct way to get bottom view without traversing, the naive approach involves multiple traversals or complex calculations.

### 2. Algorithm:
- **Step 1:** Find the minimum and maximum HDs in the tree.
- **Step 2:** For each HD in the range, perform a level order traversal or DFS to find the **bottom-most node** at that HD.
- **Step 3:** Collect nodes at each HD and select the bottom-most one (i.e., the last node encountered at that HD in level order traversal).

**Note:** This approach involves repeated traversals for each HD, leading to inefficiency.

### 3. Java Code:
```java
import java.util.*;

class Node {
    int data;
    Node left, right;
    Node(int data) { this.data = data; }
}

public class BottomViewBruteForce {
    // Helper function to find min and max HD
    private static void findMinMax(Node root, int hd, int[] minMax) {
        if (root == null) return;
        minMax[0] = Math.min(minMax[0], hd);
        minMax[1] = Math.max(minMax[1], hd);
        findMinMax(root.left, hd - 1, minMax);
        findMinMax(root.right, hd + 1, minMax);
    }

    // Function to find bottom view
    public static List<Integer> bottomView(Node root) {
        int[] minMax = {0, 0};
        findMinMax(root, 0, minMax);
        int minHd = minMax[0], maxHd = minMax[1];
        List<Integer> bottomNodes = new ArrayList<>();

        for (int hd = minHd; hd <= maxHd; hd++) {
            bottomNodes.add(findBottomNodeAtHD(root, hd));
        }
        return bottomNodes;
    }

    private static int findBottomNodeAtHD(Node root, int targetHd) {
        Queue<Node> queue = new LinkedList<>();
        queue.offer(root);
        int bottomNodeData = Integer.MIN_VALUE;

        while (!queue.isEmpty()) {
            Node current = queue.poll();
            // Track nodes at current HD
            if (getHD(root, current, 0) == targetHd) {
                bottomNodeData = current.data;
            }
            if (current.left != null) queue.offer(current.left);
            if (current.right != null) queue.offer(current.right);
        }
        return bottomNodeData;
    }

    // Helper to get HD of a node
    private static int getHD(Node root, Node target, int hd) {
        if (root == null) return Integer.MIN_VALUE;
        if (root == target) return hd;
        int left = getHD(root.left, target, hd - 1);
        if (left != Integer.MIN_VALUE) return left;
        return getHD(root.right, target, hd + 1);
    }

    // Note: The above implementation is conceptual; in practice, you'd cache HDs during traversal.
}
```

### 4. Complexity Analysis:
- **Time Complexity:** **O(N * D)**, where N is the number of nodes, and D is the number of distinct HDs, because for each HD, we traverse the entire tree to find the bottom node.
- **Space Complexity:** **O(N)**, for storing nodes in the traversal queue and recursion stack.

---

## Approach 2: Better Solution using Level Order Traversal (BFS) with HashMap
### 1. Core Idea:
- Use **level order traversal** (BFS) to process nodes level-by-level.
- For each node, update the value in a map keyed by HD.
- Since BFS processes nodes level-by-level starting from the top, the **latest node at each HD** during traversal will be the bottommost node.
- After traversal, the map will contain the bottom view.

### 2. Algorithm:
- Use a queue for BFS, storing pairs of (node, HD).
- Initialize a HashMap<Integer, Integer> to store the latest node's data at each HD.
- Track the minimum and maximum HDs encountered.
- Perform BFS:
  - For each node, update the map at the corresponding HD with the current node's data.
  - Enqueue left and right children with HD - 1 and HD + 1 respectively.
- After BFS completes, iterate from min HD to max HD to retrieve the bottom view.

### 3. Java Code:
```java
public class BottomViewBetter {
    static class Pair {
        Node node;
        int hd;
        Pair(Node node, int hd) {
            this.node = node;
            this.hd = hd;
        }
    }

    public static List<Integer> bottomView(Node root) {
        if (root == null) return new ArrayList<>();

        Map<Integer, Integer> hdNodeMap = new TreeMap<>();
        Queue<Pair> queue = new LinkedList<>();
        queue.offer(new Pair(root, 0));

        int minHd = 0, maxHd = 0;

        while (!queue.isEmpty()) {
            Pair current = queue.poll();
            Node currentNode = current.node;
            int hd = current.hd;

            // Update the map with latest node at HD
            hdNodeMap.put(hd, currentNode.data);

            if (currentNode.left != null) {
                queue.offer(new Pair(currentNode.left, hd - 1));
                minHd = Math.min(minHd, hd - 1);
            }
            if (currentNode.right != null) {
                queue.offer(new Pair(currentNode.right, hd + 1));
                maxHd = Math.max(maxHd, hd + 1);
            }
        }

        // Collect bottom view from map
        List<Integer> bottomViewList = new ArrayList<>();
        for (int hd = minHd; hd <= maxHd; hd++) {
            bottomViewList.add(hdNodeMap.get(hd));
        }
        return bottomViewList;
    }
}
```

### 4. Complexity Analysis:
- **Time Complexity:** **O(N)**, since each node is processed exactly once.
- **Space Complexity:** **O(N)**, for the queue and the map.

---

## Approach 3: Optimized Solution using DFS with Horizontal Distances
### 1. Core Idea:
- Use **depth-first search (DFS)**, traversing the tree.
- Maintain a map with HD as key and a pair of (node data, depth).
- For each node:
  - If the HD is not in map or the current node is at a greater depth, update the map.
- After traversal, extract the nodes from the map in order.

### 2. Algorithm:
- Perform DFS traversal:
  - Pass current node, HD, and depth.
  - Update the map if current depth is greater than stored depth for that HD.
- Sort the map by HD keys and output the node data.

### 3. Java Code:
```java
public class BottomViewDFS {
    static class Pair {
        int data, depth;
        Pair(int data, int depth) {
            this.data = data;
            this.depth = depth;
        }
    }

    public static List<Integer> bottomView(Node root) {
        TreeMap<Integer, Pair> map = new TreeMap<>();
        dfs(root, 0, 0, map);
        List<Integer> result = new ArrayList<>();
        for (Map.Entry<Integer, Pair> entry : map.entrySet()) {
            result.add(entry.getValue().data);
        }
        return result;
    }

    private static void dfs(Node node, int hd, int depth, Map<Integer, Pair> map) {
        if (node == null) return;
        if (!map.containsKey(hd) || depth >= map.get(hd).depth) {
            map.put(hd, new Pair(node.data, depth));
        }
        dfs(node.left, hd - 1, depth + 1, map);
        dfs(node.right, hd + 1, depth + 1, map);
    }
}
```

### 4. Complexity Analysis:
- **Time Complexity:** **O(N)**, as each node is visited once.
- **Space Complexity:** **O(N)**, due to the recursion stack and map.

---

## Summary Table

| Aspect                        | Approach 1: Brute Force                   | Approach 2: BFS with HashMap            | Approach 3: DFS with Map               |
|-------------------------------|--------------------------------------------|----------------------------------------|----------------------------------------|
| **Core Idea**                 | Multiple traversals per HD                | Level order traversal, update map    | DFS traversal, store deepest nodes   |
| **Time Complexity**           | O(N * D) (inefficient)                     | O(N)                                 | O(N)                                 |
| **Space Complexity**          | O(N)                                       | O(N)                                 | O(N)                                 |
| **Suitability**               | Conceptual, less optimal                   | Most practical and efficient         | Alternative, clean, recursive style |

---

## Final Tips for Interview:
- Use **level order traversal** for bottom view to handle overlapping nodes efficiently.
- Maintain a map/dictionary keyed by HD to keep track of the latest (bottom-most) node.
- Update the map during BFS traversal with the latest node encountered at each HD.
- Remember to track min and max HDs to output the result in order.
- DFS approach is elegant but less intuitive for bottom view; BFS is often preferred.

---

**Happy Revising!**
