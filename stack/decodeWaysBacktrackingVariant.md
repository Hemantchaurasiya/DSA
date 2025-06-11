# Decode Ways

---

## Problem Overview
Given a string containing digits, determine the total number of ways to decode it into alphabetic characters, where `'A'` to `'Z'` are mapped to `'1'` to `'26'`.

**Example:**
- Input: `"226"`
- Output: `3`  
  Decodings: `"BZ"`, `"VF"`, `"BBF"`

---

## Approach 1: Brute Force (Recursive Backtracking)

### **Core Idea**
- Explore all possible ways to partition the string into valid 1- or 2-digit encodings.
- Recursively decode the string starting from index 0.
- Count all valid decoding paths.

### **Algorithm**
1. Start from index `i=0`.
2. At each position:
   - If the single digit at `i` is valid (`'1'` to `'9'`), recurse for the substring starting at `i+1`.
   - If the two digits at `i` and `i+1` form a valid number (`'10'` to `'26'`), recurse for the substring starting at `i+2`.
3. Base case: when `i` reaches the string's length, return `1`, indicating a valid decoding.
4. Sum all recursive paths to count total decodings.

### **Java Code**
```java
public class DecodeWays {
    public int numDecodings(String s) {
        return decodeRecursive(s, 0);
    }

    private int decodeRecursive(String s, int index) {
        if (index == s.length()) {
            return 1; // Reached end, valid decoding
        }
        if (s.charAt(index) == '0') {
            return 0; // No decoding possible starting with '0'
        }
        int ways = decodeRecursive(s, index + 1); // Single digit
        if (index + 1 < s.length()) {
            int twoDigit = Integer.parseInt(s.substring(index, index + 2));
            if (twoDigit >= 10 && twoDigit <= 26) {
                ways += decodeRecursive(s, index + 2); // Two digits
            }
        }
        return ways;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(2^n)* in the worst case, as each digit can branch into two choices (decode as single or double digit).
- **Space Complexity:** *O(n)* for the recursion call stack.

---

## Approach 2: Top-Down Memoization (Optimized Recursion)

### **Core Idea**
- Use memoization to cache results of subproblems to avoid recomputation.
- This transforms exponential recursion into linear complexity.

### **Algorithm**
1. Use a cache (e.g., array or HashMap) to store number of decodings from each index.
2. Modify the recursive function to check cache before computation.
3. Return cached result if available.
4. Continue with the same logic as brute-force, but with memoization.

### **Java Code**
```java
import java.util.HashMap;

public class DecodeWays {
    public int numDecodings(String s) {
        HashMap<Integer, Integer> memo = new HashMap<>();
        return decodeMemo(s, 0, memo);
    }

    private int decodeMemo(String s, int index, HashMap<Integer, Integer> memo) {
        if (index == s.length()) {
            return 1;
        }
        if (s.charAt(index) == '0') {
            return 0;
        }
        if (memo.containsKey(index)) {
            return memo.get(index);
        }
        int ways = decodeMemo(s, index + 1, memo);
        if (index + 1 < s.length()) {
            int twoDigit = Integer.parseInt(s.substring(index, index + 2));
            if (twoDigit >= 10 && twoDigit <= 26) {
                ways += decodeMemo(s, index + 2, memo);
            }
        }
        memo.put(index, ways);
        return ways;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, as each index is computed once.
- **Space Complexity:** *O(n)* for the memoization cache and recursion stack.

---

## Approach 3: Bottom-Up Dynamic Programming (Most Efficient)

### **Core Idea**
- Iteratively build the solution from the end of the string towards the beginning.
- Use a DP array where `dp[i]` represents the number of ways to decode substring starting at `i`.

### **Algorithm**
1. Initialize a DP array of size `n+1`, with `dp[n] = 1` (empty string has 1 decoding).
2. Traverse the string from `n-1` to `0`:
   - If `s[i] == '0'`, set `dp[i] = 0`.
   - Else, set `dp[i] = dp[i+1]`.
   - If `s[i..i+1]` forms a valid number (`10` to `26`), add `dp[i+2]` to `dp[i]`.
3. The answer is in `dp[0]`.

### **Java Code**
```java
public class DecodeWays {
    public int numDecodings(String s) {
        int n = s.length();
        int[] dp = new int[n + 1];
        dp[n] = 1; // Base case: empty string
        for (int i = n - 1; i >= 0; i--) {
            if (s.charAt(i) == '0') {
                dp[i] = 0; // No decoding starts with '0'
            } else {
                dp[i] = dp[i + 1]; // Single digit decoding
                if (i + 1 < n) {
                    int twoDigit = Integer.parseInt(s.substring(i, i + 2));
                    if (twoDigit >= 10 && twoDigit <= 26) {
                        dp[i] += dp[i + 2];
                    }
                }
            }
        }
        return dp[0];
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** *O(n)*, as we process each position once.
- **Space Complexity:** *O(n)* for the DP array (can be optimized to O(1) with variables).

---

## **Dry Run Example**

**Input:** `"226"`

| Index | String Substring | Action                                  | `dp[i]` | Explanation                         |
|--------|-------------------|-----------------------------------------|---------|-------------------------------------|
| 3      | ""                | Base case, `dp[3] = 1`                  | 1       | Empty string has 1 decoding        |
| 2      | "6"               | `'6' != '0'`, `dp[2] = dp[3] = 1`       | 1       | Single digit '6' decode            |
| 1      | "26"              | `'2' != '0'`, `dp[1] = dp[2] = 1`       | 1       | '2' alone, plus '26' (valid 10-26), add `dp[3]=1` | 
| 0      | "226"             | `'2' != '0'`, `dp[0] = dp[1] = 1`       | 1       | '2' alone, plus '22' (valid), add `dp[2]=1` = 2 |

Total ways: `dp[0] = 3` (decodings: `"BZ"`, `"VF"`, `"BBF"`).

---

## **Summary Table**

| Approach                          | Time Complexity | Space Complexity | Notes                                              |
|-----------------------------------|-----------------|------------------|---------------------------------------------------|
| Naive Recursion                   | O(2^n)         | O(n)             | Exponential, inefficient for large inputs        |
| Top-Down Memoization              | O(n)           | O(n)             | Efficient, avoids recomputation                  |
| Bottom-Up DP                      | O(n)           | O(n)             | Most optimal, iterative approach                |

---

## **Final Tips for Interviews**
- Recognize that the problem involves recursive decision-making with overlapping subproblems.
- Use memoization or bottom-up DP to optimize.
- Carefully handle `'0'` since it cannot decode alone.
- Think about the problem in terms of substrings and how to build solutions from smaller subproblems.
- Practice with various inputs, including strings with `'0'`s and multiple decoding options.

---

**Happy coding!**
