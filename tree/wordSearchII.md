# Word Search II

---

## Problem Overview
Given a 2D board of characters and a list of words, find all words from the list that can be constructed from sequentially adjacent cells (horizontally or vertically). Each cell can be used only once per word.

---

## Approach 1: Brute Force

### 1. Core Idea
For each word, check every cell in the board as a potential starting point and perform a DFS to see if the word can be formed.

### 2. Algorithm
- Loop over each word in the list.
- For each word, iterate through every cell in the board:
  - If the cell matches the first character, perform DFS from that cell to find subsequent characters.
  - Use backtracking to mark visited cells.
- Collect all matched words.

### 3. Java Code
```java
public class WordSearchII {
    private int rows, cols;
    private boolean[][] visited;

    public List<String> findWords(char[][] board, String[] words) {
        List<String> result = new ArrayList<>();
        for (String word : words) {
            boolean found = false;
            for (int i = 0; i < board.length && !found; i++) {
                for (int j = 0; j < board[0].length && !found; j++) {
                    if (board[i][j] == word.charAt(0)) {
                        visited = new boolean[board.length][board[0].length];
                        if (dfs(board, i, j, word, 0)) {
                            result.add(word);
                            found = true;
                        }
                    }
                }
            }
        }
        return result;
    }

    private boolean dfs(char[][] board, int i, int j, String word, int index) {
        if (index == word.length()) return true;
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length) return false;
        if (visited[i][j] || board[i][j] != word.charAt(index)) return false;

        visited[i][j] = true;
        boolean found = dfs(board, i + 1, j, word, index + 1) ||
                        dfs(board, i - 1, j, word, index + 1) ||
                        dfs(board, i, j + 1, word, index + 1) ||
                        dfs(board, i, j - 1, word, index + 1);
        visited[i][j] = false;
        return found;
    }
}
```

### 4. Complexity Analysis
- **Time Complexity:** O(W * R * C * L)  
  For each word (W), we potentially scan the entire board (R * C), and for each cell, perform DFS of length L.
- **Space Complexity:** O(R * C) for the visited array and recursion stack in worst case.

### 5. Dry Run Example

**Input:**
```plaintext
board = [
  ['o','a','a','n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]
words = ["oath", "pea", "eat", "rain"]
```

**Step-by-step:**
- For "oath":
  - Start from cell (0,0): 'o' matches first letter.
  - DFS explores neighbors for 'a', then 't', then 'h'.
  - If found, add "oath" to result.
- For "pea":
  - Check every cell; no match, skip.
- For "eat":
  - Starting at (1,2): 'a' doesn't match 'e', but at (1,3) 'e', then find 'a', 't'.
- For "rain":
  - No path found; ignore.

---

## Approach 2: Using Trie (Prefix Tree) + Backtracking (Most Optimal)

### 1. Core Idea
- **Build a Trie** from all the words for efficient prefix matching.
- Use DFS to explore the board, pruning paths that do not match any word prefix in the Trie.
- When a complete word is found, add it to the result and mark it as found to avoid duplicates.

### 2. Algorithm
- Construct a Trie with all words.
- For each cell in the board:
  - Perform DFS, traversing only paths that are valid prefixes in the Trie.
  - Mark visited cells as you go.
  - When a Trie node indicates the end of a word:
    - Add the word to the result.
    - Mark the word as found to prevent duplicate entries.
- Return all found words.

### 3. Java Code
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    String word = null; // Store the complete word at the end node
}

public class WordSearchII {
    private List<String> result = new ArrayList<>();
    private int rows, cols;

    public List<String> findWords(char[][] board, String[] words) {
        // Build Trie
        TrieNode root = new TrieNode();
        for (String word : words) {
            TrieNode node = root;
            for (char c : word.toCharArray()) {
                if (node.children[c - 'a'] == null) {
                    node.children[c - 'a'] = new TrieNode();
                }
                node = node.children[c - 'a'];
            }
            node.word = word; // Mark end of a word
        }

        rows = board.length;
        cols = board[0].length;

        // Start DFS from each cell
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                dfs(board, i, j, root);
            }
        }

        return result;
    }

    private void dfs(char[][] board, int i, int j, TrieNode node) {
        if (i < 0 || j < 0 || i >= rows || j >= cols) return;
        char c = board[i][j];
        if (c == '#' || node.children[c - 'a'] == null) return;

        node = node.children[c - 'a'];
        if (node.word != null) {
            result.add(node.word);
            node.word = null; // Avoid duplicate entries
        }

        char temp = board[i][j];
        board[i][j] = '#'; // Mark as visited

        dfs(board, i + 1, j, node);
        dfs(board, i - 1, j, node);
        dfs(board, i, j + 1, node);
        dfs(board, i, j - 1, node);

        board[i][j] = temp; // Backtrack
    }
}
```

### 4. Complexity Analysis
- **Time Complexity:** O(R * C * 4 * L)  
  Each cell leads to DFS exploring at most 4 directions, and each step is pruned by the Trie structure. Overall, this is efficient due to pruning.
- **Space Complexity:** O(N * L) for Trie storage (N = total number of words, L = max word length) and recursion stack.

### 5. Dry Run Example

Using the same input as above:

- Build Trie with words:
  - "oath"
  - "eat"
  - "rain"
- Start DFS from each cell:
  - From (0,0): 'o' → matches Trie root, explore neighbors:
    - 'a' → continues path for "oath".
    - Found "oath", add to results.
  - From (1,2): 'a' → leads to "eat".
  - No match for "pea" or "rain" from starting points, except for "rain" from (2,2) if it exists.
- Collect all found words efficiently without redundant searches.

---

## Summary
| Approach | Core Idea | Algorithm | Code Snippet | Time Complexity | Space Complexity | Notes |
|------------|--------------|--------------|--------------|------------------|------------------|--------|
| Brute Force | Check each word independently | For each word, perform DFS from each cell | Provided above | O(W * R * C * L) | O(R * C) | Simple but inefficient for large inputs |
| Trie + Backtracking | Use Trie to prune search space | Build Trie, DFS with prefix matching | Provided above | O(R * C * 4 * L) | O(N * L) | Most efficient solution, suitable for large inputs |

---

This guide provides a structured understanding of solving **Word Search II** with increasing optimization, suitable for quick revision and interview prep.
