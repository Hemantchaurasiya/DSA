# Reconstruct Itinerary

This guide provides a comprehensive overview of solving the **Reconstruct Itinerary** problem, progressing from brute-force to optimal solutions. It details the core ideas, algorithms, code, complexity analysis, and dry runs to aid in interview preparation and quick revision.

---

## Problem Summary
Given a list of airline tickets represented as pairs `[from, to]`, reconstruct the itinerary in lexical order starting from `"JFK"`. All tickets must be used exactly once.

---

## Approach 1: Brute Force (Backtracking - Exhaustive Search)

### Core Idea
Try all possible itineraries starting from `"JFK"` and select the one that uses all tickets exactly once, prioritizing lexical order.

### Algorithm
1. Build an adjacency list representing flights from each airport.
2. Use backtracking:
   - Start from `"JFK"`.
   - For each possible destination from current airport (sorted lex order):
     - Remove the ticket from the list temporarily.
     - Recursively attempt to build the rest of the itinerary.
     - If all tickets are used, return the path.
     - Backtrack if no valid path found.
3. Return the constructed itinerary once all tickets are used.

### Recursion Tree Diagram
```
JFK
├── ATL
│   ├── SFO
│   │   └── (All tickets used) -> Return
│   └── ... (Other options)
└── ... (Other destinations from JFK)
```

### Java Code
```java
import java.util.*;

public class ReconstructItinerary {
    List<String> result = new ArrayList<>();
    Map<String, LinkedList<String>> flights = new HashMap<>();

    public List<String> findItinerary(List<List<String>> tickets) {
        // Build adjacency list
        for (List<String> ticket : tickets) {
            flights.computeIfAbsent(ticket.get(0), k -> new LinkedList<>()).add(ticket.get(1));
        }
        // Sort destinations for lexical order
        for (Map.Entry<String, LinkedList<String>> entry : flights.entrySet()) {
            Collections.sort(entry.getValue());
        }

        result.add("JFK");
        backtrack(tickets.size());
        return result;
    }

    private boolean backtrack(int ticketCount) {
        if (result.size() == ticketCount + 1) {
            return true; // All tickets used
        }

        String current = result.get(result.size() - 1);
        if (!flights.containsKey(current) || flights.get(current).isEmpty()) {
            return false; // Dead end
        }

        LinkedList<String> destinations = flights.get(current);
        for (int i = 0; i < destinations.size(); i++) {
            String nextDest = destinations.remove(i);
            result.add(nextDest);
            if (backtrack(ticketCount)) {
                return true;
            }
            result.remove(result.size() - 1);
            destinations.add(i, nextDest);
        }
        return false;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(N!)* in the worst case due to exhaustive search (trying all permutations).
- **Space Complexity:** *O(N)* for recursion stack and adjacency list.

### Dry Run with Sample Input
```java
Tickets = [["JFK","SFO"], ["JFK","ATL"], ["SFO","ATL"], ["ATL","JFK"], ["ATL","SFO"]]
```

- Build adjacency:
  ```
  JFK -> [ATL, SFO]
  SFO -> [ATL]
  ATL -> [JFK, SFO]
  ```
- Sorted adjacency:
  ```
  JFK -> [ATL, SFO]
  SFO -> [ATL]
  ATL -> [JFK, SFO]
  ```
- Path exploration:
  - Start: JFK
  - Choose ATL (lex order)
  - From ATL, choose JFK
  - From JFK, choose SFO
  - From SFO, choose ATL
  - From ATL, choose SFO
  - Path complete: JFK -> ATL -> JFK -> SFO -> ATL -> SFO

---

## Approach 2: Hierholzer’s Algorithm (Optimal Solution - Eulerian Path)

### Core Idea
The problem reduces to finding an **Eulerian Path** in a directed graph:
- Use all edges exactly once.
- The path should be lexicographically smallest.

Hierholzer's algorithm efficiently finds an Eulerian path by:
- Traversing edges in a DFS manner.
- Building the path in reverse, inserting nodes after exploring all outgoing edges.

### Algorithm
1. **Build a graph** as an adjacency list with sorted destinations.
2. **Perform DFS**:
   - While the current node has outgoing edges:
     - Remove the smallest lexical destination.
     - Recurse on that destination.
3. **Add nodes to the itinerary** after exploring all outgoing edges (post-order).
4. **Reverse** the constructed list for the final itinerary.

### Recursion Tree Diagram
```
JFK
├── ATL
│   ├── JFK
│   │   ├── SFO
│   │   └── (dead end)
│   ├── SFO
│   │   └── ATL
│   └── (Back to ATL)
└── SFO
    └── ATL
```

### Java Code
```java
import java.util.*;

public class ReconstructItineraryEuler {
    Map<String, PriorityQueue<String>> flights = new HashMap<>();
    LinkedList<String> itinerary = new LinkedList<>();

    public List<String> findItinerary(List<List<String>> tickets) {
        // Build graph with min-heap for lexical order
        for (List<String> ticket : tickets) {
            flights.computeIfAbsent(ticket.get(0), k -> new PriorityQueue<>()).add(ticket.get(1));
        }
        dfs("JFK");
        return itinerary;
    }

    private void dfs(String airport) {
        PriorityQueue<String> destinations = flights.get(airport);
        while (destinations != null && !destinations.isEmpty()) {
            String next = destinations.poll();
            dfs(next);
        }
        // Add to itinerary in reverse order
        itinerary.addFirst(airport);
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(N log N)* due to sorting of adjacency lists (via PriorityQueue).
- **Space Complexity:** *O(N)* for storing the graph and recursion stack.

### Dry Run with Sample Input
Using the same sample tickets:
- Build adjacency with min-heaps.
- Start DFS at `"JFK"`:
  - Choose `"ATL"` (smallest)
  - From `"ATL"`, choose `"JFK"`, then `"SFO"`, then backtrack.
- Construct the itinerary in reverse:
  - Final: `["JFK", "ATL", "JFK", "SFO", "ATL", "SFO"]`

---

## Summary
| Approach                  | Idea & Intuition                                | Algorithm Summary                                                    | Code Type          | Time Complexity     | Space Complexity   |
|---------------------------|------------------------------------------------|----------------------------------------------------------------------|--------------------|---------------------|--------------------|
| **Brute Force**           | Try all permutations, pick lexicographically smallest | Backtracking with sorting. Use all tickets exactly once.             | Recursive Backtracking | O(N!)               | O(N)               |
| **Hierholzer’s (Optimal)** | Find Eulerian Path using DFS with priority queues | Greedy DFS, remove edges, add nodes post traversal, reverse result. | DFS + PriorityQueue | O(N log N)          | O(N)               |

---

## Final Tips
- Always start with building the graph.
- Use sorting or priority queues to ensure lex order.
- Hierholzer’s algorithm is optimal for Eulerian path problems with constraints like these.
- Practice dry runs with small datasets to understand recursion and backtracking flow.

Good luck with your interview prep!
