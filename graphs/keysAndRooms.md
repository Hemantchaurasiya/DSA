# Keys and Rooms

---

## Problem Overview
Given a list of rooms, each represented by an array of keys to other rooms, determine if **you can visit all rooms starting from room 0**.  
- **Input:** `rooms` (List of List of integers), where `rooms[i]` contains keys to other rooms.
- **Output:** `true` if all rooms can be visited, `false` otherwise.

---

## Approach 1: Brute Force (Recursive Search without visited check)
### Core Idea
Attempt to visit every room recursively, but without tracking visited rooms, leading to potential infinite loops or repeated visits.

### Algorithm
- Start from room 0.
- For each key in the current room, recursively visit the corresponding room.
- Without marking visited rooms, potentially revisit rooms infinitely, so this approach isn't practical.  
*(Note: Usually, this isn't a valid approach, but it's the conceptual starting point before optimization.)*

### Java Code
```java
// Not recommended due to potential infinite recursion
public boolean canVisitAllRooms(int[][] rooms) {
    // No visited tracking; not practical
    return dfs(0, rooms);
}

private boolean dfs(int room, int[][] rooms) {
    for (int key : rooms[room]) {
        dfs(key, rooms);
    }
    // No check for visited; just attempt to visit all
    return true; // Not reliable
}
```

### Complexity Analysis
- **Time Complexity:** O(∞) in worst case (due to infinite loops), practically undefined without visited check.
- **Space Complexity:** O(n) for recursion stack if acyclic, but potentially infinite.

---

## Approach 2: BFS with Visited Array
### Core Idea
Use a **Breadth-First Search (BFS)** to traverse rooms, keeping track of visited rooms to avoid revisiting.

### Algorithm
1. Initialize a **queue** with room 0.
2. Initialize a **visited array** to mark rooms as visited.
3. While the queue is not empty:
   - Dequeue a room.
   - For each key in this room:
     - If the room is not visited:
       - Mark it visited.
       - Enqueue it.
4. After traversal, check if all rooms are visited.

### Java Code
```java
import java.util.*;

public class KeysAndRooms {
    public boolean canVisitAllRooms(int[][] rooms) {
        int n = rooms.length;
        boolean[] visited = new boolean[n];
        Queue<Integer> queue = new LinkedList<>();
        
        // Start from room 0
        visited[0] = true;
        queue.offer(0);
        
        while (!queue.isEmpty()) {
            int currentRoom = queue.poll();
            for (int key : rooms[currentRoom]) {
                if (!visited[key]) {
                    visited[key] = true;
                    queue.offer(key);
                }
            }
        }
        
        // Check if all rooms are visited
        for (boolean v : visited) {
            if (!v) return false;
        }
        return true;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N + E), where N is the number of rooms, E is the total number of keys across all rooms. Each room is visited at most once.
- **Space Complexity:** O(N) for the `visited` array and the queue.

---

## Approach 3: DFS with Visited Array (Most Optimal)
### Core Idea
Use **Depth-First Search (DFS)** to traverse the rooms recursively, marking visited rooms to prevent cycles.

### Algorithm
1. Initialize a `visited` boolean array.
2. Define a recursive DFS function:
   - Mark the current room as visited.
   - For each key in the current room:
     - If the room is not visited:
       - Recursively DFS into that room.
3. Start DFS from room 0.
4. After DFS completes, verify if all rooms are visited.

### Java Code
```java
public class KeysAndRooms {
    public boolean canVisitAllRooms(int[][] rooms) {
        int n = rooms.length;
        boolean[] visited = new boolean[n];
        dfs(0, rooms, visited);
        for (boolean v : visited) {
            if (!v) return false;
        }
        return true;
    }

    private void dfs(int room, int[][] rooms, boolean[] visited) {
        visited[room] = true;
        for (int key : rooms[room]) {
            if (!visited[key]) {
                dfs(key, rooms, visited);
            }
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(N + E), where N is the number of rooms, E is the total number of keys.
- **Space Complexity:** O(N) for `visited` array and recursion stack in worst case.

---

## Dry Run: Example

### Input:
```plaintext
rooms = [[1], [2], [3], []]
```

---

### BFS Approach:

| Step | Queue | Visited Array | Action | Rooms Visited? | Notes |
|-------|--------|----------------|---------|----------------|--------|
| 1     | [0]    | [true, false, false, false] | Start from room 0 | No | Initialize |
| 2     | []     | [true, false, false, false] | Dequeue 0 | Rooms 1 is key | Enqueue 1 |
| 3     | [1]    | [true, true, false, false] | Dequeue 1 | Room 2 key | Enqueue 2 |
| 4     | [2]    | [true, true, true, false] | Dequeue 2 | Room 3 key | Enqueue 3 |
| 5     | [3]    | [true, true, true, true]  | Dequeue 3 | No new rooms | End |

All rooms visited: **true** → Return `true`.

---

### DFS Approach:

| Call Stack | Current Room | Visited Array | Action | Rooms Visited? | Notes |
|--------------|----------------|----------------|---------|----------------|--------|
| DFS(0)       | 0              | [true, false, false, false] | Visit 0, recurse to keys | |
| DFS(1)       | 1              | [true, true, false, false] | Visit 1, recurse | |
| DFS(2)       | 2              | [true, true, true, false] | Visit 2, recurse | |
| DFS(3)       | 3              | [true, true, true, true]  | Visit 3 | All visited |

All rooms visited: **true** → Return `true`.

---

## Summary
| Approach | Best Use Case | Pros | Cons |
|------------|--------------|-------|-------|
| Brute Force (Recursive without visited) | Conceptual, not practical | Naive implementation | Infinite loops, unbounded recursion |
| BFS with Visited Array | Suitable for shortest path / level order | Prevents cycles, simple | Slightly more space for queue |
| DFS with Visited Array | Efficient traversal | Easy to implement, less memory | Recursive depth may be large |

---

## Final Recommendation
Use **DFS or BFS with a visited array** for optimal efficiency and safety against cycles.

---

**Happy Revising!**
