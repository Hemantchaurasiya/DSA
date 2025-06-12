# Sentence Similarity III

This problem involves determining whether two sentences (represented as arrays of words) are similar, considering the possibility of transitive similarity through a chain of similar words.

---

## 1. Brute Force Approach

### **Approach Name:**  
Direct Pairwise Comparison with Transitive Closure

### **Core Idea:**  
Two sentences are similar if, for every corresponding pair of words, either:
- They are the same, or
- There exists a chain of similarity linking them through other words.

In the brute-force method, we check all possible chains of similarity for each pair of words, which can be very inefficient.

### **Algorithm:**  
1. For each pair `(word1, word2)` at the same position:
   - If they are the same, continue.
   - Else, check if they are directly similar, or if there's a chain of similarity connecting them.
2. To do this, maintain a set or list of known similar pairs.
3. For each pair, perform a DFS or BFS to check if a chain exists linking the words.
4. If for any position, no chain exists, return `false`.
5. If all pairs are either same or connected via some chain, return `true`.

### **Java Code:**
```java
import java.util.*;

public class SentenceSimilarityIII {
    public boolean areSentencesSimilar(String[] sentence1, String[] sentence2, List<List<String>> similarPairs) {
        if (sentence1.length != sentence2.length) return false;

        // Build a graph for similarity
        Map<String, List<String>> graph = new HashMap<>();
        for (List<String> pair : similarPairs) {
            graph.computeIfAbsent(pair.get(0), k -> new ArrayList<>()).add(pair.get(1));
            graph.computeIfAbsent(pair.get(1), k -> new ArrayList<>()).add(pair.get(0));
        }

        // Check each pair
        for (int i = 0; i < sentence1.length; i++) {
            if (sentence1[i].equals(sentence2[i])) continue;
            if (!isConnected(graph, sentence1[i], sentence2[i], new HashSet<>())) {
                return false;
            }
        }
        return true;
    }

    private boolean isConnected(Map<String, List<String>> graph, String start, String end, Set<String> visited) {
        if (start.equals(end)) return true;
        visited.add(start);
        if (graph.containsKey(start)) {
            for (String neighbor : graph.get(start)) {
                if (!visited.contains(neighbor)) {
                    if (isConnected(graph, neighbor, end, visited)) return true;
                }
            }
        }
        return false;
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** *O(m * n)*, where `m` is the number of pairs and `n` is the number of words in the worst case, since each connectivity check can traverse the graph.
- **Space Complexity:** *O(m + n)* for storing the graph and visited set.

---

## 2. Slightly Optimized Approach: Union-Find (Disjoint Set Union)

### **Approach Name:**  
Union-Find (Disjoint Set Union - DSU)

### **Core Idea:**  
Use the Union-Find data structure to group all similar words into connected components. Two words are similar if they belong to the same component.

### **Algorithm:**  
1. Initialize a Union-Find structure.
2. For each pair `(word1, word2)` in `similarPairs`, perform union operation to connect their components.
3. For each position `i` in sentences:
   - Check if `sentence1[i]` and `sentence2[i]` are equal, or belong to the same component via find operation.
   - If neither, return `false`.
4. If all pairs pass the check, return `true`.

### **Java Code:**
```java
public class SentenceSimilarityIII {
    private Map<String, String> parent = new HashMap<>();

    public boolean areSentencesSimilar(String[] sentence1, String[] sentence2, List<List<String>> similarPairs) {
        if (sentence1.length != sentence2.length) return false;

        // Initialize Union-Find
        for (List<String> pair : similarPairs) {
            parent.putIfAbsent(pair.get(0), pair.get(0));
            parent.putIfAbsent(pair.get(1), pair.get(1));
        }

        // Union operation
        for (List<String> pair : similarPairs) {
            union(pair.get(0), pair.get(1));
        }

        for (int i = 0; i < sentence1.length; i++) {
            String word1 = sentence1[i], word2 = sentence2[i];
            if (word1.equals(word2)) continue;
            if (!connected(word1, word2)) return false;
        }
        return true;
    }

    private String find(String word) {
        parent.putIfAbsent(word, word);
        if (!parent.get(word).equals(word)) {
            parent.put(word, find(parent.get(word)));
        }
        return parent.get(word);
    }

    private void union(String a, String b) {
        String rootA = find(a);
        String rootB = find(b);
        if (!rootA.equals(rootB)) {
            parent.put(rootA, rootB);
        }
    }

    private boolean connected(String a, String b) {
        return find(a).equals(find(b));
    }
}
```

### **Complexity Analysis:**  
- **Time Complexity:** *O(m * α(n))* where `α(n)` is the inverse Ackermann function, nearly constant, for union-find operations.
- **Space Complexity:** *O(n)* for storing parent pointers.

---

## 3. Most Optimal Approach: Hashing and Union-Find + Path Compression

### **Approach Name:**  
Union-Find with Path Compression and Hashing

### **Core Idea:**  
Same as above, but with path compression in union-find for faster queries, making it highly efficient for large datasets.

### **Algorithm:**  
Same as the previous approach, but ensure path compression in `find()` for optimal performance.

### **Java Code:**  
Already shown in the previous step, as it includes path compression.

### **Complexity Analysis:**  
- **Time Complexity:** *O(m α(n))* (effectively constant per union/find).
- **Space Complexity:** *O(n)*.

---

## **Summary & Key Points:**

| Approach                              | Time Complexity                    | Space Complexity | Key Idea                                                      |
|---------------------------------------|-----------------------------------|------------------|----------------------------------------------------------------|
| Graph DFS/BFS                       | O(m * n)                         | O(m + n)        | Check connectivity via graph traversal for each pair.        |
| Union-Find (Disjoint Set Union)     | O(m α(n))                        | O(n)            | Group words into components, then compare component membership. |
| Union-Find with Path Compression     | Same as above, optimized further | O(n)            | Path compression for faster repeated queries.                |

---

## **Final Tips for Interview:**
- Use Union-Find for efficiency when dealing with many pairs.
- Preprocess the pairs into groups to handle transitive similarity efficiently.
- Focus on understanding how transitive relations can be represented as connected components.
- Remember to handle the case when the sentences differ in length upfront.

Mastering these approaches will help you handle sentence similarity problems efficiently in interviews!
