# Word Search (DFS)

This note provides a comprehensive overview of solving the **Word Search** problem using DFS, covering various approaches from brute-force to optimized solutions, suitable for interview prep and quick revision.

---

## Problem Statement

Given a 2D grid of characters and a word, determine if the word exists in the grid. The word can be constructed from letters of sequentially adjacent cells (horizontally or vertically). The same letter cell may not be used more than once.

---

## 1. Brute Force Approach

### **Algorithm**

- Iterate through each cell in the grid.
- For each cell, initiate a DFS to check if the word can be formed starting from that cell.
- During DFS:
    - Check if the current cell's character matches the current character in the word.
    - Mark the current cell as visited.
    - Recursively explore all four adjacent cells (up, down, left, right).
    - If at any point, the entire word is matched, return true.
    - Backtrack by unmarking the visited cell to explore other paths.
- If no path matches the entire word, return false.

### **Java Code**

```java
public class WordSearch {
    public boolean exist(char[][] board, String word) {
        int rows = board.length;
        int cols = board[0].length;
        boolean[][] visited = new boolean[rows][cols];

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (dfs(board, word, i, j, 0, visited))
                    return true;
            }
        }
        return false;
    }

    private boolean dfs(char[][] board, String word, int i, int j, int index, boolean[][] visited) {
        if (index == word.length()) return true; // All characters matched
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return false;
        if (visited[i][j] || board[i][j] != word.charAt(index)) return false;

        visited[i][j] = true; // Mark current cell as visited
        // Explore all four directions
        boolean found = dfs(board, word, i + 1, j, index + 1, visited) ||
                        dfs(board, word, i - 1, j, index + 1, visited) ||
                        dfs(board, word, i, j + 1, index + 1, visited) ||
                        dfs(board, word, i, j - 1, index + 1, visited);
        visited[i][j] = false; // Backtrack
        return found;
    }
}

```

### **Complexity Analysis**

- **Time Complexity:** O(M * N * 4^L)
    - M = number of rows, N = number of columns, L = length of the word.
    - For each cell, in the worst case, we explore up to 4 directions for each character.
- **Space Complexity:** O(L) due to recursion stack and O(M*N) for the visited array.

---

## 2. Improved Approach (Using In-Place Marking)

### **Algorithm**

- Similar to brute-force, but instead of a separate visited array:
    - Mark visited cells by changing their character temporarily.
    - After exploring, revert the character back (backtracking).
- This reduces auxiliary space.

### **Java Code**

```java
public class WordSearch {
    public boolean exist(char[][] board, String word) {
        int rows = board.length;
        int cols = board[0].length;

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (dfs(board, word, i, j, 0))
                    return true;
            }
        }
        return false;
    }

    private boolean dfs(char[][] board, String word, int i, int j, int index) {
        if (index == word.length()) return true;
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return false;
        if (board[i][j] != word.charAt(index)) return false;

        char temp = board[i][j]; // Save current character
        board[i][j] = '#'; // Mark as visited

        boolean found = dfs(board, word, i + 1, j, index + 1) ||
                        dfs(board, word, i - 1, j, index + 1) ||
                        dfs(board, word, i, j + 1, index + 1) ||
                        dfs(board, word, i, j - 1, index + 1);

        board[i][j] = temp; // Restore original character
        return found;
    }
}

```

### **Complexity Analysis**

- Same as brute force, but with reduced auxiliary space (O(1)), aside from recursion stack.

---

## 3. Most Optimal Solution (Using Trie + DFS)

### **Algorithm**

- Build a Trie (Prefix Tree) from the list of words (if multiple words are given).
- For the current grid, run DFS:
    - Check if the current cell's character is a child of the current Trie node.
    - If it is, move to that node.
    - If the node marks the end of a word, add it to the result.
    - Continue exploring adjacent cells.
- This approach is particularly useful when searching for multiple words simultaneously, reducing repeated searches.

**Note:** For the single word case, building a Trie may not provide significant benefits, but it's essential for multiple words.

### **Java Code (Single Word Version)**

```java
public class WordSearch {
    public boolean exist(char[][] board, String word) {
        int rows = board.length, cols = board[0].length;
        boolean[][] visited = new boolean[rows][cols];

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (dfs(board, word, i, j, 0, visited))
                    return true;
            }
        }
        return false;
    }

    private boolean dfs(char[][] board, String word, int i, int j, int index, boolean[][] visited) {
        if (index == word.length()) return true;
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return false;
        if (visited[i][j] || board[i][j] != word.charAt(index)) return false;

        visited[i][j] = true;
        boolean found = dfs(board, word, i + 1, j, index + 1, visited) ||
                        dfs(board, word, i - 1, j, index + 1, visited) ||
                        dfs(board, word, i, j + 1, index + 1, visited) ||
                        dfs(board, word, i, j - 1, index + 1, visited);
        visited[i][j] = false; // Backtrack
        return found;
    }
}

```

### **Note:**

- For multiple words, implement Trie construction and modify DFS to prune paths early when no words match.

### **Complexity Analysis**

- Same as brute-force, but more efficient when searching for multiple words.

---

## **Dry Run Example**

### Input:

```
board = [
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
word = "ABCCED"

```

### Execution:

| Step | i | j | Current Char | Match with Word | Path Explanation | Visited State (diagram) |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 0 | 'A' | Yes | Start DFS here | (0,0): 'A' visited |
| 2 | 1 | 0 | 'S' | No ('S' != 'B') | Backtrack |  |
| 3 | 0 | 1 | 'B' | Yes | Explore right | (0,1): 'B' visited |
| 4 | 0 | 2 | 'C' | Yes | Explore right | (0,2): 'C' visited |
| 5 | 1 | 2 | 'C' | Yes | Explore down | (1,2): 'C' visited |
| 6 | 2 | 2 | 'E' | Yes | Explore down | (2,2): 'E' visited |
| 7 | 2 | 1 | 'D' | Yes | Explore left | (2,1): 'D' visited |
| 8 | 2 | 0 | 'A' | No ('A' != 'E') | Backtrack |  |
| ... |  |  |  |  | Continue exploring |  |

The process continues until the entire word is matched or all paths are exhausted.

---

## **Summary**

| Approach | Key Points | Advantages | Disadvantages |
| --- | --- | --- | --- |
| Brute Force | DFS from each cell, visited array | Simple, straightforward | High time complexity (O(M*N*4^L)) |
| In-Place Marking | DFS with character modification | Saves auxiliary space | Same time complexity, risk of modifying input if not careful |
| Trie + DFS | Build Trie, prune paths early | Efficient for multiple words | Additional data structure complexity |

---

## Final Tips

- Always mark cells as visited to avoid reuse.
- Use backtracking effectively to explore all possible paths.
- For multiple word searches, prefer Trie to optimize performance.
- Dry run with small examples to understand recursion flow.

---

**End of Revision Note**
