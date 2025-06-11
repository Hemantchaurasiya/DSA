# Word Search

This guide covers various approaches to solving the **Word Search** problem, progressing from brute-force to optimized solutions. It includes detailed explanations, algorithms, code snippets, complexity analysis, and dry runs for quick revision and interview preparation.

---

## Problem Statement
Given a 2D board of characters and a word, determine if the word exists in the grid. The word can be constructed from letters of sequentially adjacent cells, where "adjacent" cells are horizontally or vertically neighboring. The same cell may not be used more than once.

---

## Approach 1: Brute Force (Backtracking)
### Core Idea
Try to find the starting letter of the word in the grid and explore all possible paths recursively to match the entire word.

### Algorithm
1. Iterate through each cell in the grid.
2. If the cell matches the first character of the word, initiate a DFS (Depth First Search) from that cell.
3. In DFS:
   - Check if the current index matches the length of the word (meaning the entire word has been found).
   - Mark the current cell as visited to avoid reuse.
   - Explore all four possible directions (up, down, left, right).
   - If any path matches the remaining substring, return true.
   - Backtrack (unmark visited) after exploring all directions.
4. If no starting point leads to a match, return false.

### Recursion Tree Diagram
```plaintext
Start at cell (i,j)
  - If matches character:
    - Mark visited
    - Explore up
    - Explore down
    - Explore left
    - Explore right
    - Unmark visited
```

### Java Code
```java
public class WordSearch {
    public boolean exist(char[][] board, String word) {
        int m = board.length;
        int n = board[0].length;
        boolean[][] visited = new boolean[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (dfs(board, word, i, j, 0, visited))
                    return true;
            }
        }
        return false;
    }

    private boolean dfs(char[][] board, String word, int i, int j, int idx, boolean[][] visited) {
        if (idx == word.length()) return true;
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length ||
            visited[i][j] || board[i][j] != word.charAt(idx))
            return false;

        visited[i][j] = true;

        boolean found = dfs(board, word, i + 1, j, idx + 1, visited) ||
                        dfs(board, word, i - 1, j, idx + 1, visited) ||
                        dfs(board, word, i, j + 1, idx + 1, visited) ||
                        dfs(board, word, i, j - 1, idx + 1, visited);

        visited[i][j] = false; // backtrack
        return found;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(M * N * 4^L), where M and N are grid dimensions, and L is the length of the word.
  - Each cell can initiate a DFS, and in the worst case, each DFS explores 4 directions with depth L.
- **Space Complexity:** O(M * N) for the `visited` array and recursion stack.

---

## Approach 2: Optimization with Pruning
### Core Idea
Reduce unnecessary searches:
- Check if the frequency of each character in the word is less than or equal to its frequency in the grid.
- Use early pruning when the remaining characters cannot be matched due to character count mismatch.

### Algorithm
- Count frequency of characters in the grid and the word.
- If the grid's character count for any character is less than that in the word, return false immediately.
- Otherwise, proceed with backtracking as in Approach 1.

### Java Code
```java
public class WordSearch {
    public boolean exist(char[][] board, String word) {
        if (!canFormWord(board, word)) return false;

        int m = board.length;
        int n = board[0].length;
        boolean[][] visited = new boolean[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (dfs(board, word, i, j, 0, visited))
                    return true;
            }
        }
        return false;
    }

    private boolean canFormWord(char[][] board, String word) {
        int[] boardFreq = new int[26];
        for (char[] row : board)
            for (char c : row)
                boardFreq[c - 'A']++;
        int[] wordFreq = new int[26];
        for (char c : word.toCharArray())
            wordFreq[c - 'A']++;
        for (int i = 0; i < 26; i++) {
            if (wordFreq[i] > boardFreq[i]) return false;
        }
        return true;
    }

    private boolean dfs(char[][] board, String word, int i, int j, int idx, boolean[][] visited) {
        if (idx == word.length()) return true;
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length ||
            visited[i][j] || board[i][j] != word.charAt(idx))
            return false;

        visited[i][j] = true;

        boolean found = dfs(board, word, i + 1, j, idx + 1, visited) ||
                        dfs(board, word, i - 1, j, idx + 1, visited) ||
                        dfs(board, word, i, j + 1, idx + 1, visited) ||
                        dfs(board, word, i, j - 1, idx + 1, visited);

        visited[i][j] = false; // backtrack
        return found;
    }
}
```

### Complexity Analysis
- **Time Complexity:** Still O(M * N * 4^L), but with better pruning reducing unnecessary searches.
- **Space Complexity:** O(M * N) for visited array and recursion stack.

---

## Approach 3: Trie + Backtracking (Most Optimal)
### Core Idea
Use a Trie data structure to handle multiple words simultaneously and prune the search space efficiently.

### Algorithm
1. Insert all words into a Trie.
2. For each cell in the grid:
   - Start DFS, exploring neighbors.
   - During DFS:
     - Check if current character matches a Trie node's child.
     - If a word is completed at this node, mark it found and remove it from Trie to prevent duplicate searches.
     - Continue exploring neighbors.
   - Prune paths where characters do not match Trie branches.
3. Collect all found words.

### Trie Data Structure
- Each node contains an array or map of children and a boolean or string indicating end of word.

### Java Code
```java
import java.util.*;

