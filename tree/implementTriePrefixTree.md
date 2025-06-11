# Implement Trie (Prefix Tree)

This guide provides a comprehensive overview of implementing a Trie (Prefix Tree), starting from basic approaches to the most efficient solutions. It covers the core ideas, algorithms, code snippets, complexity analysis, and dry runs to facilitate quick revision and interview preparation.

---

## 1. Brute Force Approach

### **Core Idea:**
For each word, insert it into a data structure (like a list). To search for a prefix, check each word in the list for a matching prefix.

### **Algorithm:**
1. **Insert:** Append each word to a list.
2. **Search Prefix:** For each word, iterate through the list and check if any word starts with the given prefix.
3. **Check for Word:** To verify if a word exists, scan the list for the exact match.

### **Java Code:**
```java
class Trie {
    List<String> words;

    public Trie() {
        words = new ArrayList<>();
    }

    public void insert(String word) {
        words.add(word);
    }

    public boolean search(String word) {
        for (String w : words) {
            if (w.equals(word))
                return true;
        }
        return false;
    }

    public boolean startsWith(String prefix) {
        for (String w : words) {
            if (w.startsWith(prefix))
                return true;
        }
        return false;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**
  - Insert: O(1) per insertion (amortized)
  - Search / StartsWith: O(N * L), where N is number of words, L is length of the word/prefix
- **Space Complexity:** O(N * L) for storing all words

### **Dry Run:**
- Input: `["apple", "app", "application"]`
- Insert all words into list.
- Search "app" → Check each word:
  - "apple" starts with "app" → true
  - "app" matches exactly → true
  - "application" starts with "app" → true

*Limitations:* Slow for large datasets and prefix searches, as it involves linear scans.

---

## 2. Better Solution: Using HashMaps (Dictionary-based Trie)

### **Core Idea:**
Construct a Trie using nested HashMaps, where each node is a map from characters to child nodes, plus an indicator if it is the end of a word.

### **Algorithm:**
1. **Node Structure:**
   - Use a `HashMap<Character, Node>` for children.
   - Use a boolean `isWordEnd` to mark the end of a word.
2. **Insert:**
   - For each character in the word:
     - If not present, create a new node.
     - Move to the child node.
   - Mark the last node as `isWordEnd = true`.
3. **Search & StartsWith:**
   - Traverse the trie following characters.
   - For `search`: ensure `isWordEnd` is true at the last node.
   - For `startsWith`: only ensure the path exists.

### **Java Code:**
```java
class TrieNode {
    Map<Character, TrieNode> children = new HashMap<>();
    boolean isWordEnd = false;
}

class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    public void insert(String word) {
        TrieNode current = root;
        for (char c : word.toCharArray()) {
            current = current.children.computeIfAbsent(c, k -> new TrieNode());
        }
        current.isWordEnd = true;
    }

    public boolean search(String word) {
        TrieNode node = searchNode(word);
        return node != null && node.isWordEnd;
    }

    public boolean startsWith(String prefix) {
        return searchNode(prefix) != null;
    }

    private TrieNode searchNode(String str) {
        TrieNode current = root;
        for (char c : str.toCharArray()) {
            if (!current.children.containsKey(c))
                return null;
            current = current.children.get(c);
        }
        return current;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**
  - Insert: O(L), where L is length of the word
  - Search / StartsWith: O(L)
- **Space Complexity:** O(N * L), for storing all nodes

### **Dry Run:**
- Input: `["apple", "app", "application"]`
- Insert "apple":
  - root → 'a' → 'p' → 'p' → 'l' → 'e' (mark last node as word end)
- Insert "app":
  - Reuse path to 'a' → 'p' → 'p' and mark last node as word end
- Insert "application":
  - Reuse path to 'a' → 'p' → 'p' → 'l' → 'i' → ... and mark last as word end

Searching "app":
- Follow 'a' → 'p' → 'p' → node found, check `isWordEnd` → true

Searching "ap":
- Follow 'a' → 'p' → node missing 'p' after 'a' → false

---

## 3. Most Optimal Solution: Trie (Prefix Tree)

### **Core Idea:**
Use a dedicated Trie data structure with nodes representing characters, enabling efficient prefix and word searches.

### **Algorithm:**
- Build a Trie with nodes containing:
  - An array of fixed size (e.g., 26 for lowercase letters) for children for faster access.
  - A boolean to mark word end.
- **Insert:** For each character, move to the corresponding child, creating nodes if absent.
- **Search:** Traverse by characters, check for existence, and confirm `isWordEnd`.
- **StartsWith:** Traverse by characters, just confirm path existence.

### **Java Code:**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isWordEnd = false;
}

class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    public void insert(String word) {
        TrieNode current = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null)
                current.children[index] = new TrieNode();
            current = current.children[index];
        }
        current.isWordEnd = true;
    }

    public boolean search(String word) {
        TrieNode node = searchNode(word);
        return node != null && node.isWordEnd;
    }

    public boolean startsWith(String prefix) {
        return searchNode(prefix) != null;
    }

    private TrieNode searchNode(String str) {
        TrieNode current = root;
        for (char c : str.toCharArray()) {
            int index = c - 'a';
            if (current.children[index] == null)
                return null;
            current = current.children[index];
        }
        return current;
    }
}
```

### **Complexity Analysis:**
- **Time Complexity:**
  - Insert/Search/StartsWith: O(L), L = length of the word/prefix
- **Space Complexity:**
  - O(N * L) in the worst case, but typically efficient due to shared prefixes

### **Dry Run:**
- Input: `["apple", "app", "application"]`
- Insert:
  - Create nodes for 'a' → 'p' → 'p' → 'l' → 'e'
  - Mark last node of "apple" as `isWordEnd = true`
  - Reuse nodes for "app" and "application", mark their ends.
- Search "app":
  - Traverse 'a' → 'p' → 'p', confirm `isWordEnd = true`.
- Search "ap":
  - Path exists but `isWordEnd` is false, so not a word.

---

# Summary

| Approach | Data Structure | Insertion | Search | Prefix Search | Space Complexity | Time Complexity (per operation) |
|------------|----------------|--------------|---------|----------------|------------------|------------------------------|
| Brute Force | List of words | O(1) | O(N * L) | O(N * L) | O(N * L) | O(L) to check prefix/word |
| HashMap-based Trie | Nested HashMaps | O(L) | O(L) | O(L) | O(N * L) | O(L) |
| Array-based Trie | Fixed size array (26) | O(L) | O(L) | O(L) | O(N * L) | O(L) |

---

## **Key Takeaways:**
- **Brute-force** is simple but inefficient for large datasets.
- **HashMap-based Trie** improves search time at the cost of higher space.
- **Array-based Trie** offers the most efficient search with fixed alphabet size, suitable for lowercase English letters.

---

This comprehensive guide should help you understand the evolution from naive to optimal solutions for implementing a Trie, along with code snippets, complexity insights, and dry run examples for effective revision.
