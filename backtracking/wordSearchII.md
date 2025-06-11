# Word Search II

---

## Problem Overview
Given a 2D board of characters and a list of words, find **all words** present in the board. Words can be constructed from letters of sequentially adjacent cells (horizontally or vertically). Each cell can be used only once per word.

---

## Approach 1: Brute Force Search

### 1. **Core Idea:**
For each word, scan the entire grid to see if it exists. This involves initiating a DFS from every cell that matches the first character of the word.

---

### 2. **Algorithm:**

- For each word in the list:
  - For each cell in the grid:
    - If the cell matches the first character of the word:
      - Perform DFS to check if the entire word can be constructed starting from this cell.
- Collect all matched words into a result set to avoid duplicates.

---

### 3. **Recursion Tree Diagram:**
```
Start at each cell:
    For each position:
        If match:
            DFS:
                Explore neighbors (up, down, left, right)
                Backtrack if not matching or path invalid
```

*(Visualize as a tree branching for each cell, with depth equal to the length of the word)*

---

### 4. **Java Code:**
```java
public class WordSearchII {
    public List<String> findWords(char[][] board, String[] words) {
        Set<String> result = new HashSet<>();
        for (String word : words) {
            boolean found = false;
            for (int i = 0; i < board.length; i++) {
                for (int j = 0; j < board[0].length; j++) {
                    if (board[i][j] == word.charAt(0) && dfs(board, i, j, word, 0)) {
                        result.add(word);
                        found = true;
                        break;
                    }
                }
                if (found) break;
            }
        }
        return new ArrayList<>(result);
    }

    private boolean dfs(char[][] board, int i, int j, String word, int index) {
        if (index == word.length()) return true;
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || 
            board[i][j] != word.charAt(index))
            return false;

        char temp = board[i][j];
        board[i][j] = '#'; // mark visited

        boolean found = dfs(board, i + 1, j, word, index + 1) ||
                        dfs(board, i - 1, j, word, index + 1) ||
                        dfs(board, i, j + 1, word, index + 1) ||
                        dfs(board, i, j - 1, word, index + 1);

        board[i][j] = temp; // backtrack
        return found;
    }
}
```

---

### 5. **Complexity Analysis:**
- **Time Complexity:**  
  *Worst case:* O(N * M * L * W)  
  where:  
  - N = number of rows  
  - M = number of columns  
  - L = length of each word (average)  
  - W = number of words  
  Because for each word, we perform DFS from each cell (N*M), and each DFS can explore up to 4^L paths.

- **Space Complexity:**  
  O(L) for recursion stack per DFS, negligible additional space.

---

### 6. **Dry Run (Sample Input):**
**Input:**
```plaintext
Board:
[['o','a','a','n'],
 ['e','t','a','e'],
 ['i','h','k','r'],
 ['i','f','l','v']]
Words: ["oath", "pea", "eat", "rain"]
```

**Execution:**
- For "oath":
  - Start from (0,0) 'o':
    - DFS explores neighboring cells to match "a", "t", "h" sequentially.
  - Finds "oath" starting at (0,0).
- For "pea":
  - No starting 'p' in the grid, skip.
- For "eat":
  - Starting at (1,2) 'a' (or (1,1) 't'):
    - Explore neighbors to match "e" and "a".
- For "rain":
  - No matching sequence found.

**Result:** ["oath", "eat"]

---

## Approach 2: Trie + Backtracking (Most Optimal)

### 1. **Core Idea:**
Build a Trie (prefix tree) with all words to efficiently check prefixes during DFS. During traversal, prune paths that do not lead to any word in the list, reducing unnecessary searches.

---

### 2. **Algorithm:**

- Insert all words into a Trie.
- For each cell in the grid:
  - Initiate DFS:
    - Check if current prefix exists in Trie.
    - If a word completes, add to result.
    - Continue exploring neighbors only if the prefix exists.
- Mark visited cells during DFS to prevent reuse.
- Remove matched words from Trie or mark as found to avoid duplicates.

---

### 3. **Trie Structure:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    String word; // Non-null if a word ends here
}
```

### 4. **Java Code:**
```java
public class WordSearchII {
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
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || 
            board[i][j] == '#' || node == null)
            return;

        char c = board[i][j];
        node = node.children[c - 'a'];
        if (node == null) return;

        if (node.word != null) {
            result.add(node.word);
            node.word = null; // avoid duplicate entries
        }

        board[i][j] = '#'; // mark visited

        dfs(board, i+1, j, node, result);
        dfs(board, i-1, j, node, result);
        dfs(board, i, j+1, node, result);
        dfs(board, i, j-1, node, result);

        board[i][j] = c; // backtrack
    }

    private TrieNode buildTrie(String[] words) {
        TrieNode root = new TrieNode();
        for (String word : words) {
            TrieNode curr = root;
            for (char c : word.toCharArray()) {
                if (curr.children[c - 'a'] == null)
                    curr.children[c - 'a'] = new TrieNode();
                curr = curr.children[c - 'a'];
            }
            curr.word = word;
        }
        return root;
    }

    class TrieNode {
        TrieNode[] children = new TrieNode[26];
        String word; // if non-null, word ends here
    }
}
```

---

### 7. **Complexity Analysis:**
- **Time Complexity:**  
  O(N * M * 4^L) in the worst case, but practically much better due to pruning, where:  
  - N = number of rows  
  - M = number of columns  
  - L = maximum length of a word  
  Building the Trie takes O(sum of all word lengths). The DFS is limited by the number of nodes in the Trie and grid size.

- **Space Complexity:**  
  O(sum of all word lengths) for Trie, and O(L) recursion stack during DFS.

---

### 8. **Dry Run (Sample Input):**
Using the same sample input as above:

- Build Trie with ["oath", "pea", "eat", "rain"]
- Start DFS from each cell:
  - From (0,0): 'o' matches root's 'o' branch, explore neighbors for "a", "t", "h".
  - Finds "oath".
  - Continue exploring other cells for remaining words.
- Efficiently prunes paths not leading to any word.

---

## Summary
| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|-------------------|---------------------|--------|
| Brute Force | Check each word independently, starting from every cell | O(W * N * M * L) | O(L) per DFS | Simple but inefficient for large inputs |
| Trie + Backtracking | Use Trie to prune search paths, explore simultaneously | O(N * M * 4^L) on average | O(sum of all word lengths) | Most optimal for large inputs |

---

This guide provides a comprehensive understanding of solving **Word Search II** efficiently, starting from brute-force to optimal Trie-based solutions, with detailed explanations, code, and dry runs for effective interview prep.
