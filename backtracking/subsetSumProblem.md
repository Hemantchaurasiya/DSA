# Subset Sum Problem

The **Subset Sum Problem** asks: Given a set of integers and a target sum, determine if there exists a subset of the given set whose sum is equal to the target.

---

## 1. Approach: Brute Force

### Core Idea:
Check all possible subsets and see if any sums up to the target.

### Algorithm:
- Generate all subsets of the given set.
- For each subset, calculate the sum.
- If any subset sum equals the target, return true.
- Else, return false.

### Recursion Tree:
```
At each element, decide to include or exclude:
                [index=0, current_sum=0]
               /                        \
       include arr[0]                exclude arr[0]
       /       \                       /        \
  [index=1, sum] [index=1, sum+arr[0]] ... (continue until end)
```

### Java Code:
```java
public class SubsetSum {
    public static boolean isSubsetSum(int[] arr, int n, int sum) {
        if (sum == 0) return true;
        if (n == 0) return false;
        if (arr[n - 1] > sum)
            return isSubsetSum(arr, n - 1, sum);
        return isSubsetSum(arr, n - 1, sum) ||
               isSubsetSum(arr, n - 1, sum - arr[n - 1]);
    }

    public static void main(String[] args) {
        int[] arr = {3, 34, 4, 12, 5, 2};
        int sum = 9;
        System.out.println(isSubsetSum(arr, arr.length, sum)); // true
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(2^n)  
  Because all subsets are checked (each element has 2 choices).
- **Space Complexity:** O(n)  
  Due to recursion stack in worst case.

### Dry Run:
Input: arr = [3, 34, 4, 12, 5, 2], sum = 9

| Step | n | current_sum | Consider arr[n-1] | Result | Explanation                     |
|-------|---|--------------|-------------------|---------|---------------------------------|
| 1     | 6 | 0            | 2                 | ?       | Include/exclude 2             |
| 2     | 5 | 0            | 5                 | ?       | Include/exclude 5             |
| ...   | ... | ...        | ...               | ...     | Continue exploring subsets    |

---

## 2. Approach: Dynamic Programming (Top-Down - Memoization)

### Core Idea:
Avoid recomputation by storing results of subproblems (state: index, current sum).

### Algorithm:
- Use a 2D boolean array `dp[n+1][sum+1]`.
- `dp[i][j]` indicates whether subset of first `i` elements can sum up to `j`.
- Initialize `dp[0][0] = true` (empty set sums to zero).
- Fill the table iteratively:
  - For each element, for each sum, set `dp[i][j]` based on previous results.

### Java Code:
```java
public class SubsetSumDP {
    public static boolean isSubsetSum(int[] arr, int sum) {
        int n = arr.length;
        boolean[][] dp = new boolean[n + 1][sum + 1];

        // Initialize: sum=0 can always be achieved with empty subset
        for (int i = 0; i <= n; i++) {
            dp[i][0] = true;
        }

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= sum; j++) {
                if (arr[i - 1] > j) {
                    dp[i][j] = dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - arr[i - 1]];
                }
            }
        }
        return dp[n][sum];
    }

    public static void main(String[] args) {
        int[] arr = {3, 34, 4, 12, 5, 2};
        int sum = 9;
        System.out.println(isSubsetSum(arr, sum)); // true
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(n * sum)  
  Because we fill an `n+1` by `sum+1` table.
- **Space Complexity:** O(n * sum)  
  For the DP table.

### Dry Run:
Input: arr = [3, 34, 4, 12, 5, 2], sum = 9

| i | j | dp[i][j] | Explanation                                              |
|---|---|----------|----------------------------------------------------------|
| 0 | 0 | true     | Empty set sums to 0                                   |
| 1 | 3 | true     | Using 3, sum=3 achievable                                |
| 2 | 4 | false    | No subset with first 2 elements sums to 4             |
| 3 | 4 | true     | 3 + 4 = 7, but 4 alone? No, but 4 alone is true at j=4 |
| 4 | 9 | true     | 3 + 4 + 2 = 9, so true                                  |
| ... | ... | ... | Continue filling table based on previous states |

---

## 3. Approach: Space-Optimized Dynamic Programming

### Core Idea:
Use a 1D array instead of a 2D table to reduce space.

### Algorithm:
- Initialize a boolean array `dp[sum+1]`.
- `dp[j]` indicates whether sum `j` can be formed.
- Iterate over each element:
  - For each sum from `sum` down to element, update `dp[j] = dp[j] || dp[j - element]`.

### Java Code:
```java
public class SubsetSumSpaceOptimized {
    public static boolean isSubsetSum(int[] arr, int sum) {
        boolean[] dp = new boolean[sum + 1];
        dp[0] = true; // Sum zero is always possible

        for (int num : arr) {
            for (int j = sum; j >= num; j--) {
                if (dp[j - num]) {
                    dp[j] = true;
                }
            }
        }
        return dp[sum];
    }

    public static void main(String[] args) {
        int[] arr = {3, 34, 4, 12, 5, 2};
        int sum = 9;
        System.out.println(isSubsetSum(arr, sum)); // true
    }
}
```

### Complexity Analysis:
- **Time Complexity:** O(n * sum)  
- **Space Complexity:** O(sum)  
  Because only a 1D array is used.

---

## **Summary Table**

| Approach                     | Time Complexity       | Space Complexity      | Use case                                         |
|------------------------------|------------------------|------------------------|--------------------------------------------------|
| Brute Force                  | O(2^n)               | O(n) (recursion stack) | Small input sizes, conceptual understanding     |
| DP (2D Table)                | O(n * sum)           | O(n * sum)             | Larger input, when sum is manageable            |
| Space-Optimized DP          | O(n * sum)           | O(sum)               | Large input, memory constraints                  |

---

## **Final Tips for Interviews:**
- Start with the brute-force approach to demonstrate understanding.
- Then optimize with DP for efficiency.
- Use space-optimized DP when dealing with large sums.
- Clearly explain your thought process, especially the intuition behind DP states.
- Always analyze time and space complexities.

---

This comprehensive guide provides both conceptual clarity and practical implementations to master the Subset Sum Problem efficiently!
