# Letter Tile Possibilities

---

## Problem Overview
Given a string `tiles`, representing letter tiles, return the **number of possible non-empty sequences** that can be formed using the tiles, where each tile can only be used once per sequence.

---

## Approach 1: Brute Force (Backtracking with Permutations and Subset Generation)

### Core Idea
Generate **all permutations** and **subsets** of the given tiles to count unique sequences. Handle duplicates by tracking used characters, ensuring no repeated sequences are counted multiple times.

### Algorithm
1. Use a **set** to store unique sequences.
2. Generate all possible sequences using **backtracking**:
    - For each unvisited character, add it to the current sequence.
    - Mark it as visited.
    - Recurse to extend the sequence.
    - Remove the character (backtrack) after exploring.
3. Count all stored unique sequences.

### Recursion Tree Diagram
```
Start
├── Choose 'A'
│   ├── Choose 'A' (second 'A' if exists)
│   │   └── ...
│   └── Choose 'B'
│       └── ...
├── Choose 'B'
│   └── ...
```
*(Each branch corresponds to choosing a tile, exploring further, and backtracking)*

### Java Code
```java
import java.util.*;

public class LetterTilePossibilities {
    public int numTilePossibilities(String tiles) {
        Set<String> result = new HashSet<>();
        boolean[] used = new boolean[tiles.length()];
        char[] chars = tiles.toCharArray();
        Arrays.sort(chars); // Sorting helps handle duplicates efficiently
        backtrack(chars, used, new StringBuilder(), result);
        return result.size();
    }

    private void backtrack(char[] chars, boolean[] used, StringBuilder path, Set<String> result) {
        for (int i = 0; i < chars.length; i++) {
            if (used[i]) continue;
            // Skip duplicates
            if (i > 0 && chars[i] == chars[i - 1] && !used[i - 1]) continue;

            used[i] = true;
            path.append(chars[i]);
            result.add(path.toString()); // Add current sequence
            backtrack(chars, used, path, result);
            path.deleteCharAt(path.length() - 1);
            used[i] = false;
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(N!)* in the worst case, considering all permutations, but practically less due to pruning and duplicate handling.
- **Space Complexity:** *O(N!)* for storing all sequences in the worst case; additionally, *O(N)* for recursion stack and used array.

### Dry Run (Sample Input: "AAB")
| Step | Current Path | Used array | Sequences Added | Comments |
|-------|----------------|--------------|-----------------|----------|
| Start | ""             | [false,false,false] | - | initial call |
| Choose 'A' at index 0 | "A" | [true,false,false] | "A" | first 'A' |
| Choose 'A' at index 1 | "AA" | [true,true,false] | "AA" | second 'A' |
| Choose 'B' at index 2 | "AAB" | [true,true,true] | "AAB" | complete sequence |
| Backtrack to "AA" | "AA" | [true,true,false] | | remove 'B' |
| Backtrack to "A" | "A" | [true,false,false] | | remove second 'A' |
| Choose 'B' at index 2 | "AB" | [true,false,true] | "AB" | new sequence |
| ... | ... | ... | ... | Continue similarly |

---

## Approach 2: Optimized Solution (Using Backtracking with Counting)

### Core Idea
Instead of generating all permutations explicitly, **use a frequency map** of characters. At each step, choose any remaining character, decrement its count, recurse, then backtrack by restoring the count. This reduces duplicate handling and avoids generating identical sequences multiple times.

### Algorithm
1. Count the frequency of each character.
2. Use a recursive function:
    - For each character with a non-zero count:
        - Choose the character, append it to the current sequence.
        - Decrement its count.
        - Recurse to extend sequences.
        - Backtrack (increment count back).
    - Count each step as contributing to the total sequences.
3. Sum all sequences generated during recursion.

### Recursion Tree Diagram
```
Start
├── Choose 'A' (count decreases)
│   ├── Choose 'A' (if count > 0)
│   │   └── ...
│   └── Choose 'B'
│       └── ...
├── Choose 'B'
│   └── ...
```

### Java Code
```java
import java.util.*;

public class LetterTilePossibilities {
    public int numTilePossibilities(String tiles) {
        Map<Character, Integer> countMap = new HashMap<>();
        for (char c : tiles.toCharArray()) {
            countMap.put(c, countMap.getOrDefault(c, 0) + 1);
        }
        return backtrack(countMap);
    }

    private int backtrack(Map<Character, Integer> countMap) {
        int total = 0;
        for (Map.Entry<Character, Integer> entry : countMap.entrySet()) {
            if (entry.getValue() > 0) {
                // Choose this character
                entry.setValue(entry.getValue() - 1);
                total += 1 + backtrack(countMap); // count 1 for current choice + recurse
                // Backtrack
                entry.setValue(entry.getValue() + 1);
            }
        }
        return total;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(N!)* in the worst case, but significantly pruned due to character count constraints.
- **Space Complexity:** *O(N)* for the recursion stack and character counts.

### Dry Run (Sample Input: "AAB")
| Step | Current Count Map | Sequences Count | Comments |
|-------|-------------------|------------------|----------|
| Initial | {A:2, B:1} | 0 | start |
| Choose 'A' | {A:1, B:1} | 1 ("A") | pick 'A' |
| Choose 'A' | {A:0, B:1} | 2 ("AA") | pick 'A' again |
| Choose 'B' | {A:0, B:0} | 3 ("AAB") | complete sequence |
| Backtrack to {A:1, B:1} | | | back to previous state |
| Choose 'B' | {A:1, B:0} | 4 ("AB") | pick 'B' |
| Backtrack | | | back to initial |
| Choose 'A' | {A:1, B:1} | (already counted above) | continue exploring |
| and so on... |

---

## Approach 3: Most Optimal (Mathematical/Combinatorial Approach) — Not typically straightforward for this problem but can be considered for large inputs

**Note:** This approach involves combinatorics to count permutations based on character frequencies without generating sequences explicitly, but it's more complex and less intuitive for interview prep.

---

## **Summary**

| Approach | Core Idea | Key Points | Time Complexity | Space Complexity |
|------------|--------------|--------------|-------------------|------------------|
| Brute Force | Generate all permutations and subsets, handle duplicates | Explicit backtracking, set to avoid duplicates | *O(N!)* | *O(N!)* + recursion stack |
| Optimized Counting | Use character frequency map, backtracking to generate sequences | Efficient duplicate handling, avoids explicit permutations | *O(N!)* but pruned | *O(N)* |

---

## Final Tips:
- Always handle duplicates by sorting or frequency maps.
- Use backtracking with pruning to reduce redundant work.
- Count sequences by summing choices at each step, rather than generating all explicitly when possible.
- Practice dry runs for understanding recursion flow.

---

**Good luck with your interview prep!**
