# Number of Equivalent Domino Pairs

---

## Problem Overview

Given an array of dominoes where each domino is represented as `[a, b]`, count the number of **pairs of dominoes** that are **equivalent**. Two dominoes `[a, b]` and `[c, d]` are considered **equivalent** if they contain the same numbers, regardless of order, i.e., `[a, b]` is equivalent to `[b, a]`.

**Example:**
```plaintext
Input: [[1,2], [2,1], [3,4], [1,2]]
Output: 3

Explanation:
- [1,2] and [2,1] are equivalent (forming 1 pair).
- [1,2] appears twice (forming 1 pair).
- Total pairs = 3.
```

---

## Approach 1: Brute Force

### **Core Idea**
Compare every pair of dominoes to check if they are equivalent.

### **Algorithm**
1. Initialize a counter for pairs to 0.
2. Loop through all domino pairs `(i, j)` with `i < j`.
3. For each pair:
   - Check if dominoes are equivalent (by ignoring order).
   - If yes, increment the counter.
4. Return the total count.

### **Java Code**
```java
public class Solution {
    public int numEquivDominoPairs(int[][] dominoes) {
        int count = 0;
        int n = dominoes.length;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (areEquivalent(dominoes[i], dominoes[j])) {
                    count++;
                }
            }
        }
        return count;
    }
    
    private boolean areEquivalent(int[] d1, int[] d2) {
        return (d1[0] == d2[0] && d1[1] == d2[1]) || (d1[0] == d2[1] && d1[1] == d2[0]);
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(N^2)` due to nested comparisons.
- **Space Complexity:**  
  `O(1)` additional space.

---

## Approach 2: Hash Map with Normalized Representation

### **Core Idea**
Use a hash map to count how many dominoes share the same normalized form (i.e., sorted `[a, b]`).

### **Algorithm**
1. For each domino, create a normalized form:
   - Sort the two numbers so that `[a, b]` always stored as `[min(a, b), max(a, b)]`.
2. Use a hash map to count the frequency of each normalized domino.
3. For each frequency `f`, the number of pairs contributed is `f * (f - 1) / 2`.
4. Sum all these pairs from the hash map to get the total.

### **Java Code**
```java
import java.util.*;

public class Solution {
    public int numEquivDominoPairs(int[][] dominoes) {
        Map<String, Integer> countMap = new HashMap<>();
        int result = 0;
        for (int[] domino : dominoes) {
            int a = domino[0], b = domino[1];
            // Normalize the domino
            String key = a <= b ? a + "," + b : b + "," + a;
            countMap.put(key, countMap.getOrDefault(key, 0) + 1);
        }
        for (int freq : countMap.values()) {
            result += freq * (freq - 1) / 2;
        }
        return result;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(N)` to process dominoes + `O(M)` to iterate over map, where `M` is number of unique domino forms, overall `O(N)`.
- **Space Complexity:**  
  `O(N)` for the hash map in the worst case (all dominoes are unique).

---

## Approach 3: Optimized Hashing with Integer Encoding

### **Core Idea**
Convert each domino into a unique integer key to optimize space and lookup.

### **Algorithm**
1. For each domino `[a, b]`, create a unique key:
   - Use `min(a, b) * 10 + max(a, b)` (assuming `a, b <= 9`).
2. Count frequencies in a hash map.
3. Calculate the number of pairs as before.

### **Java Code**
```java
import java.util.*;

public class Solution {
    public int numEquivDominoPairs(int[][] dominoes) {
        Map<Integer, Integer> countMap = new HashMap<>();
        int result = 0;
        for (int[] domino : dominoes) {
            int a = domino[0], b = domino[1];
            int key = a <= b ? a * 10 + b : b * 10 + a;
            countMap.put(key, countMap.getOrDefault(key, 0) + 1);
        }
        for (int freq : countMap.values()) {
            result += freq * (freq - 1) / 2;
        }
        return result;
    }
}
```

### **Complexity Analysis**
- **Time Complexity:**  
  `O(N)` for processing.
- **Space Complexity:**  
  `O(N)` in the worst case.

---

## Dry Run: Step-by-step Example

**Input:**
```plaintext
dominoes = [[1,2], [2,1], [3,4], [1,2]]
```

### Step 1: Normalization and Counting
- Domino `[1,2]` → normalized as `[1,2]` → key `"1,2"` → count: 1
- Domino `[2,1]` → normalized as `[1,2]` → key `"1,2"` → count: 2
- Domino `[3,4]` → normalized as `[3,4]` → key `"3,4"` → count: 1
- Domino `[1,2]` → normalized as `[1,2]` → key `"1,2"` → count: 3

### Step 2: Calculate pairs
- For `"1,2"` with count 3:
  - Pairs = `3 * (3 - 1) / 2 = 3`
- For `"3,4"` with count 1:
  - Pairs = 0

**Total pairs = 3**

---

## Summary Table

| Approach | Core Idea | Algorithm Highlights | Code Snippet | Time Complexity | Space Complexity |
|------------|------------|------------------------|--------------|-----------------|------------------|
| Brute Force | Compare all pairs | Nested loops, direct comparison | Provided above | `O(N^2)` | `O(1)` |
| Hash Map with Normalization | Count normalized dominoes | Use hash map to count and compute pairs | Provided above | `O(N)` | `O(N)` |
| Hash Map with Integer Encoding | Encode domino as unique key | Convert `[a,b]` to integer, count and compute pairs | Provided above | `O(N)` | `O(N)` |

---

## Final Tips
- Use normalization or encoding to optimize space and lookup.
- For large inputs, the hash map approach is most efficient.
- Remember that order doesn't matter; always normalize or encode to handle equivalence.
- Practice edge cases with duplicate dominoes, all identical, or all unique dominoes.

---

This guide provides a comprehensive overview of solving **Number of Equivalent Domino Pairs** efficiently and prepares you for coding interviews.
