# Flood Fill

---

## Problem Statement

Given an image represented by a 2D array `image`, a starting pixel `(sr, sc)`, and a new color `newColor`, **Flood Fill** changes the color of the starting pixel and all of its connected pixels (connected 4-directionally) with the same initial color to the new color.

---

## Approaches to Solve the Problem

---

### 1. Brute Force (Recursive DFS with visited array)

---

### **Algorithm:**

- Use a recursive DFS starting from `(sr, sc)`.
- Store the initial color of the starting pixel.
- For each pixel:
    - If it is within bounds, has the same color as the initial color, and is not visited:
        - Change its color to `newColor`.
        - Recursively process its 4 neighbors (up, down, left, right).
- To avoid revisiting pixels, maintain a `visited` array or check if the pixel's color has already been changed to `newColor`.

### **Java Code:**

```java
public class FloodFill {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int initialColor = image[sr][sc];
        if (initialColor == newColor) return image; // To avoid infinite loop
        boolean[][] visited = new boolean[image.length][image[0].length];
        dfs(image, sr, sc, initialColor, newColor, visited);
        return image;
    }

    private void dfs(int[][] image, int r, int c, int initialColor, int newColor, boolean[][] visited) {
        if (r < 0 || r >= image.length || c < 0 || c >= image[0].length) return;
        if (visited[r][c] || image[r][c] != initialColor) return;

        visited[r][c] = true;
        image[r][c] = newColor;

        dfs(image, r + 1, c, initialColor, newColor, visited);
        dfs(image, r - 1, c, initialColor, newColor, visited);
        dfs(image, r, c + 1, initialColor, newColor, visited);
        dfs(image, r, c - 1, initialColor, newColor, visited);
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(M * N)` where `M` and `N` are the dimensions of the image. Each pixel is visited at most once.
- **Space Complexity:** `O(M * N)` for the recursion stack and the `visited` array in the worst case.

---

### 2. Better Solution: Recursive DFS Without Extra Visited Array

---

### **Algorithm:**

- Similar to above, but instead of a separate `visited` array, check whether the pixel's color has already been changed to `newColor`.
- Before starting, check if `initialColor == newColor`, return early to avoid infinite recursion.
- Recursively fill neighboring pixels if they match the initial color.

### **Java Code:**

```java
public class FloodFill {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int initialColor = image[sr][sc];
        if (initialColor == newColor) return image; // No change needed
        dfs(image, sr, sc, initialColor, newColor);
        return image;
    }

    private void dfs(int[][] image, int r, int c, int initialColor, int newColor) {
        if (r < 0 || r >= image.length || c < 0 || c >= image[0].length) return;
        if (image[r][c] != initialColor) return;

        image[r][c] = newColor;

        dfs(image, r + 1, c, initialColor, newColor);
        dfs(image, r - 1, c, initialColor, newColor);
        dfs(image, r, c + 1, initialColor, newColor);
        dfs(image, r, c - 1, initialColor, newColor);
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(M * N)` as each pixel is visited once.
- **Space Complexity:** `O(M * N)` due to recursion stack in the worst case.

---

### 3. Optimized Approach: Iterative BFS (Using Queue)

---

### **Algorithm:**

- Use a queue to perform Breadth-First Search (BFS).
- Initialize the queue with the starting pixel.
- While the queue is not empty:
    - Dequeue a pixel.
    - For each neighbor (up, down, left, right):
        - If it matches the initial color and is not already changed:
            - Change the color.
            - Enqueue the neighbor.
- This approach avoids recursion stack overflow issues and is often preferred for large images.

### **Java Code:**

```java
import java.util.LinkedList;
import java.util.Queue;

public class FloodFill {
    public int[][] floodFill(int[][] image, int sr, int sc, int newColor) {
        int initialColor = image[sr][sc];
        if (initialColor == newColor) return image;

        int rows = image.length;
        int cols = image[0].length;
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{sr, sc});
        image[sr][sc] = newColor;

        int[][] directions = {{1,0},{-1,0},{0,1},{0,-1}};

        while (!queue.isEmpty()) {
            int[] point = queue.poll();
            for (int[] dir : directions) {
                int r = point[0] + dir[0];
                int c = point[1] + dir[1];
                if (r >= 0 && r < rows && c >= 0 && c < cols && image[r][c] == initialColor) {
                    image[r][c] = newColor;
                    queue.offer(new int[]{r, c});
                }
            }
        }
        return image;
    }
}

```

### **Complexity Analysis:**

- **Time Complexity:** `O(M * N)` since each pixel is processed at most once.
- **Space Complexity:** `O(M * N)` for the queue in the worst case.

---

## Summary Table

| Approach | Method | Time Complexity | Space Complexity | Pros | Cons |
| --- | --- | --- | --- | --- | --- |
| Brute Force Recursive DFS with visited array | DFS with visited array | `O(M*N)` | `O(M*N)` | Simple implementation | Extra space for `visited` array |
| Recursive DFS without visited array | DFS with in-place color change | `O(M*N)` | `O(M*N)` (recursion stack) | Less memory overhead | Risk of stack overflow for large images |
| Iterative BFS | BFS using queue | `O(M*N)` | `O(M*N)` | No recursion stack issues | Slightly more complex code |

---

## Dry Run Example

### Input:

```
image = [
  [1, 1, 1],
  [1, 1, 0],
  [1, 0, 1]
]
sr = 1, sc = 1, newColor = 2

```

### Initial:

- `initialColor = image[1][1] = 1`
- Queue = [(1,1)]
- Change `(1,1)` to `2`

### Step-by-step:

| Step | Queue | Changed Pixels | Description |
| --- | --- | --- | --- |
| 1 | [(1,1)] | (1,1) → 2 | Start from `(1,1)` |
| 2 | [(0,1), (2,1), (1,2), (1,0)] | (0,1), (1,0), (2,1), (1,2) → 2 (if matching initialColor) | Process neighbors: only those with color=1 get changed |

### Final `image`:

```
[
  [1, 2, 1],
  [2, 2, 0],
  [2, 0, 1]
]

```

---

## Key Takeaways

- Always check if the `newColor` is the same as the initial color to avoid infinite recursion.
- Use DFS or BFS depending on the problem constraints and environment.
- Recursive DFS is simple but can cause stack overflow; iterative BFS is more robust for large inputs.
- Time complexity is generally `O(M*N)` because each pixel is visited once.

---

This comprehensive guide should help in understanding, implementing, and optimizing the Flood Fill algorithm efficiently during interviews and revision.
