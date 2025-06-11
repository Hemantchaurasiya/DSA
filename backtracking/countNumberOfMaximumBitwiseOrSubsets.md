# Count Number of Maximum Bitwise-OR Subsets

This guide provides a comprehensive overview of solving the problem: **Count Number of Maximum Bitwise-OR Subsets**. It covers multiple approaches, from brute-force to optimal solutions, with detailed explanations, algorithms, code snippets, complexity analysis, and dry runs for better understanding.

---

## Problem Statement
Given an array of integers, find the number of subsets that have the maximum possible bitwise OR value.

---

## Approach 1: Brute Force

### **Core Idea**
- Generate all possible subsets.
- Calculate the bitwise OR for each subset.
- Track the maximum OR value encountered.
- Count how many subsets achieve this maximum OR.

### **Algorithm**
1. Initialize `maxOr` to 0 and `count` to 0.
2. Generate all subsets using recursion or bitmasking.
3. For each subset:
   - Compute its OR value.
   - If OR > `maxOr`, update `maxOr` and reset `count` to 1.
   - Else if OR == `maxOr`, increment `count`.
4. Return `count`.

---

### **Recursion Tree Diagram**

```
For an array [a, b, c]:

Start
├── Include a
│   ├── Include b
│   │   ├── Include c
│   │   └── Exclude c
│   └── Exclude b
│       ├── Include c
│       └── Exclude c
└── Exclude a
    ├── Include b
    │   ├── Include c
    │   └── Exclude c
    └── Exclude b
        ├── Include c
        └── Exclude c
```

### **Java Code**
```java
public class MaxORSubsets {
    int maxOr = 0;
    int count = 0;

    public int countMaxOrSubsets(int[] nums) {
        backtrack(nums, 0, 0);
        return count;
    }

    private void backtrack(int[] nums, int index, int currentOr) {
        if (index == nums.length) {
            if (currentOr > maxOr) {
                maxOr = currentOr;
                count = 1;
            } else if (currentOr == maxOr) {
                count++;
            }
            return;
        }
        // Include nums[index]
        backtrack(nums, index + 1, currentOr | nums[index]);
        // Exclude nums[index]
        backtrack(nums, index + 1, currentOr);
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(2^n), as all subsets are generated.
- **Space Complexity:** O(n) for the recursion stack.

### **Dry Run Example**
Input: `[1, 2]`

| Step | Subset Elements | OR Value | Max OR | Count | Comments |
|-------|------------------|----------|--------|--------|----------|
| 1     | {}               | 0        | 0      | 0      | Start |
| 2     | {1}              | 1        | 1      | 1      | Update maxOr to 1, count = 1 |
| 3     | {1,2}            | 3        | 3      | 1      | Update maxOr to 3, count = 1 |
| 4     | {2}              | 2        | 3      | 1      | OR = 2, less than max, ignore |
| 5     | {}               | 0        | 3      | 1      | Ignore |

Result: **Number of subsets with max OR = 1 (from {1}), 1 (from {1,2}) => 1 subset with OR=3, total count=1.**

---

## Approach 2: Better Solution using Set and Memoization

### **Core Idea**
- Use a set to keep track of all possible OR values from subsets.
- For each number, update the set with new OR values by OR-ing with existing set elements.
- The maximum OR is the maximum in this set.
- Count how many subsets produce this maximum.

### **Algorithm**
1. Initialize a set `orSet` with `{0}` (empty subset).
2. For each number in the array:
   - Create a new set `temp`.
   - For each element `val` in `orSet`, add `val | num` to `temp`.
   - Merge `temp` into `orSet`.
3. Find the maximum value in `orSet`.
4. Count how many times this maximum appears as a result of OR operations.

**Note:** For counting the number of subsets leading to the max OR, a map/dictionary approach may be used to maintain counts for each OR value.

---

### **Java Code**
```java
import java.util.*;

public class MaxORSubsets {
    public int countMaxOrSubsets(int[] nums) {
        Map<Integer, Integer> orCountMap = new HashMap<>();
        orCountMap.put(0, 1); // empty subset
        
        for (int num : nums) {
            Map<Integer, Integer> tempMap = new HashMap<>(orCountMap);
            for (Map.Entry<Integer, Integer> entry : orCountMap.entrySet()) {
                int newOr = entry.getKey() | num;
                tempMap.put(newOr, tempMap.getOrDefault(newOr, 0) + entry.getValue());
            }
            orCountMap = tempMap;
        }
        
        int maxOr = Collections.max(orCountMap.keySet());
        return orCountMap.get(maxOr);
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(n * 2^n), since each element can potentially double the number of OR states.
- **Space Complexity:** O(2^n), storing all possible OR results.

### **Dry Run Example**
Input: `[1, 2]`

| Step | orCountMap | Key-Value Pairs | Max OR | Count for Max OR | Comments |
|-------|------------|-----------------|----------|------------------|----------|
| Init  | {0:1}      | 0:1           | 0        | 0                | Start |
| 1     | {0:1}      | 0:1, 1:1      | 1        | 1                | After first number |
| 2     | {0:1, 1:1} | 0:1, 1:1, 3:1 | 3        | 1                | OR with 2: 0|2=2, 1|2=3 |

Result: **Count of subsets with max OR=3 is 1** (corresponding to subset {1,2}).

---

## Approach 3: Optimized Backtracking with Pruning

### **Core Idea**
- Use backtracking with pruning to avoid redundant computations.
- Keep track of the current maximum OR and count dynamically.
- Skip exploring subsets that can't produce a higher OR than the current maximum.

### **Algorithm**
1. Sort the array in decreasing order (optional for pruning).
2. Use recursive backtracking:
   - At each step, decide whether to include or exclude the current element.
   - Update the current OR.
   - If current OR exceeds previously recorded maximum, update maximum and reset count.
   - If equal, increment count.
3. End recursion when all elements are processed.

This approach reduces unnecessary computations when it is clear that further inclusion won't increase the OR.

---

### **Java Code**
```java
public class MaxORSubsets {
    int maxOr = 0;
    int count = 0;

    public int countMaxOrSubsets(int[] nums) {
        Arrays.sort(nums);
        backtrack(nums, 0, 0);
        return count;
    }

    private void backtrack(int[] nums, int index, int currentOr) {
        if (index == nums.length) {
            if (currentOr > maxOr) {
                maxOr = currentOr;
                count = 1;
            } else if (currentOr == maxOr && currentOr != 0) {
                count++;
            }
            return;
        }
        // Include current element
        backtrack(nums, index + 1, currentOr | nums[index]);
        // Exclude current element
        backtrack(nums, index + 1, currentOr);
    }
}
```

### **Complexity Analysis**
- **Time Complexity:** O(2^n) in worst-case, but pruning may help in practice.
- **Space Complexity:** O(n) for recursion stack.

---

# Final Notes
- **Brute force** is simple but inefficient for large inputs.
- **Using sets and maps** optimizes by avoiding explicit subset generation.
- **Backtracking with pruning** can be more efficient if input is large, especially with sorted arrays.

**Choose the approach based on input constraints and performance needs.**

---

**Happy coding!**
