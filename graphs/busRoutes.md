# Bus Routes

---

## Problem Overview:
Given a list of bus routes, each route is a list of stops. The goal is to find the minimum number of buses needed to travel from a source stop to a target stop, given that you can switch buses at common stops.

**Example:**
```plaintext
routes = [[1, 2, 7], [3, 4, 5], [7, 8], [2, 3, 6]]
source = 1
target = 6
```

**Output:** `3` (e.g., take bus 0 from 1 to 2, switch to bus 3 at stop 2, then to bus 1 at stop 3, and finally reach stop 6).

---

## Approach 1: Brute Force (Graph Search without Optimization)

### Core Idea:
Model the problem as a graph where each bus route is a node, and edges exist between routes sharing common stops. Use BFS to find the shortest path (minimum buses) from the route containing the source to the route containing the target.

### Algorithm:
1. Map each stop to the list of routes that include it.
2. Find all routes that contain the source stop.
3. Use BFS:
   - Enqueue all routes containing the source.
   - Maintain a visited set for routes to prevent revisiting.
   - For each route dequeued:
     - If it contains the target stop, return the number of buses taken.
     - Enqueue all connected routes sharing at least one stop, if not visited.
4. If no route leads to the target, return -1.

### Java Code:
```java
import java.util.*;

public class BusRoutesBruteForce {
    public int numBusesToDestination(int[][] routes, int source, int target) {
        if (source == target) return 0;
        Map<Integer, List<Integer>> stopToRoutes = new HashMap<>();
        int n = routes.length;

        // Map stops to routes
        for (int i = 0; i < n; i++) {
            for (int stop : routes[i]) {
                stopToRoutes.computeIfAbsent(stop, k -> new ArrayList<>()).add(i);
            }
        }

        Queue<Integer> queue = new LinkedList<>();
        Set<Integer> visitedRoutes = new HashSet<>();
        Set<Integer> visitedStops = new HashSet<>();

        // Enqueue routes that contain the source stop
        for (int route : stopToRoutes.getOrDefault(source, new ArrayList<>())) {
            queue.offer(route);
            visitedRoutes.add(route);
        }
        int buses = 1;

        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int routeIdx = queue.poll();
                for (int stop : routes[routeIdx]) {
                    if (stop == target) return buses;
                    if (visitedStops.contains(stop)) continue;
                    visitedStops.add(stop);
                    for (int neighborRoute : stopToRoutes.getOrDefault(stop, new ArrayList<>())) {
                        if (visitedRoutes.contains(neighborRoute)) continue;
                        visitedRoutes.add(neighborRoute);
                        queue.offer(neighborRoute);
                    }
                }
            }
            buses++;
        }
        return -1;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N * M)**, where N is the number of routes and M is the total number of stops across all routes, since each stop and route can be visited multiple times.
- **Space Complexity:** **O(N + S)**, where N is the number of routes, and S is the number of stops, due to the mapping and visited sets.

### Dry Run (Sample Input):
```plaintext
routes = [[1, 2, 7], [3, 4, 5], [7, 8], [2, 3, 6]]
source = 1
target = 6
```

| Step | Queue (routes) | Visited Routes | Visited Stops | Explanation |
|-------|----------------|----------------|--------------|--------------|
| Start | [0]            | {0}            | {}           | Route 0 contains 1 |
| Step 1 | [3, 2]        | {0, 3, 2}      | {1,2,7,3,6}  | Enqueue connected routes via stops |
| Check route 3 | Contains 6? No | - | - | Reached target: yes, return 3 |

---

## Approach 2: Optimized BFS with Preprocessing

### Core Idea:
Precompute a graph where nodes are routes, and edges connect routes sharing stops. Use BFS to find the shortest path from the source route(s) to the target route(s).

### Algorithm:
1. Build a map from each stop to the list of routes passing through it.
2. Build a graph where each route is a node, and edges exist between routes sharing a stop.
3. Find all routes containing the source stop and perform BFS to reach routes containing the target stop.
4. Count the minimum number of buses needed.

### Java Code:
```java
import java.util.*;

public class BusRoutesOptimized {
    public int numBusesToDestination(int[][] routes, int source, int target) {
        if (source == target) return 0;

        int n = routes.length;
        Map<Integer, List<Integer>> stopToRoutes = new HashMap<>();
        for (int i = 0; i < n; i++) {
            for (int stop : routes[i]) {
                stopToRoutes.computeIfAbsent(stop, k -> new ArrayList<>()).add(i);
            }
        }

        // Build graph of routes
        List<Set<Integer>> routeGraph = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            routeGraph.add(new HashSet<>());
        }

