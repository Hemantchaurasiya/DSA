# Minimum Number of Flips to Make The Binary String Alternating

---

## Problem Overview
Given a binary string `s`, determine the **minimum number of flips** required to make the string **alternating** (i.e., no two adjacent characters are the same).  
**Note:** Flipping means changing '0' to '1' or '1' to '0'.

---

## Approach 1: Brute Force (Check Both Patterns)

### **Core Idea**
An alternating binary string can start with either `'0'` or `'1'`.  
- Generate both possible alternating patterns:
  - Pattern A: `'0'`, `'1'`, `'0'`, `'1'`, ...
  - Pattern B: `'1'`, `'0'`, `'1'`, `'0'`, ...
- Count how many flips are needed to convert the input string into each pattern.
- The minimum of these two counts will be the answer.

### **Algorithm**
1. Generate two pattern strings: one starting with `'0'` and other with `'1'`.
2. Initialize counters for flips needed for both patterns (`flipsA`, `flipsB`).
3. For each index `i` in `s`:
   - If `s[i]` doesn't match the character in pattern A, increment `flipsA`.
   - If `s[i]` doesn't match the character in pattern B, increment `flipsB`.
4. Return the minimum of `flipsA` and `flipsB`.

### **Java Code**
```java
public int minFlips(String s) {
    int flipsA = 0, flipsB = 0;
    int n = s.length();

    for (int i = 0; i < n; i++) {
        char expectedCharA = (i % 2 == 0) ? '0' : '1'; // Pattern starting with '0'
        char expectedCharB = (i % 2 == 0) ? '1' : '0'; // Pattern starting with '1'

        if (s.charAt(i) != expectedCharA) flipsA++;
        if (s.charAt(i) != expectedCharB) flipsB++;
    }
    return Math.min(flipsA, flipsB);
}
```

### **Complexity Analysis**
- **Time Complexity:** **O(n)**, as we scan the string once.
- **Space Complexity:** **O(1)**, only counters are used.

### **Dry Run**
- Input: `"1001"`

| i | s[i] | Pattern A ('0','1','0','1') | Pattern B ('1','0','1','0') | Flip for A | Flip for B |
|---|-------|------------------------------|------------------------------|------------|------------|
| 0 | 1     | 0                            | 1                            | 1 (matches B) | 0 (matches A) |
| 1 | 0     | 1                            | 0                            | 1          | 1          |
| 2 | 0     | 0                            | 1                            | 0          | 1          |
| 3 | 1     | 1                            | 0                            | 0          | 1          |

- Flips needed:
  - Pattern A: 2
  - Pattern B: 3
- **Answer:** 2 flips (for pattern A).

---

## Approach 2: Sliding Window (Optimized for Large Strings)

*Note:* Since the string is only checked against two fixed patterns, the first approach is already optimal for this problem. For large strings or streaming input, the same logic applies with a sliding window approach, but here the first approach is sufficient.

---

## Summary
| Method | Approach | Time Complexity | Space Complexity | Key Points |
|---------|--------------|-------------------|---------------------|--------------|
| 1 | Check Both Patterns | **O(n)** | **O(1)** | Efficiently compares input against both valid alternating patterns. |

---

## Final Tips for Interviews
- Recognize that alternating patterns only have two possibilities.
- Use pattern generation or logical calculation rather than brute-force flips.
- Think about edge cases: empty string, all same characters, etc.
- Practice similar pattern matching and string transformation problems.

---

**Happy Coding!**
