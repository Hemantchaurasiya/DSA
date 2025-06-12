# Number of Islands

---

## Problem Statement

Given a 2D grid map of `'1'`s (land) and `'0'`s (water), **count the number of islands**. An island is surrounded by water and is formed by connecting adjacent lands **horizontally or vertically**.

---

## Approach 1: Brute Force (Naive DFS or BFS for each land cell)

### 1. **Algorithm**

- Iterate through each cell in the grid.
- When a cell containing `'1'` is found:
    - Initiate a DFS or BFS to traverse all connected `'1'`s (mark visited cells as `'0'` to avoid revisiting).
    - Increment the island count.
- Continue until all cells are processed.

**Intuition:** Every time you find a `'1'`, you explore its entire island to mark it as visited, ensuring each island is counted exactly once.

---

### 2. **Java Code**

```java
public class NumberOfIslands {
    private int rows, cols;
    private int[][] directions = {{1,0}, {-1,0}, {0,1}, {0,-1}};

    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int count = 0;
        rows = grid.length;
        cols = grid[0].length;

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == '1') {
                    dfs(grid, i, j);
                    count++;
                }
            }
        }
        return count;
    }

    private void dfs(char[][] grid, int r, int c) {
        if (r < 0 || c < 0 || r >= rows || c >= cols || grid[r][c] == '0') {
            return;
        }
        grid[r][c] = '0'; // Mark as visited
        for (int[] dir : directions) {
            dfs(grid, r + dir[0], c + dir[1]);
        }
    }
}

```

---

### 3. **Time & Space Complexity**

- **Time Complexity:**
    
    **O(M * N)**, where M is rows and N is columns. Each cell is visited at most once.
    
- **Space Complexity:**
    
    **O(M * N)** in the worst case (recursion stack for DFS), due to the grid and call stack.
    

---

### 4. **Dry Run**

**Input:**

```
grid = [
  ['1', '1', '0', '0', '0'],
  ['1', '1', '0', '0', '0'],
  ['0', '0', '1', '0', '0'],
  ['0', '0', '0', '1', '1']
]

```

**Step-by-step:**

- Start at (0,0): `'1'`
    - DFS explores (0,1), (1,0), (1,1), marking all as `'0'`. Count = 1
- Next unvisited `'1'` found at (2,2): Count = 2
- Next unvisited `'1'` at (3,3): DFS covers (3,3), (3,4). Count = 3

**Result:** 3 islands.

---

## Approach 2: Union-Find (Disjoint Set Union - DSU)

### 1. **Algorithm**

- Initialize a Union-Find data structure for all cells.
- Iterate through each cell:
    - For each `'1'`, check its right and bottom neighbor:
        - If neighbor is `'1'`, union their sets.
- Count how many distinct sets contain `'1'` cells.

**Intuition:** Groups connected `'1'`s into disjoint sets and count the number of distinct sets.

---

### 2. **Java Code**

```java
class UnionFind {
    int[] parent, rank;
    int count;

    public UnionFind(char[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        parent = new int[m * n];
        rank = new int[m * n];
        count = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    int id = i * n + j;
                    parent[id] = id;
                    count++;
                }
            }
        }
    }

    public int find(int i) {
        if (parent[i] != i) {
            parent[i] = find(parent[i]);
        }
        return parent[i];
    }

    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] < rank[rootY]) {
                parent[rootX] = rootY;
            } else if (rank[rootY] < rank[rootX]) {
                parent[rootY] = rootX;
            } else {
                parent[rootY] = rootX;
                rank[rootX]++;
            }
            count--;
        }
    }
}

public class NumberOfIslands {
    public int numIslands(char[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        int m = grid.length, n = grid[0].length;
        UnionFind uf = new UnionFind(grid);
        int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == '1') {
                    for (int[] dir : directions) {
                        int ni = i + dir[0], nj = j + dir[1];
                        if (ni >= 0 && ni < m && nj >= 0 && nj < n && grid[ni][nj] == '1') {
                            uf.union(i * n + j, ni * n + nj);
                        }
                    }
                }
            }
        }

        return uf.count;
    }
}

```

---

### 3. **Complexity Analysis**

- **Time Complexity:**
    
    **O(M * N)** for iterating through each cell and union operations which are almost O(1) amortized due to path compression.
    
- **Space Complexity:**
    
    **O(M * N)** for Union-Find data structures.
    

---

### 4. **Dry Run**

Using the same grid example:

```
grid = [
  ['1', '1', '0', '0', '0'],
  ['1', '1', '0', '0', '0'],
  ['0', '0', '1', '0', '0'],
  ['0', '0', '0', '1', '1']
]

```

- Initialize Union-Find:
    - Count = number of `'1'` cells = 7
- Union adjacent `'1'`s:
    - Union (0,0) with (0,1), (1,0), (1,1)
    - Union (2,2) alone
    - Union (3,3) with (3,4)
- Total distinct sets after unions: 3

---

## Summary

| Approach | Methodology | Best for | Time Complexity | Space Complexity |
| --- | --- | --- | --- | --- |
| Brute Force (DFS/BFS) | Explore each unvisited land and mark visited | Simplicity, small grids | O(M*N) | O(M*N) |
| Union-Find (Disjoint Set) | Connect adjacent land cells using DSU | Large grids, optimized | O(M*N) | O(M*N) |

---

## Tips for Interviews

- Recognize that DFS/BFS is straightforward and intuitive.
- When optimizing, Union-Find can be more efficient, especially for large grids with many connections.
- Always mark visited cells to prevent revisiting.
- Use proper data structures for efficiency (e.g., path compression in Union-Find).

---

**End of Revision Notes**
