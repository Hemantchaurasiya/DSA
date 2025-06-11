# Top View of Binary Tree

---

## Problem Overview

Given a binary tree, **print the nodes visible from the top view**. The top view includes the nodes that are visible when the tree is viewed from above, i.e., the nodes with the **minimum horizontal distance (HD)** at each level.

---

## Approach 1: Brute Force (Level Order Traversal + Tracking HDs)

### Core Idea
- Perform a level order traversal (BFS), tracking the **horizontal distance (HD)** for each node.
- For each HD, keep track of the **first node encountered** (since level order traversal visits nodes level by level).
- At the end, the collected nodes for each HD form the top view.

### Algorithm
1. Use a **queue** for BFS traversal; store nodes along with their HDs.
2. Use a **map/dictionary** to record the first node encountered at each HD.
3. Initialize the queue with the root node and HD = 0.
4. While the queue is not empty:
    - Dequeue a node and its HD.
    - If this HD is not recorded yet, store the node's value in the map.
    - Enqueue the left child with HD - 1.
    - Enqueue the right child with HD + 1.
5. After traversal, the map holds the top view nodes in order of their HDs.

### Java Code
```java
import java.util.*;

class Node {
    int data;
    Node left, right;
    Node(int data) {
        this.data = data;
        left = right = null;
    }
}

public class TopView {
    public static List<Integer> topView(Node root) {
        List<Integer> result = new ArrayList<>();
        if (root == null) return result;

        Map<Integer, Integer> hdNodeMap = new TreeMap<>();
        Queue<Pair> queue = new LinkedList<>();
        queue.offer(new Pair(root, 0));

        while (!queue.isEmpty()) {
            Pair current = queue.poll();
            Node node = current.node;
            int hd = current.hd;

            // Record the first node at this HD
            if (!hdNodeMap.containsKey(hd)) {
                hdNodeMap.put(hd, node.data);
            }

            if (node.left != null)
                queue.offer(new Pair(node.left, hd - 1));
            if (node.right != null)
                queue.offer(new Pair(node.right, hd + 1));
        }

        for (int key : hdNodeMap.keySet()) {
            result.add(hdNodeMap.get(key));
        }

        return result;
    }

    static class Pair {
        Node node;
        int hd;
        Pair(Node node, int hd) {
            this.node = node;
            this.hd = hd;
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** **O(N)**, as each node is visited once during BFS.
- **Space Complexity:** **O(N)**, for the queue and map storing nodes and HDs.

### Dry Run Example
Input:
```
        1
       / \
      2   3
       \   \
        4   5
```
- Initial queue: `(1,0)`
- Process `(1,0)`: store 1 at HD=0; enqueue `(2,-1)`, `(3,1)`
- Process `(2,-1)`: store 2 at HD=-1; enqueue `(4,0)`
- Process `(3,1)`: store 3 at HD=1; enqueue `(5,2)`
- Process `(4,0)`: HD=0 already stored, skip
- Process `(5,2)`: store 5 at HD=2

Result: `[2, 1, 3, 5]`

---

## Approach 2: Optimized Solution Using Level Order Traversal with Map

### Core Idea
- Similar to Approach 1 but emphasizes **keeping track of only the first node at each HD** during BFS.
- Using a **TreeMap** (or Sorted Map) to automatically sort HDs for output.

### Algorithm
Same as Approach 1, but explicitly note:
- **Use a TreeMap** for sorted HDs.
- **Only record first occurrence** at each HD.

### Java Code
*(Same as above, just emphasizing the use of TreeMap for sorted output)*

```java
// Same code as Approach 1, with emphasis on TreeMap for sorted order
```

### Complexity Analysis
- **Time Complexity:** **O(N)** (each node processed once).
- **Space Complexity:** **O(N)** (map + queue).

---

## Approach 3: Most Optimal - Using Vertical Order Traversal (Single Pass)

### Core Idea
- Use **vertical order traversal** with **horizontal distances**.
- Track the **first node** encountered at each HD during BFS.
- This approach is essentially the same as previous but optimized for code clarity and efficiency.

### Algorithm
1. Use a **queue** for BFS traversal, storing nodes with their HDs.
2. Use a **LinkedHashMap** or **TreeMap** to maintain insertion order or sorted order.
3. For each node:
    - If HD not in map, add the node's data.
    - Continue BFS.
4. Output the stored nodes in order of HDs.

### Java Code
*(Same as above, but with emphasis on the minimal code for top view)*

---

## Summary

| Approach | Core Idea | Data Structures | Time Complexity | Space Complexity | Notes |
|------------|--------------|-------------------|------------------|-------------------|--------|
| Brute Force | BFS + HD tracking, record first node at HD | Queue + HashMap | O(N) | O(N) | Basic, easy to implement |
| Better / Optimized | Use TreeMap for sorted HDs | Queue + TreeMap | O(N) | O(N) | Sorted output, minimal code |
| Most Optimal | Same as above, concise, efficient | Queue + TreeMap | O(N) | O(N) | Clean, suitable for interviews |

---

## Final Tips
- Always use BFS for level order traversal when dealing with views (top, bottom, vertical).
- Use a map to associate HDs with node data.
- Store only the first node encountered at each HD.
- Use TreeMap if sorted order of HDs is needed.
- For large trees, ensure space complexity is minimized.

---

This structured revision should equip you with the understanding needed for the **Top View of Binary Tree** problem in interviews and quick revisions!