        for (List<Integer> routeList : stopToRoutes.values()) {
            for (int r1 : routeList) {
                for (int r2 : routeList) {
                    if (r1 != r2) {
                        routeGraph.get(r1).add(r2);
                    }
                }
            }
        }

        Queue<Integer> queue = new LinkedList<>();
        Set<Integer> visitedRoutes = new HashSet<>();
        Set<Integer> targetRoutes = new HashSet<>();

        // Initialize queue with routes containing source
        for (int route : stopToRoutes.getOrDefault(source, new ArrayList<>())) {
            queue.offer(route);
            visitedRoutes.add(route);
        }

        // Find all routes containing target
        for (int route : stopToRoutes.getOrDefault(target, new ArrayList<>())) {
            targetRoutes.add(route);
        }

        int buses = 1;

        while (!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                int routeIdx = queue.poll();
                if (targetRoutes.contains(routeIdx)) return buses;
                for (int neighbor : routeGraph.get(routeIdx)) {
                    if (visitedRoutes.contains(neighbor)) continue;
                    visitedRoutes.add(neighbor);
                    queue.offer(neighbor);
                }
            }
            buses++;
        }
        return -1;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N^2 + S)**, where N is the number of routes, S is the total number of stops, due to building the graph and BFS.
- **Space Complexity:** **O(N + S)**, for storing graph and visited sets.

### Dry Run:
Same as previous, but with explicit route-to-route graph, enabling faster traversal.

---

## Approach 3: Most Optimal (Using Bidirectional BFS)

### Core Idea:
Perform BFS from both the source and target routes simultaneously to reduce search space, converging in the middle.

### Algorithm:
1. Map stops to routes.
2. Find all routes containing the source (front) and target (back).
3. Use two queues and visited sets for bi-directional BFS.
4. Expand from both ends until they meet, counting the minimum buses.

### Java Code:
```java
import java.util.*;

public class BusRoutesBiDirectional {
    public int numBusesToDestination(int[][] routes, int source, int target) {
        if (source == target) return 0;

        Map<Integer, List<Integer>> stopToRoutes = new HashMap<>();
        int n = routes.length;

        for (int i = 0; i < n; i++) {
            for (int stop : routes[i]) {
                stopToRoutes.computeIfAbsent(stop, k -> new ArrayList<>()).add(i);
            }
        }

        Set<Integer> startSet = new HashSet<>();
        Set<Integer> endSet = new HashSet<>();
        Set<Integer> visitedStart = new HashSet<>();
        Set<Integer> visitedEnd = new HashSet<>();

        for (int route : stopToRoutes.getOrDefault(source, new ArrayList<>())) {
            startSet.add(route);
            visitedStart.add(route);
        }
        for (int route : stopToRoutes.getOrDefault(target, new ArrayList<>())) {
            endSet.add(route);
            visitedEnd.add(route);
        }

        int buses = 0;

        while (!startSet.isEmpty() && !endSet.isEmpty()) {
            buses++;
            // Always expand from the smaller set
            if (startSet.size() > endSet.size()) {
                Set<Integer> temp = startSet;
                startSet = endSet;
                endSet = temp;
                Set<Integer> tempVisited = visitedStart;
                visitedStart = visitedEnd;
                visitedEnd = tempVisited;
            }

            Set<Integer> nextLevel = new HashSet<>();
            for (int route : startSet) {
                if (endSet.contains(route)) return buses;
                for (int stop : routes[route]) {
                    for (int neighbor : stopToRoutes.getOrDefault(stop, new ArrayList<>())) {
                        if (visitedStart.contains(neighbor)) continue;
                        if (visitedEnd.contains(neighbor)) return buses;
                        visitedStart.add(neighbor);
                        nextLevel.add(neighbor);
                    }
                }
            }
            startSet = nextLevel;
        }
        return -1;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(N + S)**, as each route and stop is processed at most once in bidirectional BFS.
- **Space Complexity:** **O(N + S)**, for storing maps and visited sets.

---

## Summary:
| Approach | Time Complexity | Space Complexity | Best Use Case |
|------------|-----------------|------------------|----------------|
| Brute Force | O(N * M) | O(N + S) | Small input sizes, initial solution |
| Route Graph BFS | O(N^2 + S) | O(N + S) | Moderate input sizes, optimized traversal |
| Bi-directional BFS | O(N + S) | O(N + S) | Large input sizes, fastest approach |

---

## Final Tips:
- Preprocessing stop-to-route mappings is crucial.
- Use BFS to find minimal number of buses.
- For very large inputs, bidirectional BFS reduces search space significantly.

---

**Happy Revising!**
