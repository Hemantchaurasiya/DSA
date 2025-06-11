# Sum of All Subsets XOR Total

---

## Problem Statement
Given an integer array `nums`, find the sum of the XOR of all possible subsets of `nums`.  
**Note:** The empty subset's XOR is 0 and should be included in the total sum.

---

## Approach 1: Brute Force

### Core Idea
Generate all possible subsets of the array, compute the XOR of each subset, and sum these XOR values.  

### Algorithm
1. Use recursion or bit manipulation to generate all subsets.
2. For each subset, compute the XOR value.
3. Accumulate the XOR values into a total sum.
4. Return the total sum after processing all subsets.

---

### Recursion Tree Diagram
For array `[a, b]`, the recursion tree looks like:

```
Start with empty subset: sum = 0
|
|-- Include a: XOR = a
|    |
|    |-- Include b: XOR = a ^ b
|    |
|    |-- Exclude b: XOR = a
|
|-- Exclude a:
     |
     |-- Include b: XOR = b
     |
     |-- Exclude b: XOR = 0
```

### Java Code
```java
public class SumOfSubsetsXOR {
    int totalSum = 0;

    public int subsetXORSum(int[] nums) {
        generateSubsets(nums, 0, 0);
        return totalSum;
    }

    private void generateSubsets(int[] nums, int index, int currentXOR) {
        if (index == nums.length) {
            totalSum += currentXOR;
            return;
        }
        // Include nums[index]
        generateSubsets(nums, index + 1, currentXOR ^ nums[index]);
        // Exclude nums[index]
        generateSubsets(nums, index + 1, currentXOR);
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(2^n), since all subsets are generated.
- **Space Complexity:** O(n), due to recursion stack depth.

---

### Dry Run: Example `[1, 2]`
| Step | Current Index | Current XOR | Action | Total Sum |
|-------|----------------|--------------|---------|------------|
| 0     | 0              | 0            | Start   | 0          |
| 1     | 1              | 0 ^ 1 = 1    | Include 1 | 0        |
| 2     | 2              | 1 ^ 2 = 3    | Include 2 | 3        |
|       |                |              | Backtrack | Total sum += 3 |
| 2     | 2              | 1            | Exclude 2 | 1        |
|       |                |              | Backtrack | Total sum += 1 |
| 1     | 1              | 0            | Exclude 1 | 0        |
| 2     | 2              | 0 ^ 2 = 2    | Include 2 | 2        |
|       |                |              | Backtrack | Total sum += 2 |

Total sum = 3 + 1 + 2 = **6**.

---

## Approach 2: Mathematical / Bitwise Insight (Most Optimal)

### Core Idea
Observe that **each element's contribution to the XOR sum across all subsets is multiplicative**.  
In particular, because XOR is symmetric and the subsets are independent, every element appears in exactly half of the subsets, and the total sum can be calculated based on the properties of XOR.

### Key Insight
- For each element `num`, it appears in `2^{n-1}` subsets.
- The XOR of all subsets can be computed directly using the property that **if any element appears in an even number of subsets, its contribution cancels out; otherwise, it contributes to the total sum**.

**But more straightforwardly:**

In the context of XOR sums over all subsets:

- The sum of XORs over all subsets is **`(sum of all elements) * 2^{n-1}`**.

**Why?**  
Because each element appears in exactly half of the total subsets, and XOR sums are additive over subsets in this specific case. This is a known property for such problems.

---

### Algorithm
1. Calculate **sum of all elements** in `nums`.
2. Calculate **`2^{n-1}`**.
3. Multiply the sum of elements by `2^{n-1}` to get the total.

### Java Code
```java
public class SumOfSubsetsXOR {
    public int subsetXORSum(int[] nums) {
        int sum = 0;
        for (int num : nums) {
            sum += num;
        }
        int n = nums.length;
        int totalSubsetsHalf = 1 << (n - 1); // 2^{n-1}
        return sum * totalSubsetsHalf;
    }
}
```

### Complexity Analysis
- **Time Complexity:** O(n), as we sum all elements once.
- **Space Complexity:** O(1), no extra space used aside from variables.

---

### Dry Run: Example `[1, 2]`
- Sum of elements = 1 + 2 = 3
- Number of elements n = 2
- Total subsets = 2^2 = 4
- Half of subsets = 2^{2-1} = 2
- Total XOR sum = 3 * 2 = **6**

Matches the brute-force result.

---

## **Summary**

| Approach | Core Idea | Algorithm Summary | Time Complexity | Space Complexity | When to Use |
|------------|--------------|-------------------------|---------------------|---------------------|--------------|
| **Brute Force** | Generate all subsets, compute XOR, sum | Recursively generate subsets, accumulate XOR | O(2^n) | O(n) | Small n, when simplicity is preferred |
| **Mathematical / Bitwise** | Use properties of subset inclusion | Sum all elements, multiply by 2^{n-1} | O(n) | O(1) | Large n, performance critical |

---

## **Final Notes**
- For large constraints, always prefer the mathematical solution for efficiency.
- Understanding properties of subset generation and XOR behavior can lead to elegant solutions.
- Practice both approaches to be comfortable with brute-force and optimized solutions.

---

Happy Revising!
