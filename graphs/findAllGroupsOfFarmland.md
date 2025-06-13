# Find All Groups of Farmland

**Problem Statement:**  
Given a 2D grid representing farmland, where `1` indicates farmland and `0` indicates empty land, identify all distinct groups (connected components) of farmland. Two farmland cells are part of the same group if they are adjacent horizontally or vertically.

---

## 1. Approach 1: Brute Force (Naive)

### **Core Idea:**
Iterate through each cell in the grid. When encountering a farmland cell (`1`) that hasn't been visited, perform a DFS/BFS to mark all connected farmland cells as part of the same group. Count each such traversal as one group.

---

### **Algorithm:**
1. Initialize an empty list to store farmland groups.
2. Create a `visited` matrix to track visited cells.
3. Loop through each cell `(i, j)`:
   - If `grid[i][j] == 1` and not visited:
     - Create a new list to record the current farmland group.
     - Perform DFS/BFS starting from `(i, j)`:
       - Mark all connected farmland cells as visited.
       - Add their coordinates to the current group.
     - Add the current group to the list of farmland groups.
4. Return the list of all farmland groups.

---

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public List<List<int[]>> findFarmland(int[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        boolean[][] visited = new boolean[rows][cols];
        List<List<int[]>> groups = new ArrayList<>();

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 1 && !visited[i][j]) {
                    List<int[]> currentGroup = new ArrayList<>();
                    dfs(grid, i, j, visited, currentGroup);
                    groups.add(currentGroup);
                }
            }
        }
        return groups;
    }

    private void dfs(int[][] grid, int i, int j, boolean[][] visited, List<int[]> group) {
        int rows = grid.length;
        int cols = grid[0].length;
        if (i < 0 || i >= rows || j < 0 || j >= cols || visited[i][j] || grid[i][j] == 0) {
            return;
        }
        visited[i][j] = true;
        group.add(new int[]{i, j});
        dfs(grid, i + 1, j, visited, group);
        dfs(grid, i - 1, j, visited, group);
        dfs(grid, i, j + 1, visited, group);
        dfs(grid, i, j - 1, visited, group);
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  *O(rows * cols)* because each cell is visited at most once during DFS.

- **Space Complexity:**  
  *O(rows * cols)* for the `visited` matrix and recursive call stack in the worst case.

---

### **Dry Run:**

Suppose:
```plaintext
grid = [
  [1, 0, 0, 1],
  [1, 1, 0, 0],
  [0, 0, 1, 1],
  [0, 0, 1, 1]
]
```

- Start at `(0,0)`:
  - Perform DFS, mark connected farmland `(0,0)`, `(1,0)`, `(1,1)`.
  - Save this as group 1.
- Next unvisited farmland at `(0,3)`:
  - DFS marks `(0,3)` only, as it's isolated.
  - Save as group 2.
- Next unvisited farmland at `(2,2)`:
  - DFS marks `(2,2)`, `(2,3)`, `(3,2)`, `(3,3)`.
  - Save as group 3.

---

## 2. Approach 2: BFS (Iterative)

### **Core Idea:**
Use a queue to explore each farmland group iteratively. When an unvisited farmland cell is encountered, enqueue it and explore all connected farmland cells, marking them as visited.

---

### **Algorithm:**
1. Initialize a list for farmland groups.
2. Use a `visited` matrix.
3. Loop through each cell `(i, j)`:
   - If `grid[i][j] == 1` and not visited:
     - Create a new list for the current group.
     - Enqueue `(i, j)` and mark visited.
     - While queue not empty:
       - Dequeue a cell.
       - Add to current group.
       - Enqueue all unvisited connected farmland neighbors.
     - Add current group to the list.
4. Return the list of groups.

---

### **Java Code:**
```java
import java.util.*;

public class Solution {
    public List<List<int[]>> findFarmland(int[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        boolean[][] visited = new boolean[rows][cols];
        List<List<int[]>> groups = new ArrayList<>();
        int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 1 && !visited[i][j]) {
                    List<int[]> currentGroup = new ArrayList<>();
                    Queue<int[]> queue = new LinkedList<>();
                    queue.offer(new int[]{i, j});
                    visited[i][j] = true;

                    while (!queue.isEmpty()) {
                        int[] cell = queue.poll();
                        currentGroup.add(cell);
                        for (int[] dir : directions) {
                            int ni = cell[0] + dir[0];
                            int nj = cell[1] + dir[1];
                            if (ni >= 0 && ni < rows && nj >= 0 && nj < cols
                                && grid[ni][nj] == 1 && !visited[ni][nj]) {
                                visited[ni][nj] = true;
                                queue.offer(new int[]{ni, nj});
                            }
                        }
                    }
                    groups.add(currentGroup);
                }
            }
        }
        return groups;
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  *O(rows * cols)*, each cell is processed once.

- **Space Complexity:**  
  *O(rows * cols)* for the `visited` array and the queue.

---

## 3. Approach 3: Union-Find (Disjoint Set Union - DSU)

### **Core Idea:**
Use a Union-Find data structure to merge adjacent farmland cells into connected components. After processing, extract all disjoint sets representing farmland groups.

---

### **Algorithm:**
1. Initialize Union-Find structure for all cells.
2. Loop through each cell:
   - If cell is farmland (`1`):
     - For each neighbor (right and down to avoid duplication):
       - If neighbor is farmland, union their sets.
3. After union operations, iterate through all cells:
   - For each cell in a set, record its coordinates.
   - Collect sets with more than one cell as farmland groups.
4. Return the list of all farmland groups.

---

### **Java Code:**

*(Implementing Union-Find is more involved; here's a simplified version outline)*

```java
class UnionFind {
    int[] parent;
    int[] rank;

    public UnionFind(int size) {
        parent = new int[size];
        rank = new int[size];
        for (int i=0; i<size; i++) {
            parent[i] = i;
            rank[i] = 0;
        }
    }

    public int find(int x) {
        if (parent[x] != x) {
            parent[x] = find(parent[x]);
        }
        return parent[x];
    }

    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] < rank[rootY]) {
                parent[rootX] = rootY;
            } else if (rank[rootX] > rank[rootY]) {
                parent[rootY] = rootX;
            } else {
                parent[rootY] = rootX;
                rank[rootX]++;
            }
        }
    }
}

