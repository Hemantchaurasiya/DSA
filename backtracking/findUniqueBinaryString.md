# Find Unique Binary String

---

## Problem Overview:
Given an array `nums` of binary strings where **each string is of equal length**, find a binary string **that is not present** in the array. The goal is to identify **any such string** efficiently.

---

## Approach 1: Brute Force

### Core Idea:
Generate all possible binary strings of the given length and check if they exist in the array. The first string not found in the array is our answer.

### Algorithm:
1. Determine the length `n` of the binary strings.
2. Generate all binary strings of length `n` (from `0` to `2^n - 1`).
3. For each generated string:
   - Check if it exists in `nums`.
   - If not, return this string.

### Recursion Tree:
- Not typically visualized for brute-force as it involves generating all combinations.

### Java Code:
```java
public class Solution {
    public String findDifferentBinaryString(String[] nums) {
        int n = nums.length;
        Set<String> set = new HashSet<>();
        for (String s : nums) {
            set.add(s);
        }
        for (int i = 0; i < (1 << n); i++) {
            String candidate = intToBinaryString(i, n);
            if (!set.contains(candidate)) {
                return candidate;
            }
        }
        return "";
    }
    
    private String intToBinaryString(int num, int length) {
        StringBuilder sb = new StringBuilder();
        for (int i = length - 1; i >= 0; i--) {
            sb.append(((num >> i) & 1) == 1 ? '1' : '0');
        }
        return sb.toString();
    }
}
```

### Complexity Analysis:
- **Time Complexity:**  
  *O(2^n * n)* due to generating all binary strings and checking in the set.  
- **Space Complexity:**  
  *O(n)* for the set and string construction.

### Dry Run:
| Input | nums = ["00", "01"] | n=2 |
|---------|---------------------|-----|
| Generate binary strings | "00", "01", "10", "11" | |
| Check "00" in set | Yes | skip |
| Check "01" in set | Yes | skip |
| Check "10" in set | No | return "10" |

---

## Approach 2: **Most Optimal Solution (Greedy with Bit Manipulation)**

### Core Idea:
Construct a binary string by choosing each bit to differ from the corresponding bit in the string at the same index in the array, ensuring the constructed string is **not in the array**.

**Key Insight:**  
If we process each string in `nums` and flip the bit at the current index, we can guarantee a string not in `nums`.

### Algorithm:
1. Initialize an empty string builder `res`.
2. Iterate through each string in `nums`:
   - For each position `i`:
     - If the current string's `i-th` bit is `'0'`, append `'1'` to `res`.
     - Else, append `'0'`.
3. Return the constructed string `res`.

This approach guarantees that the resulting string differs from every string in `nums` at **least** one position, ensuring uniqueness.

### Recursion Tree Diagram:
- Not applicable here; the process is iterative.

### Java Code:
```java
public class Solution {
    public String findDifferentBinaryString(String[] nums) {
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < nums.length; i++) {
            if (nums[i].charAt(i) == '0') {
                result.append('1');
            } else {
                result.append('0');
            }
        }
        return result.toString();
    }
}
```

### Complexity Analysis:
- **Time Complexity:**  
  *O(n)*, where `n` is the length of the array (and string length), since we process each string once.
- **Space Complexity:**  
  *O(n)* for constructing the output string.

### Dry Run:
| Input | nums = ["01", "10"] | n=2 |
|--------|---------------------|-----|
| i=0 | nums[0][0] = '0' | append '1' -> "1" |
| i=1 | nums[1][1] = '0' | append '1' -> "11" |
| Output | "11" | |
| Check | "11" ≠ "01" and "11" ≠ "10" | confirmed |

---

## Summary:
| Approach | Key Idea | Time Complexity | Space Complexity | Suitability |
|------------|------------|-------------------|-------------------|--------------|
| Brute Force | Generate all strings; check existence | O(2^n * n) | O(n) | Small `n` due to exponential growth |
| Greedy (Optimal) | Construct a string by flipping bits at each position | O(n) | O(n) | Efficient for large `n` |

---

## Final Recommendation:
For practical purposes, **the greedy approach** is preferred due to its linear time complexity and simplicity.

---

**Happy Coding!**
