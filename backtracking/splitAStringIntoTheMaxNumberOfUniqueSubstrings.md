# Split a String Into the Max Number of Unique Substrings

---

## Problem Statement
Given a string `s`, partition `s` into **disjoint substrings** such that:
- All substrings are **unique**.
- The total number of substrings is maximized.

Return the **maximum number of such substrings**.

---

## Approach 1: Brute Force (Backtracking with Set)

### Core Idea
Explore **all possible partitions** of the string, ensuring **no duplicates** in the current partition, and keep track of the maximum number of substrings found.

### Algorithm
1. Use recursion to try all possible splits starting from the current index.
2. Maintain a `Set` to track the current substrings to ensure uniqueness.
3. For each position, try to extend the current substring and:
   - If it is not in the set, add it and recurse for the remaining string.
   - Backtrack after exploring each option.
4. Keep a global or class variable to track the maximum count.

### Recursion Tree Diagram
```
Start at index 0:
    - Take s[0:1], recurse
        - Take s[1:2], recurse
            - Take s[2:3], recurse ...
        - Take s[1:3], recurse ...
    - Take s[0:2], recurse
        - ...
    - Take s[0:3], recurse ...
```

### Java Code
```java
public class Solution {
    int maxCount = 0;

    public int maxUniqueSplit(String s) {
        backtrack(s, 0, new HashSet<>());
        return maxCount;
    }

    private void backtrack(String s, int start, Set<String> seen) {
        if (start == s.length()) {
            maxCount = Math.max(maxCount, seen.size());
            return;
        }
        for (int end = start + 1; end <= s.length(); end++) {
            String current = s.substring(start, end);
            if (!seen.contains(current)) {
                seen.add(current);
                backtrack(s, end, seen);
                seen.remove(current);
            }
        }
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(2^n * n)  
  - In the worst case, each position can either be a cut or not, leading to exponential combinations.  
  - Substring operations are O(n) in worst case, but often optimized by substring sharing in Java.
- **Space Complexity:** O(n)  
  - Due to recursive call stack and set storage.

### Dry Run with Example
- Input: `"abac"`  
- Step-by-step:
  - Start at index 0:
    - Take `"a"`, recurse from index 1 with `{"a"}`
      - Take `"b"`, recurse from index 2 with `{"a","b"}`
        - Take `"a"`, recurse from index 3 with `{"a","b"}` (since `"a"` in set, skip)
        - Take `"ac"` (not possible, since index 3),
      - Take `"ba"` (from index 1 to 3), recurse with `{"a","ba"}` and so on...
  - Explore all combinations, updating `maxCount`.

---

## Approach 2: Backtracking with Pruning (Optimized Search)

### Core Idea
Improve the brute-force approach by **pruning** when the current substring already exists, reducing unnecessary recursive calls.

### Algorithm
Similar to Approach 1, but:
- Before recursive call, check if the current substring is already in set.
- If yes, skip further exploration for that branch.

### Java Code
Same as above, but emphasizing pruning:
```java
// Same as previous code, pruning is inherent
```

### Complexity Analysis
- **Time Complexity:** Still exponential in worst case, but pruning reduces the search space.
- **Space Complexity:** O(n) for recursion and set.

---

## Approach 3: **Bitmask + DFS (Most Optimal for Small Strings)**

### Core Idea
Use bitmasking to represent used substrings (if the input length is small), enabling faster checks and space-efficient storage.

### Algorithm
- Assign each substring a unique index (if feasible).
- Use an integer as bitmask to represent which substrings are used.
- DFS to explore all possible splits, updating the bitmask.

*Note:* Due to the problem's nature, this approach is more suitable when the string length is small (e.g., n <= 20).

### Java Code
This is more conceptual; actual implementation depends on constraints.

```java
// For small strings, using bitmasking can be efficient
public class Solution {
    private int maxCount = 0;

    public int maxUniqueSplit(String s) {
        dfs(s, 0, 0);
        return maxCount;
    }

    private void dfs(String s, int start, int usedMask) {
        if (start == s.length()) {
            maxCount = Math.max(maxCount, Integer.bitCount(usedMask));
            return;
        }
        for (int end = start + 1; end <= s.length(); end++) {
            String current = s.substring(start, end);
            int hash = current.hashCode(); // For small input, or precompute indices
            // Use a map to assign each substring an index
            // For simplicity, check if substring already used
            // (Implementation details omitted for brevity)
        }
    }
}
```

---

## Summary Table

| Approach | Idea | Key Points | Time Complexity | Space Complexity |
|------------|-------|--------------|-------------------|------------------|
| **Brute Force** | Explore all possible partitions | Recursive backtracking, tries all splits | O(2^n * n) | O(n) |
| **Backtracking with Pruning** | Skip duplicate substrings early | Slightly optimized, reduces recursion | Still exponential, but faster in practice | O(n) |
| **Bitmask + DFS** | Use bitmask for used substrings | Efficient for small n, fast checks | O(2^n) for small strings | O(n) |

---

## Final Tips for Interview
- Understand that the core challenge is to **explore all partitions** while avoiding duplicates.
- Use **backtracking** as the primary technique.
- For optimization, incorporate **pruning** or **bitmasking** depending on input constraints.
- Always analyze the **trade-offs** between complexity and implementation complexity.
- Remember to handle **edge cases**: empty string, all characters same, etc.

---

**Happy Revising!**
