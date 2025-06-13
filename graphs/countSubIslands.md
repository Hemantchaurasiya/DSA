# Count Sub Islands

**Problem Statement:**  
Given two binary matrices `grid1` and `grid2` of the same size, count the number of **sub islands** in `grid2`.  
A **sub island** is an island in `grid2` that is entirely contained within an island in `grid1`.  
In other words, for each island in `grid2`, if all land cells of that island are also land cells in `grid1`, it counts as a sub island.

---

## 1. Approach 1: Brute Force (Naive)

### **Core Idea:**
Iterate through each cell of `grid2`. When a land cell (`1`) is found, perform a DFS/BFS to explore the entire island in `grid2`. During exploration, check if the corresponding cells in `grid1` are also land (`1`). If all are land, increment the count; otherwise, discard this island.

---

### **Algorithm:**
1. Initialize `count = 0`.
2. Loop over each cell `(i, j)`:
   - If `grid2[i][j] == 1`, call DFS/BFS to explore the island.
   - During traversal:
     - Check if the corresponding cell in `grid1` is land.
     - If **any** cell in the island of `grid2` corresponds to water (`0`) in `grid1`, mark the island as **not** a sub island.
3. After exploration:
   - If the island is a sub island (all cells in `grid1` are land), increment `count`.
4. Return `count`.

---

### **Java Code:**
```java
public class Solution {
    public int countSubIslands(int[][] grid1, int[][] grid2) {
        int rows = grid2.length;
        int cols = grid2[0].length;
        int count = 0;

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid2[i][j] == 1) {
                    boolean[] isSubIsland = new boolean[]{true};
                    dfs(grid1, grid2, i, j, isSubIsland);
                    if (isSubIsland[0]) {
                        count++;
                    }
                }
            }
        }
        return count;
    }

    private void dfs(int[][] grid1, int[][] grid2, int i, int j, boolean[] isSubIsland) {
        int rows = grid2.length;
        int cols = grid2[0].length;
        if (i < 0 || i >= rows || j < 0 || j >= cols || grid2[i][j] == 0) {
            return;
        }
        if (grid1[i][j] == 0) {
            isSubIsland[0] = false;
        }
        // Mark visited
        grid2[i][j] = 0;
        dfs(grid1, grid2, i + 1, j, isSubIsland);
        dfs(grid1, grid2, i - 1, j, isSubIsland);
        dfs(grid1, grid2, i, j + 1, isSubIsland);
        dfs(grid1, grid2, i, j - 1, isSubIsland);
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  *O(rows * cols)*. Each cell is visited at most once during DFS.

- **Space Complexity:**  
  *O(rows * cols)* for recursion stack in worst case.

---

### **Dry Run:**

Suppose:
```plaintext
grid1 = [
  [1,1,1,0],
  [0,1,1,1],
  [0,0,1,0],
  [1,1,0,0]
]

grid2 = [
  [1,1,1,0],
  [0,0,1,1],
  [0,0,1,0],
  [1,1,0,0]
]
```

- Starting from `(0,0)`, explore the island in `grid2`.
- Check if all corresponding cells in `grid1` are land.
- Count only if entire island in `grid2` is within land in `grid1`.
- Count the total sub islands accordingly.

---

## 2. Approach 2: DFS (Recursive) - Optimized

### **Core Idea:**
Perform DFS on each island in `grid2`. While exploring, verify whether the current island is a sub island by ensuring all land cells are also land in `grid1`. Mark visited cells to avoid revisiting.

---

### **Algorithm:**
- Similar to Approach 1 but emphasizes the recursive DFS process with a clear boolean flag that tracks whether the current island is a sub island.

---

### **Java Code:**
*(Same as approach 1, but focus on the clarity of implementation)*

---

### **Complexity Analysis:**
Same as Approach 1.

---

### **Dry Run:**
Same as above, confirming whether each explored island in `grid2` is a sub island based on the corresponding `grid1` cells.

---

## 3. Approach 3: BFS (Iterative) - Alternative

### **Core Idea:**
Use BFS to explore each island in `grid2`. During traversal, check whether all parts of the island are within land in `grid1`. Count only if the island qualifies.

---

### **Algorithm:**
1. Loop through each cell in `grid2`.
2. When an unvisited land cell is found:
   - Initialize a queue and enqueue the cell.
   - Track whether the island is a sub island.
3. While queue is not empty:
   - Dequeue a cell.
   - Check the corresponding cell in `grid1`.
   - If any cell in `grid2`'s island corresponds to water in `grid1`, mark as not sub.
   - Enqueue all unvisited land neighbors.
4. After BFS completes:
   - If the island is a sub island, increment count.
5. Return total count.

---

### **Java Code:**
```java
import java.util.LinkedList;
import java.util.Queue;

public class Solution {
    public int countSubIslands(int[][] grid1, int[][] grid2) {
        int rows = grid2.length;
        int cols = grid2[0].length;
        boolean[][] visited = new boolean[rows][cols];
        int count = 0;

        int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid2[i][j] == 1 && !visited[i][j]) {
                    boolean isSubIsland = true;
                    Queue<int[]> queue = new LinkedList<>();
                    queue.offer(new int[]{i, j});
                    visited[i][j] = true;

                    while (!queue.isEmpty()) {
                        int[] curr = queue.poll();
                        int x = curr[0], y = curr[1];
                        if (grid1[x][y] == 0) {
                            isSubIsland = false;
                        }
                        for (int[] dir : directions) {
                            int nx = x + dir[0];
                            int ny = y + dir[1];
                            if (nx >= 0 && nx < rows && ny >= 0 && ny < cols && 
                                grid2[nx][ny] == 1 && !visited[nx][ny]) {
                                visited[nx][ny] = true;
                                queue.offer(new int[]{nx, ny});
                            }
                        }
                    }
                    if (isSubIsland) {
                        count++;
                    }
                }
            }
        }
        return count;
    }
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  *O(rows * cols)*, each cell visited at most once.

- **Space Complexity:**  
  *O(rows * cols)* for the `visited` array and queue.

---

# Summary:

| Approach          | Method        | Time Complexity | Space Complexity | Key Points                                              |
|-------------------|---------------|-----------------|------------------|---------------------------------------------------------|
| Naive             | Brute Force   | O(rows * cols) | O(rows * cols)   | Checks each island independently, revisits cells often |
| DFS (Recursive)   | DFS traversal | O(rows * cols) | O(rows * cols)   | Efficient, recursive exploration with sub-island check |
| BFS (Iterative)   | BFS traversal | O(rows * cols) | O(rows * cols)   | Iterative, suitable for large grids with explicit queue |

---

This guide provides a clear understanding of how to approach **Count Sub Islands**, with multiple strategies, their implementation, and efficiency considerations. Use it for quick revision and interview prep!
