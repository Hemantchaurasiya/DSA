# Course Schedule II

---

## Problem Statement
Given the total number of courses (`numCourses`) and a list of prerequisite pairs (`prerequisites`), return the order in which courses should be taken to finish all courses. If it is impossible to complete all courses, return an empty array.

**Example:**
```plaintext
Input: numCourses = 4, prerequisites = [[1,0],[2,0],[3,1],[3,2]]
Output: [0,1,2,3] or [0,2,1,3]
```

---

## Approach 1: Brute Force (Topological Sort via DFS with Cycle Detection)

### Core Idea:
- Model the courses as a directed graph.
- Detect cycles (which imply impossible schedule).
- Perform a DFS to find a valid ordering.

### Algorithm:
1. Build a graph with courses as nodes and prerequisites as directed edges.
2. Use three states for each node:
   - **0**: Not visited
   - **1**: Visiting (in recursion stack)
   - **2**: Visited (fully processed)
3. For each node, perform DFS:
   - If a node is currently in the recursion stack, cycle detected → return empty array.
   - Mark node as visiting, recurse all neighbors.
   - After processing neighbors, mark node as visited and add to order list.
4. Reverse the order list at the end to get the course order.

### Java Code:
```java
import java.util.*;

public class CourseScheduleII {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        List<List<Integer>> graph = new ArrayList<>();
        for(int i = 0; i < numCourses; i++) {
            graph.add(new ArrayList<>());
        }
        for(int[] pre : prerequisites) {
            graph.get(pre[1]).add(pre[0]);
        }

        int[] status = new int[numCourses]; // 0=unvisited, 1=visiting, 2=visited
        List<Integer> order = new ArrayList<>();
        
        for(int i = 0; i < numCourses; i++) {
            if(status[i] == 0) {
                if(!dfs(i, graph, status, order))
                    return new int[0]; // cycle detected
            }
        }
        Collections.reverse(order);
        return order.stream().mapToInt(i -> i).toArray();
    }

    private boolean dfs(int node, List<List<Integer>> graph, int[] status, List<Integer> order) {
        if(status[node] == 1) return false; // cycle detected
        if(status[node] == 2) return true;  // already processed

        status[node] = 1; // mark as visiting
        for(int neighbor : graph.get(node)) {
            if(!dfs(neighbor, graph, status, order))
                return false;
        }
        status[node] = 2; // mark as visited
        order.add(node);
        return true;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(V + E)**, where V = number of courses, E = number of prerequisites, due to DFS traversal.
- **Space Complexity:** **O(V + E)** for storing the graph and recursion stack.

### Dry Run:
**Input:** `numCourses = 4`, `prerequisites = [[1,0],[2,0],[3,1],[3,2]]`

| Step | Variables and Data Structures | Description |
|------|------------------------------|-------------|
| Build Graph | {0: [1,2], 1: [3], 2: [3], 3: []} | Map prerequisites |
| Initialize status | [0,0,0,0], order = [] | All unvisited |
| Start DFS from node 0 | status[0]=1 | Visiting node 0 |
| Visit neighbor 1 | status[1]=1 | Visiting node 1 |
| Visit neighbor 3 | status[3]=1 | Visiting node 3 (leaf) |
| Finish node 3 | status[3]=2, order=[3] | Add 3 after processing |
| Finish node 1 | status[1]=2, order=[3,1] | Add 1 after processing |
| Visit neighbor 2 from node 0 | status[2]=1 | Visiting node 2 |
| Visit neighbor 3 (already visited) | skip | Already processed |
| Finish node 2 | status[2]=2, order=[3,1,2] | Add 2 |
| Finish node 0 | status[0]=2, order=[3,1,2,0] | Add 0 |
| Reverse order | [0,2,1,3] |

---

## Approach 2: Topological Sort Using Kahn's Algorithm (BFS)

### Core Idea:
- Use **indegree** of each node to determine the starting nodes.
- Repeatedly pick nodes with indegree 0, add to result, and reduce indegree of neighbors.
- Detect if all nodes are processed (no cycle).

### Algorithm:
1. Build the graph and compute indegree of each node.
2. Initialize a queue with all nodes having indegree 0.
3. While queue not empty:
   - Pop node, add to result.
   - For each neighbor, reduce indegree by 1.
   - If indegree becomes 0, add neighbor to queue.
4. If the result contains all courses, return it; else, return empty array.

### Java Code:
```java
import java.util.*;

public class CourseScheduleII {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        int[] indegree = new int[numCourses];
        List<List<Integer>> graph = new ArrayList<>();
        for(int i=0; i<numCourses; i++) graph.add(new ArrayList<>());
        
        for(int[] pre : prerequisites) {
            graph.get(pre[1]).add(pre[0]);
            indegree[pre[0]]++;
        }

        Queue<Integer> queue = new LinkedList<>();
        for(int i=0; i<numCourses; i++) {
            if(indegree[i] == 0)
                queue.offer(i);
        }

        List<Integer> order = new ArrayList<>();
        while(!queue.isEmpty()) {
            int course = queue.poll();
            order.add(course);
            for(int neighbor : graph.get(course)) {
                indegree[neighbor]--;
                if(indegree[neighbor] == 0)
                    queue.offer(neighbor);
            }
        }

        if(order.size() == numCourses)
            return order.stream().mapToInt(i -> i).toArray();
        else
            return new int[0]; // cycle detected
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(V + E)**, due to processing each node and edge once.
- **Space Complexity:** **O(V + E)** for storing the graph, indegrees, and queue.

### Dry Run:
**Input:** `numCourses=4`, `prerequisites=[[1,0],[2,0],[3,1],[3,2]]`

| Step | Variables and Data Structures | Description |
|------|------------------------------|-------------|
| Build graph | {0:[1,2], 1:[3], 2:[3], 3:[]} | Map prerequisites |
| Indegree | [0,1,1,2] | Count of incoming edges |
| Initialize queue | [0] | Nodes with indegree=0 |
| Process 0 | order=[0], reduce indegree of 1 and 2 | indegree: [0,0,0,2] |
| Enqueue 1 and 2 | queue=[1,2] | As their indegree=0 |
| Process 1 | order=[0,1], reduce indegree of 3 | indegree: [0,0,0,1] |
| Process 2 | order=[0,1,2], reduce indegree of 3 | indegree: [0,0,0,0] |
| Process 3 | order=[0,1,2,3] | End, all courses processed |

---

## Summary:
| Approach | Key Points | Best Use Cases |
|------------|--------------|----------------|
| DFS (Cycle Detection) | Recursive, detects cycles, builds order via post-order | When recursion stack is preferred or graph is dense |
| BFS (Kahn's Algorithm) | Iterative, uses indegree, easy to detect cycles | When an iterative approach is preferred or for large graphs |

---

## Final Notes:
- Both methods detect cycles effectively.
- The BFS approach tends to be more intuitive and easier to implement iteratively.
- Choose the method based on problem constraints and personal preference.

---

Feel free to review these approaches and practice with different graph configurations!
