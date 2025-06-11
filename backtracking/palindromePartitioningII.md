# Palindrome Partitioning II

This problem asks: Given a string `s`, partition `s` such that every substring of the partition is a palindrome. Return the **minimum number of cuts** needed to partition the string into palindromes.

---

## 1. Brute Force Approach

### Core Idea:
- Generate **all possible partitions** of the string.
- For each partition, check if all substrings are palindromes.
- Keep track of the **minimum cuts** needed.

### Algorithm:
1. Use recursion to generate all partitions:
   - Starting from index `start`, try all possible substrings `s[start..end]`.
   - If substring `s[start..end]` is a palindrome:
     - Recursively compute minimum cuts for the remaining substring `s[end+1..]`.
2. The base case:
   - When `start` reaches the end of the string, no more cuts are needed (return 0).
3. For each valid palindrome substring, compute the total cuts as:
   - `1 + min_cuts_for_remaining`

### Recursion Tree Diagram:
```
Start at index 0:
  - Check s[0..0], if palindrome:
    - Recurse from 1
  - Check s[0..1], if palindrome:
    - Recurse from 2
  - ...
At each level, branch for each palindrome substring starting at current index.

```

### Java Code:
```java
public class PalindromePartitioningII {
    public int minCut(String s) {
        return helper(s, 0);
    }

    private int helper(String s, int start) {
        if (start == s.length()) return 0;
        int minCuts = Integer.MAX_VALUE;
        for (int end = start; end < s.length(); end++) {
            if (isPalindrome(s, start, end)) {
                int cuts = 1 + helper(s, end + 1);
                minCuts = Math.min(minCuts, cuts);
            }
        }
        return minCuts - 1; // subtract 1 because last cut is unnecessary
    }

    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--))
                return false;
        }
        return true;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(2^n * n)**  
  - Exponential number of partitions (~2^n), each check palindrome takes O(n).
- **Space Complexity:** **O(n)**  
  - Recursion stack depth and palindrome check space.

---

## 2. Better Solution: Memoized DFS (Top-Down DP)

### Core Idea:
- Use **memoization** to avoid recomputing results for the same starting index.
- Store the minimum cuts needed from each starting point.

### Algorithm:
1. Maintain a memo array `dp[]` where `dp[start]` stores the minimum cuts needed from index `start`.
2. When computing for `start`, check if already computed.
3. For each substring `s[start..end]`, if palindrome:
   - Recursively compute remaining cuts from `end+1`.
   - Update `dp[start]` with the minimum.

### Recursion Tree:
- Similar to brute-force, but overlapping subproblems are cached.

### Java Code:
```java
public class PalindromePartitioningII {
    public int minCut(String s) {
        int n = s.length();
        Integer[] memo = new Integer[n];
        return helper(s, 0, memo) - 1; // subtract 1 because last cut is unnecessary
    }

    private int helper(String s, int start, Integer[] memo) {
        if (start == s.length()) return 0;
        if (memo[start] != null) return memo[start];

        int minCuts = Integer.MAX_VALUE;
        for (int end = start; end < s.length(); end++) {
            if (isPalindrome(s, start, end)) {
                int cuts = 1 + helper(s, end + 1, memo);
                minCuts = Math.min(minCuts, cuts);
            }
        }
        memo[start] = minCuts;
        return minCuts;
    }

    private boolean isPalindrome(String s, int left, int right) {
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--))
                return false;
        }
        return true;
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(n^3)**  
  - Each `isPalindrome` check is O(n), called for each substring, but with memoization, the number of recursive calls reduces.
- **Space Complexity:** **O(n)** for recursion and memo array.

---

## 3. Most Optimal Solution: Dynamic Programming (Bottom-Up)

### Core Idea:
- Precompute all palindromic substrings using a 2D boolean array.
- Use a 1D DP array to store minimum cuts up to each index.

### Algorithm:
1. **Precompute Palindromes:**
   - Create a 2D boolean array `isPal[i][j]` indicating whether `s[i..j]` is palindrome.
   - Fill this table in O(n^2).
2. **Compute Minimum Cuts:**
   - Use a 1D array `dp[]` where `dp[i]` is the minimum cuts for substring `s[0..i]`.
   - Initialize `dp[i]` with `i` (worst case: all singles).
   - For each index `i`, check all `j <= i`:
     - If `s[j..i]` is palindrome, then:
       - If `j == 0`, no more cuts needed before `i`.
       - Else, `dp[i] = min(dp[i], dp[j-1] + 1)`.

### Recursion Tree:
- Not recursive; iterative filling of DP tables.

### Java Code:
```java
public class PalindromePartitioningII {
    public int minCut(String s) {
        int n = s.length();
        boolean[][] isPal = new boolean[n][n];

        // Precompute palindromes
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i; j < n; j++) {
                if (s.charAt(i) == s.charAt(j) && (j - i < 2 || isPal[i + 1][j - 1])) {
                    isPal[i][j] = true;
                }
            }
        }

        int[] dp = new int[n];
        for (int i = 0; i < n; i++) {
            if (isPal[0][i]) {
                dp[i] = 0; // no cut needed if s[0..i] is palindrome
            } else {
                dp[i] = i; // maximum cuts
                for (int j = 1; j <= i; j++) {
                    if (isPal[j][i]) {
                        dp[i] = Math.min(dp[i], dp[j - 1] + 1);
                    }
                }
            }
        }
        return dp[n - 1];
    }
}
```

### Complexity Analysis:
- **Time Complexity:** **O(n^2)**  
  - Palindrome precomputation: O(n^2)
  - DP calculation: O(n^2)
- **Space Complexity:** **O(n^2)** for the palindrome table + O(n) for DP arrays.

---

## **Summary Table**

| Approach | Idea | Key Steps | Time | Space | Best for |
|------------|-------|------------|--------|--------|------------|
| Brute Force | Generate all partitions | Recursive exploration, palindrome check each time | O(2^n * n) | O(n) | Small strings, conceptual understanding |
| Memoized DFS | Cache overlapping results | Top-down DP with memoization | O(n^3) | O(n) | Medium strings, avoids recomputation |
| Bottom-Up DP | Precompute palindrome substrings & build solution iteratively | Precompute `isPal`, then compute `dp` | O(n^2) | O(n^2) | Large strings, most efficient |

---

## **Quick Tips for Interview Preparation**
- Always consider precomputing palindromes when dealing with substring palindrome problems.
- Use memoization to optimize recursive solutions.
- Recognize the pattern of breaking down problems into subproblems with DP.
- Think about the problem as a graph where nodes are string positions and edges are palindromic substrings; minimum cuts are shortest path.

---

This comprehensive guide offers you a structured understanding from brute-force to the most optimal DP solution for **Palindrome Partitioning II**. Practice with different string inputs to solidify intuition!
