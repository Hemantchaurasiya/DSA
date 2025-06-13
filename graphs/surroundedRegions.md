# Surrounded Regions

---

## Problem Overview
Given an `m x n` grid filled with `'X'` and `'O'`, capture all regions surrounded by `'X'`. A region is captured by flipping all `'O'`s in that region to `'X'`s, **only if** the `'O'`s are **completely enclosed** by `'X'`s.  
**Key insight:** `'O'`s connected to the boundary **cannot** be captured because they are not fully surrounded.

---

## Approach 1: Brute Force (Repeatedly Check and Capture)

### **Core Idea**
- Identify `'O'`s that are **not** surrounded (i.e., connected to boundary).
- Convert all `'O'`s **not** connected to boundary to `'X'`.
- Repeat until no changes occur.

### **Algorithm**
1. Traverse all boundary cells:
   - For each `'O'` on boundary, mark it and all `'O'`s connected to it as **safe** (e.g., temporarily mark as `'S'`).
2. After marking, flip all remaining `'O'`s (which are surrounded) to `'X'`.
3. Convert `'S'` back to `'O'`.

### **Java Code**
```java
public void solve(char[][] board) {
    int m = board.length;
    int n = board[0].length;

    // Mark boundary-connected 'O's
    for (int i = 0; i < m; i++) {
        if (board[i][0] == 'O') dfs(board, i, 0);
        if (board[i][n - 1] == 'O') dfs(board, i, n - 1);
    }
    for (int j = 0; j < n; j++) {
        if (board[0][j] == 'O') dfs(board, 0, j);
        if (board[m - 1][j] == 'O') dfs(board, m - 1, j);
    }

    // Flip surrounded 'O's to 'X' and safe 'O's back to 'O'
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i][j] == 'O') {
                board[i][j] = 'X'; // surrounded
            } else if (board[i][j] == 'S') {
                board[i][j] = 'O'; // safe
            }
        }
    }
}

private void dfs(char[][] board, int r, int c) {
    int m = board.length;
    int n = board[0].length;
    if (r < 0 || c < 0 || r >= m || c >= n || board[r][c] != 'O') return;
    board[r][c] = 'S'; // mark as safe
    dfs(board, r - 1, c);
    dfs(board, r + 1, c);
    dfs(board, r, c - 1);
    dfs(board, r, c + 1);
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - O(M*N), as each cell is visited at most once during DFS.
- **Space Complexity:**  
  - O(M*N) for recursion stack in worst case.

---

## Approach 2: BFS for Boundary Connection Marking

### **Core Idea**
- Use BFS instead of DFS to mark `'O'`s connected to the boundary as safe.
- After marking, flip remaining `'O'`s to `'X'`.

### **Algorithm**
1. Traverse boundary cells:
   - For each `'O'`, enqueue and mark as safe.
2. Perform BFS:
   - For each `'O'` dequeued, mark connected `'O'`s as safe.
3. Flip all remaining `'O'`s to `'X'`.
4. Convert safe `'O'`s back to `'O'`.

### **Java Code**
```java
import java.util.LinkedList;
import java.util.Queue;

public void solve(char[][] board) {
    int m = board.length;
    int n = board[0].length;
    Queue<int[]> queue = new LinkedList<>();

    // Enqueue boundary 'O's
    for (int i = 0; i < m; i++) {
        if (board[i][0] == 'O') queue.offer(new int[]{i, 0});
        if (board[i][n - 1] == 'O') queue.offer(new int[]{i, n - 1});
    }
    for (int j = 0; j < n; j++) {
        if (board[0][j] == 'O') queue.offer(new int[]{0, j});
        if (board[m - 1][j] == 'O') queue.offer(new int[]{m - 1, j});
    }

    int[][] directions = {{-1,0},{1,0},{0,-1},{0,1}};

    // BFS to mark boundary-connected 'O's
    while (!queue.isEmpty()) {
        int[] cell = queue.poll();
        int r = cell[0], c = cell[1];
        if (board[r][c] != 'O') continue;
        board[r][c] = 'S'; // mark as safe
        for (int[] dir : directions) {
            int nr = r + dir[0], nc = c + dir[1];
            if (nr >= 0 && nr < m && nc >= 0 && nc < n && board[nr][nc] == 'O') {
                queue.offer(new int[]{nr, nc});
            }
        }
    }

    // Flip surrounded 'O's to 'X' and safe 'O's back to 'O'
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (board[i][j] == 'O') {
                board[i][j] = 'X'; // surrounded
            } else if (board[i][j] == 'S') {
                board[i][j] = 'O'; // safe
            }
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - O(M*N), each cell processed once.
- **Space Complexity:**  
  - O(M*N) for the queue.

---

## Approach 3: In-Place Marking + Final Conversion (Most Optimal)

### **Core Idea**
- Directly mark boundary-connected `'O'`s as safe using DFS/BFS.
- Convert all remaining `'O'`s to `'X'`.
- Convert safe `'O'`s back to `'O'`.

This is essentially the same as above but emphasizes in-place updates for optimal performance.

---

## Dry Run

### Example Input:
```
[
  ['X', 'X', 'X', 'X'],
  ['X', 'O', 'O', 'X'],
  ['X', 'X', 'O', 'X'],
  ['X', 'O', 'X', 'X']
]
```

**Step-by-step:**

- Boundary `'O'`s are at `(3,1)` only.
- Mark `'O'`s connected to `(3,1)` as safe (none connected in this case).
- Remaining `'O'`s in the middle are surrounded.
- Flip `'O'`s in the middle to `'X'`.
- Final grid:
```
[
  ['X', 'X', 'X', 'X'],
  ['X', 'X', 'X', 'X'],
  ['X', 'X', 'X', 'X'],
  ['X', 'O', 'X', 'X']
]
```

---

## Summary Table

| Approach | Key Idea | Pros | Cons | Best Use Case |
|------------|--------------|--------|--------|--------------|
| DFS Marking | Recursive boundary marking | Simple, easy to implement | Stack overflow for large grids | Small to medium grids |
| BFS Marking | Iterative boundary marking | No recursion limit | Slightly more verbose | Large grids |
| In-Place Final Conversion | Combine boundary marking and flipping | Most efficient | Slightly complex | Large inputs, performance-critical |

---

## Final Tips
- Always start marking `'O'`s connected to the boundary.
- Use DFS or BFS depending on grid size and recursion limits.
- After boundary marking, flip all remaining `'O'`s to `'X'`.
- Convert back the safe `'O'`s to `'O'`.

---

Happy Revising!
