# Knight's Tour Problem

The **Knight's Tour** problem involves moving a knight on an \( N \times N \) chessboard so that it visits every cell exactly once. The goal is to find a sequence of moves that covers all squares.

---

## 1. Approach: Brute Force (Backtracking)

### **Core Idea:**
Try all possible moves from the current position, mark cells as visited, and backtrack when no further moves are possible. The process continues until all cells are visited.

### **Algorithm:**
1. Start from a given cell (e.g., (0, 0)).
2. Mark the current cell as visited.
3. For each of the 8 possible knight moves:
   - Check if the move is valid (inside board, not visited).
   - Recursively attempt to visit the next cell.
4. If all cells are visited, return success.
5. Backtrack: unmark the current cell and try other moves if dead-end reached.
6. Continue until a complete tour is found or all options exhausted.

---

### **Recursion Tree Diagram:**

```plaintext
Start (0,0)
├── Move 1
│   ├── Move 1.1
│   │   ├── ... (recursive calls)
│   │   └── backtrack
│   ├── Move 1.2
│   │   └── ...
│   └── backtracking...
├── Move 2
│   ├── ...
│   └── backtracking...
└── No solution
```

*(Note: The recursion tree expands exponentially, illustrating the exhaustive search.)*

---

### **Java Code:**

```java
public class KnightTour {
    private static final int[] rowMoves = {-2, -1, 1, 2, 2, 1, -1, -2};
    private static final int[] colMoves = {1, 2, 2, 1, -1, -2, -2, -1};
    private int N;
    private int[][] board;

    public KnightTour(int size) {
        this.N = size;
        this.board = new int[N][N];
    }

    public boolean solveKnightTour() {
        // Initialize board with -1 indicating unvisited
        for (int[] row : board) {
            java.util.Arrays.fill(row, -1);
        }

        // Starting position
        board[0][0] = 0;

        if (solveUtil(0, 0, 1)) {
            printBoard();
            return true;
        }
        return false;
    }

    private boolean solveUtil(int x, int y, int moveCount) {
        if (moveCount == N * N) {
            return true; // All cells visited
        }

        for (int i = 0; i < 8; i++) {
            int nextX = x + rowMoves[i];
            int nextY = y + colMoves[i];

            if (isValid(nextX, nextY)) {
                board[nextX][nextY] = moveCount;
                if (solveUtil(nextX, nextY, moveCount + 1))
                    return true;
                // Backtracking
                board[nextX][nextY] = -1;
            }
        }
        return false;
    }

    private boolean isValid(int x, int y) {
        return (x >= 0 && y >= 0 && x < N && y < N && board[x][y] == -1);
    }

    private void printBoard() {
        for (int[] row : board) {
            for (int cell : row)
                System.out.print(cell + "\t");
            System.out.println();
        }
    }

    public static void main(String[] args) {
        KnightTour kt = new KnightTour(8);
        kt.solveKnightTour();
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  \( O(8^{N^2}) \) in the worst case, since at each step, up to 8 moves are possible, and we attempt to explore all configurations.  
  *Note:* This is exponential, making brute-force impractical for large N.

- **Space Complexity:**  
  \( O(N^2) \) for the board and recursion stack, due to the depth of recursion being \( N^2 \).

---

### **Dry Run:**
Let's consider a small \( 5 \times 5 \) board:

- Start at (0, 0), mark visited (0).
- Explore moves:
  - First move: (2, 1), mark visited (1).
  - Next moves from (2, 1):
    - (4, 2), mark visited (2).
    - Continue recursively until either:
      - All 25 cells are visited (success).
      - Dead-end reached, backtrack to previous move.

*(In practice, this process can involve a large number of recursive calls.)*

---

## 2. Approach: Warnsdorff's Heuristic (Optimized Solution)

### **Core Idea:**
Always move to the square with the fewest onward moves (least degree), reducing dead-ends and improving efficiency. This heuristic guides the search toward a solution faster.

### **Algorithm:**
1. Start from a given position.
2. At each step:
   - Generate all possible moves.
   - For each move, count the number of onward moves (degree).
   - Choose the move with the minimum degree (heuristic).
3. Mark the move as visited.
4. Continue until all cells are visited or no moves remain.
5. Backtrack if dead-end encountered.

### **Recursion Tree Diagram:**

```plaintext
Start (0,0)
├── Select move with least onward moves
│   ├── Move 1
│   │   ├── Next move with least degree
│   │   └── ...
│   ├── Move 2
│   │   └── ...
│   └── Dead-end -> backtrack
```

*(This approach greatly prunes the search space.)*

---

### **Java Code:**

```java
import java.util.*;

