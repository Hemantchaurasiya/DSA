# Flood Fill

The **Flood Fill** problem is a classic DFS/BFS problem where you need to change the color of a region in an image (2D grid) starting from a given pixel, and extending to all connected pixels of the same color.

---

## 1. Approach 1: Brute Force (Naive)

### **Core Idea:**
Start from the given pixel, and check all pixels in the grid to find connected pixels of the same initial color, then change their color. This approach essentially involves exploring the entire grid multiple times, making it inefficient.

---

### **Algorithm:**
1. **Identify** the original color at the starting pixel `(sr, sc)`.
2. **Iterate** over all pixels in the grid:
   - For each pixel, if it is **connected** to the starting pixel (via adjacency) and **has the same original color**, change its color.
3. **Repeat** this process until all relevant pixels are updated.

*Note:* This approach is inefficient because it involves scanning the entire grid repeatedly, which is unnecessary.

---

### **Java Code:**
```java
public int[][] floodFillNaive(int[][] image, int sr, int sc, int newColor) {
    int originalColor = image[sr][sc];
    int rows = image.length;
    int cols = image[0].length;
    boolean[][] visited = new boolean[rows][cols];

    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (connectedToStart(image, i, j, sr, sc, originalColor, visited)) {
                image[i][j] = newColor;
            }
        }
    }
    return image;
}

private boolean connectedToStart(int[][] image, int i, int j, int sr, int sc, int color, boolean[][] visited) {
    if (i < 0 || i >= image.length || j < 0 || j >= image[0].length || visited[i][j]) {
        return false;
    }
    if (image[i][j] != color) {
        return false;
    }
    if (i == sr && j == sc) {
        return true;
    }
    visited[i][j] = true;
    // Check neighbors
    return connectedToStart(image, i + 1, j, sr, sc, color, visited) ||
           connectedToStart(image, i - 1, j, sr, sc, color, visited) ||
           connectedToStart(image, i, j + 1, sr, sc, color, visited) ||
           connectedToStart(image, i, j - 1, sr, sc, color, visited);
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  *O((rows * cols)^2)* in the worst case because for each cell, we potentially scan the entire grid multiple times.  
  *This is highly inefficient.*

- **Space Complexity:**  
  *O(rows * cols)* for the `visited` array.

---

### **Dry Run:**

Suppose the grid:
```
image = [
  [1,1,1],
  [1,1,0],
  [1,0,1]
]
sr = 1, sc = 1, newColor = 2
```

- **Original color:** 1
- **Iteration:** For each cell, check if connected to `(1,1)` and has color 1.
- **Result:** Only the connected region of `1`s from `(1,1)` changes to `2`.
- **Issue:** The entire grid is scanned multiple times, leading to inefficiency.

---

## 2. Approach 2: Depth-First Search (DFS) - Recursive

### **Core Idea:**
Use DFS to explore and fill all connected pixels of the same initial color starting from `(sr, sc)`. This approach avoids multiple scans and efficiently explores only relevant pixels.

---

### **Algorithm:**
1. **Check** if the current pixel `(i, j)` is within bounds.
2. **If** the pixel's color matches the original color, change it to `newColor`.
3. **Recursively** call DFS on all four neighbors (up, down, left, right).
4. **Terminate** recursion when neighbors are out of bounds or of different color.

*Important:* Avoid revisiting pixels by changing color or using a visited array.

---

### **Java Code:**
```java
public int[][] floodFillDFS(int[][] image, int sr, int sc, int newColor) {
    int originalColor = image[sr][sc];
    if (originalColor == newColor) return image; // No change needed
    dfs(image, sr, sc, originalColor, newColor);
    return image;
}

