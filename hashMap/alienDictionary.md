# Alien Dictionary

---

## Problem Overview

Given a list of words sorted lexicographically according to an alien language, determine the order of characters in that language. If no valid order exists or there's a cycle, return an empty string.

**Example:**
```
Input: ["wrt", "wrf", "er", "ett", "rftt"]
Output: "wertf"
```

---

## Approach 1: Brute Force / Direct Comparison (Not practical for large inputs)

### Core Idea
Attempt to deduce the character order by directly comparing adjacent words and extracting ordering constraints.

### Algorithm
1. Compare each pair of adjacent words.
2. For each pair, find the first differing character.
3. Deduce that the first differing character of the first word comes before the character in the second word.
4. Collect all such ordering constraints.
5. Topologically sort the characters based on these constraints.
6. Detect cycles; if a cycle exists, return an empty string.

**Note:** This approach is often a preliminary idea but not efficient for complex cases. It requires building a graph of constraints and topological sorting.

---

## Approach 2: Graph + Topological Sorting (Most Common and Efficient)

### Core Idea
Model characters as nodes in a directed graph. Edges represent the ordering constraints found by comparing adjacent words. Use topological sorting to derive the character order.

### Algorithm
1. **Initialize Data Structures:**
   - A graph represented by adjacency lists.
   - An in-degree map for each character.

2. **Build the Graph:**
   - For each adjacent pair of words:
     - Find the first different character.
     - Add an edge from the first differing character of the first word to that of the second.
     - Increment in-degree of the second character.

3. **Topological Sort:**
   - Use a queue to process characters with in-degree 0.
   - Repeatedly dequeue, append to result.
   - Reduce in-degree of neighboring characters.
   - If at the end, the length of the result matches the number of unique characters, return the result.
   - Else, there's a cycle; return an empty string.

### Java Code
```java
import java.util.*;

class Solution {
    public String alienOrder(String[] words) {
        Map<Character, Set<Character>> graph = new HashMap<>();
        Map<Character, Integer> inDegree = new HashMap<>();

        // Initialize graph and inDegree
        for (String word : words) {
            for (char c : word.toCharArray()) {
                graph.putIfAbsent(c, new HashSet<>());
                inDegree.putIfAbsent(c, 0);
            }
        }

        // Build the graph by comparing adjacent words
        for (int i = 0; i < words.length - 1; i++) {
            String first = words[i];
            String second = words[i + 1];
            int len = Math.min(first.length(), second.length());
            boolean foundDifference = false;

            for (int j = 0; j < len; j++) {
                char c1 = first.charAt(j);
                char c2 = second.charAt(j);
                if (c1 != c2) {
                    if (!graph.get(c1).contains(c2)) {
                        graph.get(c1).add(c2);
                        inDegree.put(c2, inDegree.get(c2) + 1);
                    }
                    foundDifference = true;
                    break;
                }
            }

            // Edge case: prefix situation like ["abc", "ab"]
            if (!foundDifference && first.length() > second.length()) {
                return "";
            }
        }

        // Topological sort
        Queue<Character> queue = new LinkedList<>();
        for (char c : inDegree.keySet()) {
            if (inDegree.get(c) == 0) {
                queue.offer(c);
            }
        }

        StringBuilder order = new StringBuilder();
        while (!queue.isEmpty()) {
            char current = queue.poll();
            order.append(current);
            for (char neighbor : graph.get(current)) {
                inDegree.put(neighbor, inDegree.get(neighbor) - 1);
                if (inDegree.get(neighbor) == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // Check if topological sort includes all characters
        if (order.length() != inDegree.size()) {
            return "";
        }
        return order.toString();
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  - Building the graph: `O(N * L)`, where `N` is the number of words, `L` is the maximum length of a word.
  - Topological sorting: `O(C + E)`, where `C` is the number of unique characters, and `E` is the number of edges.
  - Overall: **O(N * L + C + E)**, practically linear.

- **Space Complexity:**  
  - Graph and in-degree map: `O(C + E)`, where `C` is number of unique characters and `E` edges.

---

## Dry Run: Step-by-step Example

**Input:** `["wrt", "wrf", "er", "ett", "rftt"]`

### Step 1: Initialize Data Structures
- Characters: `w, r, t, f, e`
- Graph: empty adjacency lists
- In-degree: all zero initially

### Step 2: Build Graph by comparing adjacent words
- Compare `"wrt"` and `"wrf"`:
  - First different character: `'t'` vs `'f'`
  - Add edge: `'t' -> 'f'`
  - in-degree of `'f'` increases by 1
- Compare `"wrf"` and `"er"`:
  - First different character: `'w'` vs `'e'`
  - Add edge: `'w' -> 'e'`
  - in-degree of `'e'` increases by 1
- Compare `"er"` and `"ett"`:
  - First different character: `'r'` vs `'t'`
  - Add edge: `'r' -> 't'`
  - in-degree of `'t'` increases by 1
- Compare `"ett"` and `"rftt"`:
  - First different character: `'e'` vs `'r'`
  - Add edge: `'e' -> 'r'`
  - in-degree of `'r'` increases by 1

### Step 3: Topological Sort
- Initial zero in-degree characters: `'w'`, `'f'`, `'e'`
- Process `'w'`: append `'w'`, reduce in-degree of `'e'`: now zero
- Process `'f'`: append `'f'`
- Process `'e'`: append `'e'`, reduce in-degree of `'r'`: now zero
- Process `'r'`: append `'r'`, reduce in-degree of `'t'`: now zero
- Process `'t'`: append `'t'`, reduce in-degree of `'f'`? No, already processed.

**Result:** `"w" -> "e" -> "r" -> "t" -> "f"`  
*But note the actual order depends on the queue operations. The correct order is `"w", "e", "r", "t", "f"`.*

### Final output: `"wertf"`

---

## Summary

| Approach | Core Idea | Algorithm Highlights | Code Snippet | Time Complexity | Space Complexity |
|------------|------------|------------------------|--------------|-----------------|------------------|
| Brute Force | Direct comparison and constraints deduction | Compare adjacent words, find first difference, build constraints | Not practical for large datasets | `O(N^2 * L)` | `O(C + E)` |
| Graph + Topological Sort | Model characters as nodes, constraints as edges, topologically sort | Build graph from first different characters, detect cycles with topological sort | Provided above | `O(N * L + C + E)` | `O(C + E)` |

---

## Final Tips for Interview Preparation
- Understand how to build graphs and perform topological sorts.
- Practice detecting invalid cases such as cycles or prefix issues.
- Be familiar with topological sort algorithms (Kahn’s Algorithm, DFS-based).
- Handle edge cases like singleton words, prefix conditions, and invalid inputs.

---

This guide should help you understand the **Alien Dictionary** problem thoroughly and prepare for interviews effectively.
