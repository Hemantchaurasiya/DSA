# Word Break II

## Problem Overview
Given a string `s` and a dictionary of words `wordDict`, return **all possible sentences** where `s` is segmented into a sequence of dictionary words.

---

## Approach 1: Brute Force (Recursive Backtracking)

### Core Idea
Try all possible prefixes of `s`. If a prefix exists in the dictionary, recursively solve for the remaining suffix. Collect all valid sentences formed this way.

### Algorithm
1. Start from index 0.
2. For each index `i` from current position to end:
   - Check if `s[start:i+1]` is in `wordDict`.
   - If yes, recurse for the substring `s[i+1:]`.
   - Combine the current prefix with the sentences returned from recursion.
3. If the starting index reaches the end of the string, return an empty list (base case).

### Recursion Tree Diagram
```
Start
├── Prefix1 (valid word)
│   ├── Recurse for suffix
│   │   ├── Prefix2 (valid)
│   │   │   ├── Recurse ...
│   │   │   └── ...
│   └── ...
└── ...
```

### Java Code
```java
import java.util.*;

public class WordBreakII {
    public List<String> wordBreak(String s, List<String> wordDict) {
        return backtrack(s, new HashSet<>(wordDict), 0);
    }

    private List<String> backtrack(String s, Set<String> dict, int start) {
        List<String> result = new ArrayList<>();
        if (start == s.length()) {
            result.add("");
            return result;
        }

        for (int end = start + 1; end <= s.length(); end++) {
            String prefix = s.substring(start, end);
            if (dict.contains(prefix)) {
                List<String> sublist = backtrack(s, dict, end);
                for (String sub : sublist) {
                    result.add(prefix + (sub.isEmpty() ? "" : " " + sub));
                }
            }
        }
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *Exponential*, approximately O(2^n) in the worst case, as it explores all possible partitions.
- **Space Complexity:** *O(n)* for the recursion stack and result storage.

### Dry Run (Sample Input)
- Input: `s = "catsand"`  
- `wordDict = ["cats", "cat", "sand", "and", "s"]`

| Step | Current start index | Prefixes checked | Valid prefixes | Recursive calls | Result snippets collected |
|-------|---------------------|------------------|----------------|-------------------|---------------------------|
| 0     | 0                   | "c", "ca", "cat", ... | "cat" | recurse from index 3 | "cat sand" <br> "cats and" |
| 3     | 3                   | "s", "sa", "san", "sand" | "sand" | recurse from index 7 | "sand" (base) |
| 7     | 7                   | end of string | - | return [""] | - |
| 3     | 3                   | combine to: "sand" | | return ["sand"] | |
| 0     | 0                   | combine: "cat sand" | | return ["cat sand", "cats and"] |


---

## Approach 2: Memoized Recursion (Top-Down DP)

### Core Idea
Use memoization to cache the results of recursive calls starting from each index, avoiding repeated calculations.

### Algorithm
1. Maintain a `Map<Integer, List<String>> memo` to store results for each start index.
2. When a start index is visited:
   - If in memo, return stored results.
   - Else, perform recursive backtracking as in Approach 1.
3. Combine and store results in `memo`.

### Java Code
```java
import java.util.*;

public class WordBreakII {
    public List<String> wordBreak(String s, List<String> wordDict) {
        Map<Integer, List<String>> memo = new HashMap<>();
        Set<String> dict = new HashSet<>(wordDict);
        return backtrack(s, dict, 0, memo);
    }

    private List<String> backtrack(String s, Set<String> dict, int start, Map<Integer, List<String>> memo) {
        if (memo.containsKey(start)) {
            return memo.get(start);
        }

        List<String> result = new ArrayList<>();
        if (start == s.length()) {
            result.add("");
            return result;
        }

        for (int end = start + 1; end <= s.length(); end++) {
            String prefix = s.substring(start, end);
            if (dict.contains(prefix)) {
                List<String> sublist = backtrack(s, dict, end, memo);
                for (String sub : sublist) {
                    result.add(prefix + (sub.isEmpty() ? "" : " " + sub));
                }
            }
        }
        memo.put(start, result);
        return result;
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(n^3)* in worst case (due to substring operations and overlapping subproblems), significantly improved over brute force.
- **Space Complexity:** *O(n)* for memoization and recursion stack.

### Dry Run
Same as above, but now results for each start index are cached, avoiding repeated exploration.

---

## Approach 3: Dynamic Programming + Memoization (Bottom-Up + Top-Down Hybrid)

### Core Idea
Use DP to determine if a substring can be segmented, combined with memoization to generate all solutions efficiently.

### Algorithm
1. Create a boolean array `dp[]` where `dp[i]` indicates if `s[0..i]` can be segmented.
2. Use a list `result[]` where `result[i]` holds all sentences that can be formed from `s[0..i]`.
3. Fill `dp[]` and `result[]` by iterating through `s`:
   - For each position, check all previous positions where `dp[j]` is true.
   - If `s[j..i]` is in `wordDict`, append new sentences to `result[i]`.
4. The answer is `result[s.length()]`.

### Java Code
```java
import java.util.*;

public class WordBreakII {
    public List<String> wordBreak(String s, List<String> wordDict) {
        Set<String> dict = new HashSet<>(wordDict);
        int n = s.length();
        List<List<String>> dp = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) {
            dp.add(new ArrayList<>());
        }
        dp.get(0).add("");
        
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                String word = s.substring(j, i);
                if (dp.get(j).size() > 0 && dict.contains(word)) {
                    for (String prevSentence : dp.get(j)) {
                        String sentence = prevSentence.isEmpty() ? word : prevSentence + " " + word;
                        dp.get(i).add(sentence);
                    }
                }
            }
        }
        return dp.get(n);
    }
}
```

### Complexity Analysis
- **Time Complexity:** *O(n^3)*, due to substring operations within nested loops.
- **Space Complexity:** *O(n^2)* for storing all sentences at each position.

### Dry Run
- For `s = "catsand"` and `wordDict = ["cats", "cat", "sand", "and", "s"]`, build `dp[]` step-by-step, accumulating possible sentences at each index.

---

## Summary
| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
|---|---|---|---|---|
| Brute Force | Recursive exploration of all partitions | Exponential (O(2^n)) | O(n) | Simple but inefficient for large inputs |
| Memoized Recursion | Cache results to avoid recomputation | O(n^3) | O(n) | Significantly faster, avoids duplicate work |
| DP + Memoization | Bottom-up building of solutions | O(n^3) | O(n^2) | Efficient for large input and ensures all solutions |

---

## Final Tips for Interviews:
- Start with the recursive backtracking to demonstrate understanding.
- Use memoization to optimize overlapping subproblems.
- Recognize the importance of caching results for efficiency.
- Be clear about the base cases and how concatenation builds sentences.
- Understand the trade-offs between approaches.

Good luck with your preparation!
