# Minimum Cost to Convert String I

---

## Problem Overview
Given two strings `s1` and `s2` of equal length, and a cost matrix `cost` where `cost[i][j]` is the cost of converting character `i` to character `j`, find the **minimum total cost** to convert `s1` into `s2`. You can also choose to convert characters in `s1` to some common character to minimize total cost.

---

## Approach 1: Brute Force (Naive)

### Core Idea
For each position, consider converting the current character in `s1` to the corresponding character in `s2` directly or converting both to some other character `k` that minimizes the total cost.

### Algorithm
1. For each position `i`:
   - Calculate the direct conversion cost: `cost[s1[i]][s2[i]]`.
   - For all possible characters `k`:
     - Compute the cost of converting `s1[i]` to `k` plus converting `k` to `s2[i]`.
   - Choose the minimum among these options.
2. Sum all minimum costs for each position.

### Java Code
```java
public class MinCostToConvertString {
    public int minCost(String s1, String s2, int[][] cost) {
        int totalCost = 0;
        int n = s1.length();

        for (int i = 0; i < n; i++) {
            int minCost = cost[s1.charAt(i) - 'a'][s2.charAt(i) - 'a']; // direct conversion
            for (int k = 0; k < 26; k++) {
                int currentCost = cost[s1.charAt(i) - 'a'][k] + cost[k][s2.charAt(i) - 'a'];
                minCost = Math.min(minCost, currentCost);
            }
            totalCost += minCost;
        }

        return totalCost;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  `O(n * 26)`, since for each position, we check up to 26 characters.
- **Space Complexity:**  
  `O(1)` (cost matrix is given and fixed size).

---

## Approach 2: Dynamic Programming with Preprocessing (Optimal)

### Core Idea
Since converting characters involves choosing a common character to minimize total cost, **precompute the minimal cost of converting any character to any other character**. This is similar to the Floyd-Warshall algorithm, which finds the shortest paths between all pairs in a weighted graph.

### Algorithm
1. Use Floyd-Warshall to compute `cost[i][j]` as the minimal cost of converting `i` to `j` using intermediate characters.
2. For each position `i` in the strings:
   - For each possible character `k`:
     - Calculate the cost of converting `s1[i]` to `k` plus `k` to `s2[i]`.
   - Choose the minimum over all `k`.
3. Sum these costs for all positions.

### Java Code
```java
public class MinCostToConvertStringOptimized {
    public int minCost(String s1, String s2, int[][] cost) {
        int n = s1.length();

        // Floyd-Warshall to find minimal conversion costs between all pairs
        for (int k = 0; k < 26; k++) {
            for (int i = 0; i < 26; i++) {
                for (int j = 0; j < 26; j++) {
                    if (cost[i][k] + cost[k][j] < cost[i][j]) {
                        cost[i][j] = cost[i][k] + cost[k][j];
                    }
                }
            }
        }

        int totalCost = 0;

        for (int i = 0; i < n; i++) {
            int s1Char = s1.charAt(i) - 'a';
            int s2Char = s2.charAt(i) - 'a';
            int minCost = Integer.MAX_VALUE;

            for (int k = 0; k < 26; k++) {
                int currentCost = cost[s1Char][k] + cost[k][s2Char];
                minCost = Math.min(minCost, currentCost);
            }
            totalCost += minCost;
        }

        return totalCost;
    }
}
```

### Complexity Analysis
- **Time Complexity:**  
  `O(26^3 + n * 26)`, dominated by Floyd-Warshall `O(26^3)` and the per-position check `O(n * 26)`.
- **Space Complexity:**  
  `O(26^2)` for the cost matrix.

---

## Approach 3: Most Optimal - Floyd-Warshall + Early Pruning (If applicable)

### Core Idea
Precompute all minimal conversions using Floyd-Warshall, then directly compute the total cost. If constraints are large, consider using memoization or pruning strategies.

### Algorithm
- Similar to Approach 2, with an emphasis on efficient implementation.
- Use the precomputed minimal costs for each position directly.

### Java Code
(Identical to Approach 2, emphasizing that this is the most optimal in the context of all-pairs shortest paths precomputation)

```java
// Same as Approach 2
```

### Complexity Analysis
- Same as Approach 2.

---

## Summary Table

| Approach | Core Idea | Time Complexity | Space Complexity | Notes |
|------------|--------------|-------------------|------------------|-------|
| Naive per-position | Check direct and intermediate conversions per position | `O(n * 26)` | `O(1)` | Simple but less scalable |
| Floyd-Warshall precompute | Precompute minimal conversion costs between all characters | `O(26^3 + n * 26)` | `O(26^2)` | Most efficient for large `n`, small character set |
| Early pruning / Memoization | Use precomputed data and prune unnecessary calculations | Same as Approach 2 | Same as Approach 2 | Best for large datasets with constraints |

---

## Final Tips for Interview
- Use Floyd-Warshall for small, fixed character sets (like lowercase English letters).
- Precompute minimal costs before processing strings for efficiency.
- Think about the problem as a shortest path problem in a weighted graph.
- Always validate with test cases involving edge cases, such as identical strings, empty strings, or maximum costs.

---

**Happy revising!**
