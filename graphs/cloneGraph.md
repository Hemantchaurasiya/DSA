# Clone Graph

---

## Problem Overview
Given a reference of a **node** in a connected **undirected graph**, create a deep copy (clone) of the entire graph. Each node contains a value and a list of neighbors.

---

## Approach 1: Recursive DFS (Depth-First Search)
### Core Idea
Use recursion to traverse the graph. For each node, clone it if not already cloned, then recursively clone all its neighbors, ensuring that each node is cloned exactly once.

### Algorithm
1. Use a **HashMap** to map original nodes to their clones to avoid duplicates and handle cycles.
2. Define a recursive function `clone(node)`:
   - If `node` is already in the map, return its clone.
   - Create a new node clone with the same value.
   - Add the clone to the map.
   - For each neighbor of `node`, recursively clone it and add to the clone's neighbors list.
3. Start the recursion from the given node and return the cloned node.

### Java Code
```java
import java.util.*;

class Node {
    public int val;
    public List<Node> neighbors;

    public Node() {
        val = 0;
        neighbors = new ArrayList<>();
    }

    public Node(int _val) {
        val = _val;
        neighbors = new ArrayList<>();
    }

    public Node(int _val, ArrayList<Node> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
}

public class CloneGraph {
    private Map<Node, Node> visited = new HashMap<>();

    public Node cloneGraph(Node node) {
        if (node == null) return null;
        if (visited.containsKey(node))
            return visited.get(node);

        Node cloneNode = new Node(node.val);
        visited.put(node, cloneNode);

        for (Node neighbor : node.neighbors) {
            cloneNode.neighbors.add(cloneGraph(neighbor));
        }
        return cloneNode;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N + E), where N is the number of nodes and E is the number of edges, since each node and edge is processed once.
- **Space Complexity:** O(N) for the recursion stack and the hash map storing cloned nodes.

---

## Approach 2: Iterative BFS
### Core Idea
Use Breadth-First Search (BFS) to traverse the graph level by level, cloning nodes as we go along, and using a map to keep track of cloned nodes.

### Algorithm
1. Use a **HashMap** to map original nodes to their clones.
2. Use a **Queue** for BFS traversal.
3. Initialize by cloning the starting node and enqueue it.
4. While the queue is not empty:
   - Dequeue a node.
   - For each neighbor:
     - If neighbor not cloned, clone it, add to map, and enqueue.
     - Add the cloned neighbor to the current node’s clone neighbors list.
5. Return the clone of the starting node.

### Java Code
```java
public class CloneGraphIterative {
    public Node cloneGraph(Node node) {
        if (node == null) return null;

        Map<Node, Node> map = new HashMap<>();
        Queue<Node> queue = new LinkedList<>();
        Node cloneStart = new Node(node.val);
        map.put(node, cloneStart);
        queue.offer(node);

        while (!queue.isEmpty()) {
            Node current = queue.poll();
            for (Node neighbor : current.neighbors) {
                if (!map.containsKey(neighbor)) {
                    Node neighborClone = new Node(neighbor.val);
                    map.put(neighbor, neighborClone);
                    queue.offer(neighbor);
                }
                // Link clone nodes
                map.get(current).neighbors.add(map.get(neighbor));
            }
        }
        return cloneStart;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N + E), similar to DFS, since each node and edge is processed once.
- **Space Complexity:** O(N), for the hash map and queue.

---

## Dry Run with Sample Input
### Sample Graph
Suppose the graph is:
```
Node 1: neighbors -> [Node 2, Node 4]
Node 2: neighbors -> [Node 1, Node 3]
Node 3: neighbors -> [Node 2, Node 4]
Node 4: neighbors -> [Node 1, Node 3]
```

### Step-by-step (using BFS approach)
| Step | Queue | Current Node | Clone Map Keys | Clone Map Values | Action |
|-------|---------|----------------|------------------|------------------|---------|
| Initial | [Node 1] | Node 1 | {} | {} | Clone Node 1, add to map |
| 1 | [] | Node 1 | {1} | {clone1} | For neighbors (2,4): clone if not exists, enqueue |
| 2 | [Node 2, Node 4] | Node 2 | {1,2} | {clone1, clone2} | Clone Node 2, link to clone1, enqueue if new |
| 3 | [Node 4, Node 3] | Node 4 | {1,2,4} | {clone1, clone2, clone4} | Clone Node 4, link to clone1, enqueue if new |
| 4 | [Node 3] | Node 3 | {1,2,4,3} | {clone1, clone2, clone4, clone3} | Clone Node 3, link to clone2, clone4 |
| ... | ... | ... | ... | ... | Continue until all nodes processed |

---

## Summary
| Approach | Use Case | Pros | Cons |
|--------------|--------------|-------|-------|
| Recursive DFS | Suitable for deep graphs; simple implementation | Clear, straightforward | Risk of stack overflow for very deep graphs |
| Iterative BFS | Suitable for wide graphs; avoids recursion | No stack overflow issue | Slightly more complex to implement |
| Both approaches | Efficient and standard for graph copying | O(N + E) time complexity | Extra space for hash map and traversal data structures |

---

## Final Notes
- Always use a **hash map** to track cloned nodes to prevent duplication and handle cycles.
- Choose DFS or BFS based on graph structure and problem constraints.
- This cloning approach ensures a **deep copy** of the graph, preserving structure without referencing original nodes.

---

**Happy Revising!**