public class KnightTourWarnsdorff {
    private static final int[] rowMoves = {-2, -1, 1, 2, 2, 1, -1, -2};
    private static final int[] colMoves = {1, 2, 2, 1, -1, -2, -2, -1};
    private int N;
    private int[][] board;

    public KnightTourWarnsdorff(int size) {
        this.N = size;
        this.board = new int[N][N];
    }

    public boolean solveKnightTour() {
        for (int[] row : board) {
            Arrays.fill(row, -1);
        }
        board[0][0] = 0; // starting point

        if (solveUtil(0, 0, 1))
            return true;
        return false;
    }

    private boolean solveUtil(int x, int y, int moveCount) {
        if (moveCount == N * N)
            return true;

        List<MoveOption> options = new ArrayList<>();
        for (int i = 0; i < 8; i++) {
            int nextX = x + rowMoves[i];
            int nextY = y + colMoves[i];
            if (isValid(nextX, nextY)) {
                int degree = countOnwardMoves(nextX, nextY);
                options.add(new MoveOption(nextX, nextY, degree));
            }
        }

        // Sort moves based on degree (Warnsdorff's heuristic)
        options.sort(Comparator.comparingInt(o -> o.degree));

        for (MoveOption option : options) {
            board[option.x][option.y] = moveCount;
            if (solveUtil(option.x, option.y, moveCount + 1))
                return true;
            // Backtrack
            board[option.x][option.y] = -1;
        }
        return false;
    }

    private int countOnwardMoves(int x, int y) {
        int count = 0;
        for (int i = 0; i < 8; i++) {
            int nx = x + rowMoves[i];
            int ny = y + colMoves[i];
            if (isValid(nx, ny))
                count++;
        }
        return count;
    }

    private boolean isValid(int x, int y) {
        return (x >= 0 && y >= 0 && x < N && y < N && board[x][y] == -1);
    }

    private static class MoveOption {
        int x, y, degree;
        MoveOption(int x, int y, int degree) {
            this.x = x;
            this.y = y;
            this.degree = degree;
        }
    }

    public void printBoard() {
        for (int[] row : board) {
            for (int cell : row)
                System.out.print(cell + "\t");
            System.out.println();
        }
    }

    public static void main(String[] args) {
        KnightTourWarnsdorff kt = new KnightTourWarnsdorff(8);
        if (kt.solveKnightTour())
            kt.printBoard();
        else
            System.out.println("No solution found");
    }
}
```

---

### **Complexity Analysis:**

- **Time Complexity:**  
  Significantly reduces search space; in practice, the heuristic enables solutions in reasonable time for larger boards. Worst-case remains exponential but practically efficient.

- **Space Complexity:**  
  \( O(N^2) \) for the board and recursion stack.

---

### **Dry Run:**
Suppose starting at (0,0):

- From (0,0), possible moves are evaluated.
- Count onward moves for each candidate:
  - For example, move to (2,1): onward moves from (2,1) are counted.
- Select move with minimum onward moves (e.g., (2,1) if it has fewer onward options).
- Mark move, proceed recursively.
- Continue until the entire board is covered or no moves are left.

---

## **Summary**

| Approach | Core Idea | Complexity | Notes |
|------------|--------------|--------------|--------|
| Brute Force | Explore all paths via backtracking | Exponential \(O(8^{N^2})\) | Simple but slow |
| Warnsdorff's Heuristic | Greedy approach based on move degrees | Practical, often efficient | Fast and effective for large boards |

---

## **Key Takeaways for Interviews:**

- Understand the recursive backtracking approach thoroughly.
- Know the limitations of brute-force solutions.
- Recognize the importance of heuristics like Warnsdorff's for optimization.
- Be ready to discuss time and space complexities.
- Be able to implement and optimize the solution for different constraints.

---

**Happy Revision!**
