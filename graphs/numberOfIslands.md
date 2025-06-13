# Number of Islands

The **Number of Islands** problem involves counting the number of disconnected land masses (islands) in a 2D grid, where `'1'` represents land and `'0'` represents water. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically.

---

## 1. Approach 1: Brute Force (Naive)

### **Core Idea:**
Scan the entire grid. Every time you encounter an unvisited `'1'`, perform a DFS/BFS to mark all connected `'1'`s as visited, counting one island. Continue scanning until all land cells are visited.

---

### **Algorithm:**
1. Initialize `count = 0`.
2. Iterate over each cell `(i, j)`:
   - If the cell contains `'1'` and is not visited:
     - Increment `count` by 1.
     - Perform a DFS/BFS from `(i, j)` to mark all connected `'1'`s as visited.
3. Return `count`.

*Note:* This approach is straightforward but involves repeated traversals for each unvisited `'1'`.

---

### **Java Code:**
```java
public int numIslandsNaive(char[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    boolean[][] visited = new boolean[rows][cols];
    int count = 0;

    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == '1' && !visited[i][j]) {
                count++;
                dfs(grid, i, j, visited);
            }
        }
    }
    return count;
}

private void dfs(char[][] grid, int i, int j, boolean[][] visited) {
    if (i < 0 || i >= grid.length || j < 0 || j >= grid[0].length || visited[i][j] || grid[i][j] == '0') {
        return;
    }
    visited[i][j] = true;
    dfs(grid, i + 1, j, visited);
    dfs(grid, i - 1, j, visited);
    dfs(grid, i, j + 1, visited);
    dfs(grid, i, j - 1, visited);
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  *O(rows * cols)* in total, since each cell is visited at most once during the DFS calls.

- **Space Complexity:**  
  *O(rows * cols)* for the `visited` array and recursion stack in worst case.

---

### **Dry Run:**

Input:
```
grid = [
  ['1','1','0','0'],
  ['1','0','0','1'],
  ['0','0','1','1'],
  ['0','0','0','0']
]
```
- Start at `(0,0)`, find `'1'`, increment count to 1, DFS to mark all connected `'1'`s.
- Continue scanning; when encountering next unvisited `'1'` at `(1,3)`, increment count to 2, mark connected land.
- Next `'1'` at `(2,2)` found, increment count to 3.
- Final count: **3** islands.

---

## 2. Approach 2: DFS (Recursive) - Optimized

### **Core Idea:**
Use DFS to explore each island starting from every unvisited `'1'`. Mark all connected `'1'`s as visited to avoid recounting.

---

### **Algorithm:**
1. Initialize `count = 0`.
2. Loop through each cell:
   - If cell is `'1'` and not visited:
     - Increment `count`.
     - Call DFS to mark all connected `'1'`s.
3. Return `count`.

*This approach is similar to the brute-force but emphasizes the DFS traversal to avoid unnecessary re-traversals.*

---

### **Java Code:**
```java
public int numIslandsDFS(char[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    boolean[][] visited = new boolean[rows][cols];
    int count = 0;

    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == '1' && !visited[i][j]) {
                count++;
                dfs(grid, i, j, visited);
            }
        }
    }
    return count;
}
```

*(Note: Same as above; the focus is on the DFS method.)*

---

### **Complexity Analysis:**
- **Time Complexity:**  
  *O(rows * cols)*, each cell visited once.

- **Space Complexity:**  
  *O(rows * cols)* for the `visited` array and recursion stack.

---

### **Dry Run:**
Same as the previous example, each distinct island is counted once, and all connected land cells are marked visited during each DFS call.

---

## 3. Approach 3: BFS (Iterative)

### **Core Idea:**
Use a queue to perform BFS from each unvisited `'1'`, marking all connected `'1'`s as visited. This avoids recursion and stack overflow issues, especially for large grids.

---

### **Algorithm:**
1. Initialize `count = 0`.
2. For each cell `(i, j)`:
   - If it is `'1'` and unvisited:
     - Increment `count`.
     - Enqueue `(i, j)`.
     - While queue not empty:
       - Dequeue a cell.
       - Mark visited.
       - Enqueue all unvisited `'1'` neighbors.
3. Return `count`.

---

### **Java Code:**
```java
import java.util.LinkedList;
import java.util.Queue;

public int numIslandsBFS(char[][] grid) {
    int rows = grid.length;
    int cols = grid[0].length;
    boolean[][] visited = new boolean[rows][cols];
    int count = 0;

    int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};

    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == '1' && !visited[i][j]) {
                count++;
                Queue<int[]> queue = new LinkedList<>();
                queue.offer(new int[]{i, j});
                visited[i][j] = true;

                while (!queue.isEmpty()) {
                    int[] curr = queue.poll();
                    for (int[] dir : directions) {
                        int ni = curr[0] + dir[0];
                        int nj = curr[1] + dir[1];
                        if (ni >= 0 && ni < rows && nj >= 0 && nj < cols && 
                            grid[ni][nj] == '1' && !visited[ni][nj]) {
                            visited[ni][nj] = true;
                            queue.offer(new int[]{ni, nj});
                        }
                    }
                }
            }
        }
    }
    return count;
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  *O(rows * cols)*, each cell visited once during BFS.

- **Space Complexity:**  
  *O(rows * cols)* for the `visited` array and queue in the worst case.

---

### **Dry Run:**
Using the previous grid:
- When reaching `(0,0)`:
  - Increment count to 1.
  - BFS to mark `(0,1)`, `(1,0)`.
- Next unvisited `'1'` at `(1,3)`:
  - Increment count to 2.
  - BFS to mark `(2,2)`, `(2,3)`.
- No more unvisited `'1'`.
- Final count: **3**.

---

# Summary:

| Approach             | Method        | Time Complexity | Space Complexity | Key Points                                  |
|----------------------|---------------|-----------------|------------------|--------------------------------------------|
| Naive                | Repeated scan | O(rows * cols) | O(rows * cols) | Inefficient, multiple traversals        |
| DFS (Recursive)      | DFS traversal | O(rows * cols) | O(rows * cols) | Efficient, recursive exploration      |
| BFS (Iterative)      | BFS traversal | O(rows * cols) | O(rows * cols) | Iterative, suitable for large grids   |

---

This structured revision provides clarity on solving the **Number of Islands** problem, along with multiple approaches, their implementation, and efficiency considerations. Use this as a quick reference for interview prep!
