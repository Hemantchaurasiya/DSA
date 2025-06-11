# Replace Words

---

## Problem Statement
Given a **sentence** and a **dictionary** of **root words**, replace **words** in the sentence with their **shortest root** from the dictionary if such a root exists. If multiple roots match, replace with the shortest one.

---

## Approach 1: Brute Force

### **Core Idea**
For each word in the sentence, check every root in the dictionary to see if it is a prefix of the word. If yes, replace the original word with this root.

### **Algorithm**
1. Split the sentence into individual words.
2. For each word:
   - Iterate over all roots in the dictionary.
   - Check if the root is a prefix of the current word.
   - If yes, replace the word with the root and break out of the loop.
3. Join the processed words back into a sentence.

### **Java Code**
```java
public String replaceWords(List<String> dictionary, String sentence) {
    String[] words = sentence.split(" ");
    for (int i = 0; i < words.length; i++) {
        for (String root : dictionary) {
            if (words[i].startsWith(root)) {
                words[i] = root;
                break; // Stop at the first matching root
            }
        }
    }
    return String.join(" ", words);
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - For each word (W), check each root (R).  
  - Checking prefix: O(L) where L is the length of the word.  
  - Total: **O(W * R * L)**, which can be costly for large inputs.
  
- **Space Complexity:**  
  - O(N) for storing the split words and the dictionary list (if not considering input storage).  
  - Additional O(1) for in-place replacements.

---

### **Dry Run Example**

**Input:**  
```plaintext
sentence = "the cattle was rattled by the battery"
dictionary = ["cat", "bat", "rat"]
```

| Step | Word          | Check roots | Root matched? | Replaced with | Explanation                                  |
|-------|--------------|--------------|--------------|--------------|----------------------------------------------|
| 1     | "the"        | "cat", "bat", "rat" | No | "the" | No prefix matches.                          |
| 2     | "cattle"     | "cat", "bat", "rat" | Yes ("cat") | "cat" | "cat" is prefix of "cattle".                |
| 3     | "was"        | "cat", "bat", "rat" | No | "was" | No prefix matches.                          |
| 4     | "rattled"    | "cat", "bat", "rat" | Yes ("rat") | "rat" | "rat" is prefix of "rattled".               |
| 5     | "by"         | "cat", "bat", "rat" | No | "by" | No prefix matches.                          |
| 6     | "the"        | Same as above | No | "the" | No prefix matches.                          |
| 7     | "battery"    | "cat", "bat", "rat" | Yes ("bat") | "bat" | "bat" is prefix of "battery".               |

**Final Output:**  
`"cat was rat by the bat"`

---

## Approach 2: Better Solution - Using Trie (Prefix Tree)

### **Core Idea**
Use a Trie data structure for efficient prefix matching. Insert all dictionary roots into the Trie. For each word in the sentence:
- Traverse the Trie character by character.
- If a node marks the end of a root, replace the original word with this root.
- If traversal ends without finding a root, keep the word as is.

### **Algorithm**
1. Build a Trie with all roots in the dictionary:
   - For each root, insert it into the Trie.
2. For each word in the sentence:
   - Traverse the Trie character by character.
   - If a node is the end of a root, replace the word with this root.
   - If no root found, keep the original word.
3. Join the words into a sentence.

### **Java Code**
```java
class TrieNode {
    TrieNode[] children = new TrieNode[26];
    boolean isEndOfWord = false;
}

public String replaceWords(List<String> dictionary, String sentence) {
    TrieNode root = new TrieNode();
    
    // Build Trie
    for (String word : dictionary) {
        TrieNode node = root;
        for (char c : word.toCharArray()) {
            int index = c - 'a';
            if (node.children[index] == null) {
                node.children[index] = new TrieNode();
            }
            node = node.children[index];
        }
        node.isEndOfWord = true;
    }
    
    String[] words = sentence.split(" ");
    for (int i = 0; i < words.length; i++) {
        StringBuilder replacement = new StringBuilder();
        TrieNode node = root;
        boolean replaced = false;
        for (char c : words[i].toCharArray()) {
            int index = c - 'a';
            if (node.children[index] == null || node.isEndOfWord) {
                break;
            }
            replacement.append(c);
            node = node.children[index];
            if (node.isEndOfWord) {
                words[i] = replacement.toString();
                replaced = true;
                break;
            }
        }
        // If no root found, keep the original word
    }
    return String.join(" ", words);
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  - Building Trie: O(R * L) where R is total roots and L is average root length.  
  - For each word in sentence: O(W * L) where W is number of words and L is max word length for traversal.  
  - Total: **O((R + W) * L)**.
  
- **Space Complexity:**  
  - O(R * L) for Trie storage.
  - O(W) for processing the sentence.

---

### **Dry Run Example**

**Input:**  
```plaintext
sentence = "the cattle was rattled by the battery"
dictionary = ["cat", "bat", "rat"]
```

- Build Trie with "cat", "bat", "rat".
- For each word, traverse Trie:
  - "the": no root prefix found → keep original.
  - "cattle": traverse 'c' → 'a' → 't' → root end, replace with "cat".
  - "was": no prefix → keep original.
  - "rattled": traverse 'r' → 'a' → 't' → root end, replace with "rat".
  - "by": no prefix → keep original.
  - "battery": traverse 'b' → 'a' → 't' → root end, replace with "bat".

**Final Output:**  
`"cat was rat by the bat"`

---

## Approach 3: Most Optimal - Trie with Early Termination and Priority Queue (if needed)

In most cases, the Trie approach is optimal for prefix matching, especially for large dictionaries and sentences, due to efficient prefix searches.

---

## Summary

| Approach                        | Data Structure | Key Point                                 | Time Complexity                         | Space Complexity                         |
|--------------------------------|----------------|-------------------------------------------|----------------------------------------|------------------------------------------|
| Brute Force                   | No special data structure | Check all roots for each word | O(W * R * L)                          | O(N) (for storage)                       |
| Trie (Prefix Tree)            | Trie            | Efficient prefix matching, early stop | O((R + W) * L)                        | O(R * L)                                |

---

## Final Tips
- Use **Trie** for large input sizes for efficiency.
- Optimize prefix checking by breaking early once a root is found.
- Always consider the problem constraints to choose the suitable approach.

---

**Happy Coding!**
