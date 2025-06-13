# Parallel Courses III

---

## Problem Statement
Given `n` courses labeled from `0` to `n-1`, and a list of prerequisite relations where to take course `i` you need to complete course `prev`, find the minimum number of semesters required to complete all courses. Each semester, you can take any number of courses, provided all their prerequisites are finished.

**Input:**
- `n`: Total number of courses.
- `prerequisites`: List of prerequisite pairs, e.g., `[[1, 0], [2, 1]]`.

**Output:**
- The minimum number of semesters needed, or `-1` if it's impossible due to cycles.

---

## Approach 1: Brute Force (Exponential Search)

### **Core Idea:**
- Generate all possible course completion sequences.
- Check for prerequisites satisfaction at each sequence.
- Find the minimum number of steps (semesters).

### **Algorithm:**
- Generate all permutations of courses.
- For each sequence:
  - Verify if prerequisites are satisfied at each step.
  - Count the number of semesters (steps) needed.
- Return the minimal semester count across all valid sequences.

**Note:** This approach is highly inefficient (factorial time) and only feasible for very small `n`.

---

## Approach 2: Topological Sorting + BFS (Level-based Scheduling)

### **Core Idea:**
- Use **topological sorting** to determine a valid order of courses.
- Use **BFS** to process courses level by level, where each level represents a semester.
- The maximum depth of the BFS layers indicates the minimum number of semesters.

### **Algorithm:**
1. Build an adjacency list of the graph based on prerequisites.
2. Calculate in-degree for each course.
3. Initialize a queue with all courses having in-degree 0 (courses with no prerequisites).
4. Initialize `semesters` counter to 0.
5. While the queue is not empty:
   - Increment `semesters`.
   - For each course in the current level:
     - For each neighbor:
       - Decrement its in-degree.
       - If in-degree becomes 0, add it to the queue.
6. If all courses are processed, return `semesters`. Else, return `-1` (cycle detected).

---

## Approach 3: Dynamic Programming with Topological Traversal (Most Efficient)

### **Core Idea:**
- Perform a topological traversal.
- Use a **DP array** where `dp[course]` is the minimum number of semesters needed to complete that course.
- Update `dp[course]` based on the maximum `dp` values of its prerequisites plus one.

### **Algorithm:**
1. Build the graph (adjacency list) and compute in-degrees.
2. Initialize a queue with all courses with in-degree 0, set their `dp` values to 1.
3. While the queue is not empty:
   - Dequeue a course.
   - For each neighbor:
     - Update `dp[neighbor] = max(dp[neighbor], dp[current] + 1)`.
     - Decrement in-degree of neighbor.
     - If in-degree becomes 0, enqueue neighbor.
4. After processing, if some courses still have non-zero in-degree, there's a cycle → return `-1`.
5. The answer is the maximum value in `dp` array.

---

## Java Implementation: DP with Topological Sorting

```java
import java.util.*;

public class ParallelCoursesIII {
    public int minNumberOfSemesters(int n, int[][] prerequisites) {
        List<List<Integer>> graph = new ArrayList<>();
        int[] inDegree = new int[n];
        int[] dp = new int[n];

        for (int i = 0; i < n; i++) {
            graph.add(new ArrayList<>());
            dp[i] = 1; // Minimum semesters to complete course i
        }

        // Build graph and in-degree array
        for (int[] prereq : prerequisites) {
            graph.get(prereq[1]).add(prereq[0]);
            inDegree[prereq[0]]++;
        }

        Queue<Integer> queue = new LinkedList<>();
        // Initialize queue with courses having in-degree 0
        for (int i = 0; i < n; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
            }
        }

        int completedCourses = 0;

        while (!queue.isEmpty()) {
            int course = queue.poll();
            completedCourses++;
            for (int neighbor : graph.get(course)) {
                // Update dp for neighbor
                dp[neighbor] = Math.max(dp[neighbor], dp[course] + 1);
                inDegree[neighbor]--;
                if (inDegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Check if all courses are completed
        if (completedCourses != n) {
            return -1; // Cycle exists
        }

        // Return the maximum number of semesters needed
        int maxSemesters = 0;
        for (int val : dp) {
            maxSemesters = Math.max(maxSemesters, val);
        }
        return maxSemesters;
    }
}
```

---

## **Complexity Analysis:**

| Aspect | Description |
|---------|--------------|
| **Time Complexity** | **O(N + E)**, where `N` is the number of courses, and `E` is the number of prerequisite relations. Each node and edge is processed once. |
| **Space Complexity** | **O(N + E)** for storing the graph, in-degree array, and DP array. |

---

## **Dry Run Example:**

### Input:
```plaintext
n = 4
prerequisites = [[1,0],[2,1],[3,2]]
```

### Step-by-step:
| Step | Action | Variables / Data Structures | Result |
|---------|---------|------------------------------|---------|
| 1 | Build graph | 0 -> 1, 1 -> 2, 2 -> 3 | Graph adjacency list |
| 2 | Compute in-degree | inDegree[0]=0, inDegree[1]=1, inDegree[2]=1, inDegree[3]=1 | Initialize in-degree array |
| 3 | Queue initialization | Queue contains [0] | Start from course 0 with in-degree 0 |
| 4 | Process course 0 | dp[0]=1 | Decrease in-degree of 1: inDegree[1]=0, enqueue 1 |
| 5 | Process course 1 | dp[1]=max(1, dp[0]+1)=2 | Decrease in-degree of 2: inDegree[2]=0, enqueue 2 |
| 6 | Process course 2 | dp[2]=max(1, dp[1]+1)=3 | Decrease in-degree of 3: inDegree[3]=0, enqueue 3 |
| 7 | Process course 3 | dp[3]=max(1, dp[2]+1)=4 | All courses completed in 4 semesters |
| **Result:** | | Max in `dp` array = 4 | **Minimum semesters = 4** |

---

## **Summary:**

- Use **topological sorting** to process courses respecting prerequisites.
- Maintain a **DP array** to track the earliest semester each course can be completed.
- Detect cycles by checking if all courses are processed.
- The method ensures an optimal solution with linear time complexity.

---

Practice with different prerequisite graphs to get comfortable with this approach!
