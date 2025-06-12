# Reconstruct Itinerary

---

## Problem Overview

Given a list of airline tickets represented as `[from, to]`, reconstruct the itinerary starting from `"JFK"` such that:
- All tickets are used exactly once.
- The resulting itinerary is lexicographically smallest if multiple solutions are possible.

**Example:**
```plaintext
Input: [["MUC","LHR"],["JFK","MUC"],["SFO","SJC"],["LHR","SFO"]]
Output: ["JFK","MUC","LHR","SFO","SJC"]
```

---

## Approach 1: Brute Force (Backtracking / DFS)

### Core Idea
Generate all possible itineraries using backtracking, and select the lexicographically smallest valid route.

### Algorithm
1. Build a list of all tickets and keep track of used tickets.
2. Starting from `"JFK"`, recursively try all reachable destinations:
   - For each ticket starting from current airport:
     - Mark the ticket as used.
     - Append destination to current itinerary.
     - Recurse from the new destination.
     - Backtrack if no complete itinerary is found.
3. Track the lexicographically smallest valid itinerary during the process.
4. Return the valid itinerary once all tickets are used.

**Note:** This approach is highly inefficient for large inputs due to exponential search space.

---

## Approach 2: Graph + Hierarchical Traversal (Optimal, Hierarchical DFS with Sorting)

### Core Idea
Model tickets as a directed graph. Use DFS with lexicographical sorting of adjacency lists to ensure the smallest itinerary is generated.

### Algorithm
1. **Build the Graph:**
   - For each ticket `[from, to]`, add `to` to the adjacency list of `from`.
2. **Sort Adjacency Lists:**
   - Sort each adjacency list lexicographically to prioritize smaller destinations.
3. **Hierarchical DFS:**
   - Start from `"JFK"`.
   - Recurse into the smallest neighbor, removing it from the adjacency list to mark it as used.
   - Continue until all tickets are used.
   - Backtrack if necessary.
4. **Construct the Path:**
   - Use a stack or list to record the path.
   - The path built in reverse order during recursion is reversed at the end.

### Java Code
```java
import java.util.*;

class Solution {
    private Map<String, PriorityQueue<String>> graph = new HashMap<>();
    private LinkedList<String> result = new LinkedList<>();
    
    public List<String> findItinerary(List<List<String>> tickets) {
        // Build the graph
        for (List<String> ticket : tickets) {
            String from = ticket.get(0);
            String to = ticket.get(1);
            graph.putIfAbsent(from, new PriorityQueue<>());
            graph.get(from).offer(to);
        }
        // Start DFS from "JFK"
        dfs("JFK");
        return result;
    }
    
    private void dfs(String airport) {
        PriorityQueue<String> destinations = graph.get(airport);
        while (destinations != null && !destinations.isEmpty()) {
            String next = destinations.poll();
            dfs(next);
        }
        // Add airport to the front (post-order)
        result.addFirst(airport);
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  - Building graph: `O(N)`, where `N` is number of tickets.
  - Sorting adjacency lists: total `O(N log N)` since each list is sorted once.
  - DFS traversal: `O(N)`, each ticket is visited exactly once.
  - Overall: **O(N log N)** due to sorting.

- **Space Complexity:**  
  - Graph storage: `O(N)` for adjacency lists.
  - Call stack and result list: `O(N)`.

---

## Dry Run: Step-by-step Example

**Input:**
```plaintext
tickets = [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
```

### Step 1: Build Graph
- `"JFK"`: `["ATL", "SFO"]` (after sorting: `["ATL", "SFO"]`)
- `"SFO"`: `["ATL"]`
- `"ATL"`: `["JFK", "SFO"]`

### Step 2: DFS from `"JFK"`:
- Visit `"JFK"`:
  - Poll `"ATL"` (smallest)
    - Visit `"ATL"`:
      - Poll `"JFK"`:
        - Visit `"JFK"`:
          - Poll `"SFO"`:
            - Visit `"SFO"`:
              - Poll `"ATL"`:
                - Visit `"ATL"`:
                  - No more destinations; add `"ATL"` to result.
              - No more destinations; add `"SFO"` to result.
          - No more destinations; add `"JFK"` to result.
      - Poll `"SFO"`:
        - Visit `"SFO"`:
          - No destinations; add `"SFO"` to result.
- Final itinerary (reverse order): `"JFK" -> "ATL" -> "JFK" -> "SFO" -> "ATL"`.

Reversing the result gives: `["JFK", "ATL", "JFK", "SFO", "ATL"]`.

---

## Summary

| Approach | Core Idea | Algorithm Highlights | Code Snippet | Time Complexity | Space Complexity |
|------------|------------|------------------------|--------------|-----------------|------------------|
| Brute Force | Generate all possible permutations | Backtracking with pruning | Not practical | Exponential `O(N!)` | `O(N)` (recursion stack) |
| Hierarchical DFS + Sorting | Use graph + lexicographical DFS | Build graph, sort adjacency, DFS post-order | Provided above | `O(N log N)` | `O(N)` |

---

## Final Tips for Interview Preparation
- Understand how to model the problem as a graph.
- Practice DFS with backtracking and post-order traversal.
- Use priority queues to always pick the lexicographically smallest destination.
- Be mindful of edge cases, such as disconnected graphs or multiple routes.
- Remember that Hierarchical DFS with post-order traversal naturally constructs the correct itinerary.

---

This comprehensive guide should help you master the **Reconstruct Itinerary** problem and be well-prepared for interviews.
