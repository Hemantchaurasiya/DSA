# Palindrome Partitioning

---

## Problem Statement
Given a string **s**, partition **s** such that **every substring** of the partition is a palindrome. Return **all possible palindrome partitionings**.

---

## Approach 1: Brute Force (Backtracking without Optimization)

### Core Idea:
Generate all possible partitions of the string, and for each partition, check if all substrings are palindromes. Collect all valid partitions.

---

### Algorithm:
1. Start from index 0.
2. For each position `i` from start to end:
   - Extract the substring `s[start:i+1]`.
   - Check if the substring is a palindrome.
   - If yes, recurse from index `i+1` with this substring added to the current partition.
3. When the start index reaches the length of the string, add the current partition to the results.

---

### Recursion Tree Diagram:

```
s = "aab"

Start at index 0:
- Partition "a" (palindrome)
  - Recurse from index 1:
    - Partition "a" (palindrome)
      - Recurse from index 2:
        - Partition "b" (palindrome)
          - Recurse from index 3 (end): add ["a", "a", "b"]
    - Partition "ab" (not palindrome) -> discard
- Partition "aa" (palindrome)
  - Recurse from index 2:
    - Partition "b" (palindrome)
      - Recurse from index 3 (end): add ["aa", "b"]
```

---

### Java Code:

```java
import java.util.*;

public class PalindromePartitioning {
    public List<List<String>> partition(String s) {
        List<List<String>> result = new ArrayList<>();
        backtrack(s, 0, new ArrayList<>(), result);
        return result;
    }
    
    private void backtrack(String s, int start, List<String> currentList, List<List<String>> result) {
        if (start == s.length()) {
            result.add(new ArrayList<>(currentList));
            return;
        }
        for (int i = start; i < s.length(); i++) {
            String sub = s.substring(start, i + 1);
            if (isPalindrome(sub)) {
                currentList.add(sub);
                backtrack(s, i + 1, currentList, result);
                currentList.remove(currentList.size() - 1);
            }
        }
    }
    
    private boolean isPalindrome(String s) {
        int left = 0, right = s.length() -1;
        while (left < right) {
            if (s.charAt(left) != s.charAt(right))
                return false;
            left++;
            right--;
        }
        return true;
    }
}
```

---

### Complexity Analysis:
- **Time Complexity:**  
O(N * 2^N)  
In the worst case, the number of partitions is exponential (2^N), and checking for palindrome takes O(N) for each substring.

- **Space Complexity:**  
O(N) due to recursion stack and auxiliary space for storing current partition.

---

### Dry Run: Example `"aab"`

| Step | Current String | Current Partition | Remaining String | Palindrome Check | Result |
|-------|----------------|---------------------|-------------------|------------------|---------|
| Start | "aab"          | []                  | "aab"             | "a" is palindrome | recurse |
|       |                | ["a"]               | "ab"              | "a" is palindrome | recurse |
|       |                | ["a", "a"]          | "b"               | "b" is palindrome | recurse |
|       |                | ["a", "a", "b"]     | ""                | -- end -- | add ["a", "a", "b"] |
| Back to previous | | | "b" | "b" palindrome | add ["a", "a", "b"] |
| Back to previous | | | "aab" | "ab" not palindrome | discard |
| Back to earlier | | | "a" | "aa" palindrome | recurse |
|       |                | ["aa"]              | "b"               | "b" palindrome | recurse |
|       |                | ["aa", "b"]         | ""                | -- end -- | add ["aa", "b"] |

---

## Approach 2: Memoization to Optimize Palindrome Checks

### Core Idea:
Avoid recomputing whether substrings are palindromes by caching results.

---

### Algorithm:
1. Use a 2D boolean array `dp` where `dp[i][j]` indicates whether `s.substring(i,j+1)` is a palindrome.
2. Fill the `dp` table beforehand.
3. Use backtracking similar to Approach 1, but refer to `dp[i][j]` to quickly check palindrome status.

---

### Java Code:

```java
public class PalindromePartitioning {
    public List<List<String>> partition(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        // Fill the DP table
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s.charAt(i) == s.charAt(j) && (j - i < 2 || dp[i + 1][j - 1])) {
                    dp[i][j] = true;
                }
            }
        }
        List<List<String>> result = new ArrayList<>();
        backtrack(s, 0, new ArrayList<>(), result, dp);
        return result;
    }
    
    private void backtrack(String s, int start, List<String> currentList, List<List<String>> result, boolean[][] dp) {
        if (start == s.length()) {
            result.add(new ArrayList<>(currentList));
            return;
        }
        for (int end = start; end < s.length(); end++) {
            if (dp[start][end]) {
                currentList.add(s.substring(start, end + 1));
                backtrack(s, end + 1, currentList, result, dp);
                currentList.remove(currentList.size() - 1);
            }
        }
    }
}
```

---

### Complexity Analysis:
- **Time Complexity:**  
O(N^2 + 2^N) due to palindrome precomputation and backtracking.
- **Space Complexity:**  
O(N^2) for the DP table and recursion stack.

---

## Approach 3: Dynamic Programming (Most Optimal)

### Core Idea:
Precompute palindromic substrings using DP, then use backtracking to generate solutions efficiently.

### Explanation:
- Precompute all palindrome substrings (O(N^2))
- Use backtracking to generate partitions, referencing the precomputed info for palindrome checks.

---

### Recap:
- Precompute `dp[i][j]` (whether `s[i..j]` is a palindrome).
- Generate all partitions via backtracking, leveraging `dp` for quick palindrome validation.

---

### Final Note:
- The most optimized approach combines precomputed palindrome info with backtracking.
- For large strings, this reduces redundant palindrome checks, improving overall performance.

---

## Summary Table

| Approach | Core Idea | Key Optimization | Time Complexity | Space Complexity |
|-----------|--------------|------------------|----------------|------------------|
| Brute Force | Generate all partitions; check palindromes on the fly | No | O(N * 2^N) | O(N) |
| Memoized Palindrome Check | Cache palindrome results | Yes | O(N^2 + 2^N) | O(N^2) |
| DP + Backtracking | Precompute all palindromes, then generate partitions | Yes | O(N^2 + 2^N) | O(N^2) |

---

This comprehensive guide covers various approaches for solving **Palindrome Partitioning**, emphasizing evolving from brute-force to optimized solutions suitable for interview scenarios.