public class Solution {
    public List<List<int[]>> findFarmland(int[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;
        UnionFind uf = new UnionFind(rows * cols);

        for (int i=0; i<rows; i++) {
            for (int j=0; j<cols; j++) {
                if (grid[i][j] == 1) {
                    int currentId = i * cols + j;
                    // Check right neighbor
                    if (j + 1 < cols && grid[i][j+1] == 1) {
                        uf.union(currentId, i * cols + j + 1);
                    }
                    // Check down neighbor
                    if (i + 1 < rows && grid[i+1][j] == 1) {
                        uf.union(currentId, (i+1) * cols + j);
                    }
                }
            }
        }

        Map<Integer, List<int[]>> groupsMap = new HashMap<>();
        for (int i=0; i<rows; i++) {
            for (int j=0; j<cols; j++) {
                if (grid[i][j] == 1) {
                    int rootId = uf.find(i * cols + j);
                    groupsMap.putIfAbsent(rootId, new ArrayList<>());
                    groupsMap.get(rootId).add(new int[]{i, j});
                }
            }
        }

        return new ArrayList<>(groupsMap.values());
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  *O(rows * cols)* for union operations and iterations.

- **Space Complexity:**  
  *O(rows * cols)* for Union-Find data structures and storage of groups.

---

# Summary:

| Approach             | Method              | Time Complexity | Space Complexity | Key Points                                               |
|----------------------|---------------------|-----------------|------------------|----------------------------------------------------------|
| Naive                | DFS/BFS on each cell | O(rows * cols) | O(rows * cols)   | Simple, direct exploration with visited array          |
| BFS (Iterative)      | BFS traversal       | O(rows * cols) | O(rows * cols)   | Uses queue for iterative search                          |
| Union-Find (DSU)     | Disjoint set union   | O(rows * cols) | O(rows * cols)   | Efficient for large grids, merges connected components |

---

This comprehensive guide provides multiple approaches to **Find All Groups of Farmland**, along with their implementations, complexities, and dry runs for better understanding. Use this for quick revision and preparing for technical interviews!