public class WordSearch {
    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word = null; // null if not end of word
    }

    private TrieNode buildTrie(String[] words) {
        TrieNode root = new TrieNode();
        for (String w : words) {
            TrieNode node = root;
            for (char c : w.toCharArray()) {
                if (node.children[c - 'a'] == null)
                    node.children[c - 'a'] = new TrieNode();
                node = node.children[c - 'a'];
            }
            node.word = w; // mark end of word
        }
        return root;
    }

    public List<String> findWords(char[][] board, String[] words) {
        List<String> result = new ArrayList<>();
        TrieNode root = buildTrie(words);

        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                dfs(board, i, j, root, result);
            }
        }
        return result;
    }

    private void dfs(char[][] board, int i, int j, TrieNode node, List<String> result) {
        char c = board[i][j];
        if (c == '#' || node.children[c - 'a'] == null) return;

        node = node.children[c - 'a'];
        if (node.word != null) {
            result.add(node.word);
            node.word = null; // avoid duplicates
        }

        char temp = board[i][j];
        board[i][j] = '#'; // mark visited

        int[] dx = {-1, 1, 0, 0};
        int[] dy = {0, 0, -1, 1};
        for (int dir = 0; dir < 4; dir++) {
            int x = i + dx[dir], y = j + dy[dir];
            if (x >= 0 && y >= 0 && x < board.length && y < board[0].length) {
                dfs(board, x, y, node, result);
            }
        }

        board[i][j] = temp; // backtrack
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(M * N * 4 * L), where L is the maximum length of the words, but practically faster due to Trie pruning.
- **Space Complexity:** O(W * L) for Trie construction, where W is number of words.

---

## Dry Run Example
### Input:
```plaintext
board = [
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
word = "ABCCED"
```

### Step-by-step:
| Step | Action | Variables/States |
|--------|---------|------------------|
| 1 | Start at (0,0) with 'A' | match 'A' in word |
| 2 | DFS from (0,0), explore neighbors | Up: invalid, Left: invalid, Right: 'B', Down: 'S' |
| 3 | Move to (0,1), 'B' | match 'B' |
| 4 | Explore neighbors | Right: 'C', Down: 'F' |
| 5 | Move to (0,2), 'C' | match 'C' |
| 6 | Explore neighbors | Right: 'C', Down: 'E' |
| 7 | Move to (1,2), 'C' | match 'C' |
| 8 | Explore neighbors | Down: 'E', Back to (0,2) already visited |
| 9 | Move to (2,2), 'E' | match 'E' |
| 10 | Explore neighbors | Up: 'C', Left: 'D' |
| 11 | Move to (2,1), 'D' | match 'D', complete word |
| **Result**: Found "ABCCED" | |

---

## Summary
| Approach | Best Use Case | Pros | Cons |
|------------|----------------|-------|-------|
| Brute Force | Small grids, single word | Simple implementation | Inefficient for large grids or multiple words |
| Pruning | Slightly larger grids | Slight performance gain | Still exponential in worst case |
| Trie + Backtracking | Multiple words, large dataset | Efficient, scalable | More complex implementation |

---

## Final Tips
- Always check for early pruning conditions.
- Use visited arrays or modify the grid carefully to avoid reusing cells.
- For multiple words, Trie-based solutions are optimal.
- Dry run with sample inputs to understand the flow.

---

This concludes the comprehensive DSA revision note for the **Word Search** problem. Use this guide for quick revision and to prepare effectively for interviews!