private void dfs(int[][] image, int i, int j, int color, int newColor) {
    if (i < 0 || i >= image.length || j < 0 || j >= image[0].length) return;
    if (image[i][j] != color) return;

    image[i][j] = newColor; // Fill current pixel

    // Explore neighbors
    dfs(image, i + 1, j, color, newColor);
    dfs(image, i - 1, j, color, newColor);
    dfs(image, i, j + 1, color, newColor);
    dfs(image, i, j - 1, color, newColor);
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  *O(rows * cols)* in the worst case, since each pixel is visited at most once.

- **Space Complexity:**  
  *O(rows * cols)* for the recursion stack in the worst case (deepest recursion).

---

### **Dry Run:**

Using the same sample:
```
image = [
  [1,1,1],
  [1,1,0],
  [1,0,1]
]
sr=1, sc=1, newColor=2
```
- Start at `(1,1)` with color 1.
- Change `(1,1)` to 2.
- Recursively explore neighbors:
  - `(2,1)` has color 0 → stop.
  - `(0,1)` has color 1 → change to 2, recurse.
  - Continue until all connected 1's are changed to 2.

---

## 3. Approach 3: Breadth-First Search (BFS) - Iterative

### **Core Idea:**
Use a queue to perform BFS starting from `(sr, sc)` to explore all connected pixels of the same initial color. This approach is iterative and suitable for large images to avoid recursion stack overflow.

---

### **Algorithm:**
1. Initialize a queue and enqueue the starting pixel `(sr, sc)`.
2. Store the original color.
3. While the queue is not empty:
   - Dequeue a pixel.
   - Change its color to `newColor`.
   - For each neighbor (up, down, left, right):
     - If within bounds and has the original color, enqueue it.
4. Continue until all connected pixels are processed.

---

### **Java Code:**
```java
import java.util.LinkedList;
import java.util.Queue;

public int[][] floodFillBFS(int[][] image, int sr, int sc, int newColor) {
    int originalColor = image[sr][sc];
    if (originalColor == newColor) return image;

    int rows = image.length;
    int cols = image[0].length;
    Queue<int[]> queue = new LinkedList<>();
    queue.offer(new int[]{sr, sc});

    while (!queue.isEmpty()) {
        int[] curr = queue.poll();
        int i = curr[0], j = curr[1];

        if (i < 0 || i >= rows || j < 0 || j >= cols || image[i][j] != originalColor) continue;

        image[i][j] = newColor;

        // Enqueue neighbors
        queue.offer(new int[]{i + 1, j});
        queue.offer(new int[]{i - 1, j});
        queue.offer(new int[]{i, j + 1});
        queue.offer(new int[]{i, j - 1});
    }

    return image;
}
```

---

### **Complexity Analysis:**
- **Time Complexity:**  
  *O(rows * cols)* as each pixel is processed once.

- **Space Complexity:**  
  *O(rows * cols)* for the queue in the worst case when all pixels are connected.

---

### **Dry Run:**

Using the same sample:
- Enqueue `(1,1)`.
- Dequeue `(1,1)`, change to 2.
- Enqueue neighbors:
  - `(2,1)` (color 0) → no change.
  - `(0,1)` (color 1) → enqueue.
  - `(1,2)` (color 0) → no change.
  - `(1,0)` (color 1) → enqueue.
- Continue until all connected 1's are changed to 2.

---

# Summary:

| Approach             | Method        | Time Complexity | Space Complexity | Key Points                                  |
|----------------------|---------------|-----------------|------------------|--------------------------------------------|
| Brute Force          | Scan entire grid repeatedly | O((rows * cols)^2) | O(rows * cols) | Inefficient, not practical for large grids |
| DFS (Recursive)      | Recursive DFS exploration | O(rows * cols) | O(rows * cols) | Efficient, uses recursion stack           |
| BFS (Iterative)      | BFS with Queue | O(rows * cols) | O(rows * cols) | Iterative, avoids recursion stack        |

---

This comprehensive guide provides a structured approach to solving the **Flood Fill** problem, helping you understand multiple strategies, their implementation, and their efficiency. Use these notes for quick revision before interviews!
