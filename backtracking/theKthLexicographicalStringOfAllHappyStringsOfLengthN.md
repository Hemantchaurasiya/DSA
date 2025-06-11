# The k-th Lexicographical String of All Happy Strings of Length n

---

## Problem Overview

Given integers `n` and `k`, find the **k-th lexicographically smallest happy string** of length `n`.  
A **happy string** is a string of characters `'a'`, `'b'`, `'c'` where **no two consecutive characters are the same**.

---

## Approach 1: Brute Force Enumeration

### **Core Idea**
Generate **all** happy strings of length `n` in lexicographical order, store them, and then pick the `k-th`.  
This approach is straightforward but inefficient for large `n` or `k`.

### **Algorithm**
1. Use **backtracking** to generate all happy strings:
    - Start with an empty string.
    - For each position, append `'a'`, `'b'`, or `'c'` (if it doesn't match the previous character).
    - Recursively generate all valid strings.
2. Collect all generated strings in a list.
3. Sort the list lexicographically (though generation order can be maintained lex order).
4. Return the `(k-1)`th string if it exists.

### **Recursion Tree Diagram**
```
Start
│
├─ 'a'
│  ├─ 'b'
│  │  ├─ 'a'
│  │  ├─ 'c'
│  ├─ 'c'
│     ├─ 'a'
│     ├─ 'b'
├─ 'b'
│  ├─ 'a'
│  │  ├─ 'b'
│  │  ├─ 'c'
│  ├─ 'c'
│     ├─ 'a'
│     ├─ 'b'
├─ 'c'
   ├─ 'a'
   ├─ 'b'
```

### **Java Code**

```java
import java.util.ArrayList;
import java.util.List;

public class HappyStringKth {
    List<String> allStrings = new ArrayList<>();

    public String getHappyString(int n, int k) {
        backtrack("", n);
        if (k > allStrings.size()) {
            return "";
        }
        return allStrings.get(k - 1);
    }

    private void backtrack(String current, int n) {
        if (current.length() == n) {
            allStrings.add(current);
            return;
        }
        for (char c : new char[]{'a', 'b', 'c'}) {
            if (current.isEmpty() || current.charAt(current.length() - 1) != c) {
                backtrack(current + c, n);
            }
        }
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(3^n)*, since in worst case, each position can have up to 3 choices, and all valid strings are generated.
- **Space Complexity:** *O(3^n)* for storing all strings, plus recursion stack *O(n)*.

### **Dry Run (Sample Input):** `n=2, k=3`
- Generate all happy strings of length 2:
  - "ab", "ac", "ba", "bc", "ca", "cb"
- Sorted lex order: "ab", "ac", "ba", "bc", "ca", "cb"
- Output: "ba" (3rd string)

---

## Approach 2: Mathematical / Combinatorial Counting (Optimized)

### **Core Idea**
Instead of generating all strings, **count how many happy strings** start with each character at each position, then **directly compute** the k-th string by navigating through the lex order using these counts.

This approach leverages **precomputed counts** and **divide-and-conquer** logic to jump directly to the desired string.

### **Algorithm**
1. **Precompute counts** of happy strings of length `n` starting with each character `'a'`, `'b'`, `'c'`.
2. **Iterate position-by-position**:
   - For the current position, check the lex order of `'a'`, `'b'`, `'c'`.
   - For each character, **compute how many strings** start with that character.
   - Use `k` to determine which character to pick:
     - If `k` is less than or equal to the number of strings starting with `'a'`, pick `'a'`.
     - Else subtract that count from `k`, move to `'b'`, and so on.
3. **Recursively** or iteratively proceed to the next position, updating counts and `k`.
4. Continue until the string of length `n` is built.

### **Key Point**
- Use **dynamic programming** to precompute counts for subproblems.
- The counts help decide the character at each position without enumerating all strings.

### **Java Code**
```java
public class HappyStringKth {
    private static final int MOD = 1000000007;
    
    public String getHappyString(int n, int k) {
        long[][] count = new long[n + 1][3]; // counts[length][charIndex]
        // Initialize for length=1
        for (int i = 0; i < 3; i++) {
            count[1][i] = 1;
        }
        // Precompute counts for lengths > 1
        for (int len = 2; len <= n; len++) {
            for (int c = 0; c < 3; c++) {
                for (int prev = 0; prev < 3; prev++) {
                    if (c != prev) {
                        count[len][c] += count[len - 1][prev];
                    }
                }
            }
        }

        // Total number of happy strings of length n
        long total = 0;
        for (long c : count[n]) total += c;
        if (k > total) return "";

        StringBuilder result = new StringBuilder();
        int prevCharIdx = -1; // no previous character
        for (int i = 0; i < n; i++) {
            long sum = 0;
            for (int c = 0; c < 3; c++) {
                if (c != prevCharIdx) {
                    long cnt = count[n - i][c];
                    if (sum + cnt >= k) {
                        result.append((char) ('a' + c));
                        prevCharIdx = c;
                        break;
                    } else {
                        sum += cnt;
                    }
                }
            }
            k -= sum; // move to next level
        }
        return result.toString();
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, since we compute counts once and then build the string character by character.
- **Space Complexity:** *O(n)* for the DP table `count`.

---

## Approach 3: Recursive DFS with Pruning (Most Optimal)

### **Core Idea**
Use **recursive DFS with counting** to directly **navigate the lexicographical space**, pruning branches that don't contain the k-th string.

### **Algorithm**
1. **Precompute counts** similar to approach 2.
2. **Recursive function**:
   - At each position, iterate over `'a'`, `'b'`, `'c'` in lex order.
   - For each candidate character:
     - Check how many strings start with this character.
     - If `k` is greater than this count, subtract and move to the next character.
     - Else, choose this character, recurse for the next position with updated `k`.
3. Terminate when the string is built.

### **Advantages**
- Avoids generating all strings.
- Efficient for large `n` and `k`.

### **Java Code**

```java
public class HappyStringKth {
    private long[][] count;
    private int n;
    private int k;
    private StringBuilder result = new StringBuilder();

    public String getHappyString(int n, int k) {
        this.n = n;
        this.k = k;
        count = new long[n + 1][3];
        // Initialize counts for length=1
        for (int i = 0; i < 3; i++) {
            count[1][i] = 1;
        }
        // Precompute counts
        for (int len = 2; len <= n; len++) {
            for (int c = 0; c < 3; c++) {
                for (int prev = 0; prev < 3; prev++) {
                    if (c != prev) {
                        count[len][c] += count[len - 1][prev];
                    }
                }
            }
        }

        // Build string recursively
        dfs(0, -1);
        return result.length() == n ? result.toString() : "";
    }

    private boolean dfs(int index, int prevChar) {
        if (index == n) return true;
        for (int c = 0; c < 3; c++) {
            if (c != prevChar) {
                long cnt = count[n - index][c];
                if (cnt >= k) {
                    result.append((char) ('a' + c));
                    if (dfs(index + 1, c)) {
                        return true;
                    }
                    result.deleteCharAt(result.length() - 1);
                } else {
                    k -= cnt;
                }
            }
        }
        return false;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, since each position is processed once.
- **Space Complexity:** *O(n)* for the recursive call stack and DP table.

---

## Summary of Approaches

| Approach | Methodology | Pros | Cons | Time Complexity | Space Complexity |
|------------|--------------|-------|-------|-----------------|------------------|
| Brute Force | Generate all strings, sort, pick k | Simple to implement | Inefficient for large n/k | O(3^n) | O(3^n) |
| Counting (Iterative) | Precompute counts, navigate lex order | Efficient, direct access | Requires DP setup | O(n) | O(n) |
| Recursive DFS with Counting | Similar to approach 2, recursive traversal | Most optimized, minimal overhead | Slightly complex implementation | O(n) | O(n) |

---

## Final Tips
- For large `n` and `k`, always prefer counting + direct navigation to avoid TLE.
- Precompute counts for subproblems to facilitate quick decision-making.
- Use lex order to prune unnecessary branches and jump directly to the target string.

---

This revision note provides a structured overview from naive to optimal solutions, including key ideas, algorithms, code snippets, and complexity insights for quick revision and interview prep.
